# rembg Comprehensive Usage Guide

This guide consolidates the current recommendations from the official [rembg](https://github.com/danielgatis/rembg) project, the underlying U<sup>2</sup>-Net / IS-Net model documentation, and ONNX Runtime guidance. It targets a Minimal Arch Linux setup with DWM, but the workflows apply to any Linux distribution.

## Installation & Environment Setup
1. Install the recommended system packages (Arch Linux):
   ```bash
   sudo pacman -S --needed python python-pip python-virtualenv python-onnxruntime
   ```
2. Create an isolated environment and install rembg via pip (the method endorsed by the maintainers):
   ```bash
   python -m venv ~/.venvs/rembg
   source ~/.venvs/rembg/bin/activate
   pip install --upgrade pip wheel
   # Base CLI (CPU inference)
   pip install rembg
   # Optional: install one or both extras if required
   # pip install "rembg[gpu]"   # Installs onnxruntime-gpu and CUDA deps
   # pip install "rembg[sam]"   # Installs Segment Anything dependencies
   ```
3. (Optional) Shell helpers:
   - Bash/Zsh  
     ```bash
     echo "alias rembg-env='source ~/.venvs/rembg/bin/activate'" >> ~/.zshrc
     ```
   - Fish  
     ```fish
     mkdir -p ~/.config/fish/functions
     printf "function rembg-env\n  source ~/.venvs/rembg/bin/activate.fish\nend\n" > ~/.config/fish/functions/rembg-env.fish
     ```
4. Verify the CLI:
   ```bash
   rembg --help
   rembg version
   ```
   The first run downloads model weights to `~/.u2net/`; keep that folder cached if you work offline.

## CLI Subcommands
`rembg` exposes a small set of subcommands; each corresponds to a use case documented by the project.

| Command | Purpose | Typical Usage |
|---------|---------|---------------|
| `rembg i <in> <out>` | Process one file (fast I/O). | `rembg i photo.jpg cutout.png` |
| `rembg p <in> <out>` | Process one file while preserving metadata (EXIF, ICC). | `rembg p product.tif product-cutout.png` |
| `rembg b` | Stream binary input to stdout (useful for pipelines). | `cat shot.png \| rembg b > shot-cut.png` |
| `rembg s` | Start the official HTTP server (REST API). | `rembg s --host 0.0.0.0 --port 7000` |

> Tip: The server endpoint accepts `POST /` with a multipart `file` field and returns a PNG with alpha.

## Key CLI Arguments
The options below are taken directly from `rembg --help` (v2.x). Combine them as needed for quality and performance tuning.

| Flag | Description | Recommended Scenarios |
|------|-------------|-----------------------|
| `-m, --model` | Select model (`u2net`, `u2netp`, `u2net_human_seg`, `u2net_cloth_seg`, `silueta`, `isnet-general-use`, `isnet-anime`, `sam`). | Choose based on subject matter (see model table). |
| `-s, --session` | Choose runtime backend (`cpu`, `cuda`, `tensorrt`, `openvino`). | Match to hardware acceleration you have configured. |
| `-a, --alpha-matting` | Enable alpha matting refinement. | Glass, hair, fur, translucent packaging. |
| `--alpha-matting-foreground-threshold <int>` | Foreground threshold (0–255, default 240). | Lower for dark subjects; higher for white products. |
| `--alpha-matting-background-threshold <int>` | Background threshold (0–255, default 10). | Raise when the background is bright or reflective. |
| `--alpha-matting-erosion-size <int>` | Erode mask before matting (pixels). | 5–15px for fine edges; 0 to skip erosion. |
| `--alpha-matting-base-size <int>` | Target long-edge resize before matting. | Keep at 1024 (default) unless working with huge TIFFs. |
| `--background R,G,B` | Replace transparency with a solid background. | `--background 255,255,255` for white e-commerce PNGs. |
| `--trim {fg,bg}` | Auto-crop extra foreground/background space. | `--trim fg` to remove margins around cutout. |
| `--post-process-mask` | Apply morphological smoothing to the mask. | Removes jagged edges; combine with matting. |
| `--output-mask <path>` | Save the matte as a grayscale PNG. | Hand off to GIMP/Krita for manual refinement. |
| `--scale <float>` | Downscale before processing (0–1). | `--scale 0.5` for previews on low-power devices. |
| `--device-id <int>` | Select GPU index when using CUDA/TensorRT. | Multi-GPU workstations. |

## Model Catalog & Hardware Recommendations
| Model | Summary (per official docs) | Quality vs Speed | Optimal Hardware |
|-------|-----------------------------|------------------|------------------|
| `u2net` | Default U<sup>2</sup>-Net (176 MB). Balanced for general-purpose subjects. | High quality, moderate speed. | Any modern CPU (quad-core+). GPU optional. |
| `u2netp` | Lightweight U<sup>2</sup>-Net (4.8 MB). For constrained systems. | Lower quality, fastest CPU inference. | Low-power CPUs, ARM SBCs. |
| `u2net_human_seg` | Human-focused segmentation. | High quality on people, less on objects. | CPU ok; GPU speeds up batches. |
| `u2net_cloth_seg` | Apparel segmentation (front/back clothing). | Tuned for garments; slower than `u2netp`. | CPU with >8 GB RAM or GPU. |
| `silueta` | Fast silhouette extractor; good for simple backgrounds. | Medium quality, fast. | Mobile/embedded CPUs. |
| `isnet-general-use` | IS-Net general model (360 MB). Improved edges and transparency handling. | Highest quality, heavier. | Dedicated GPU (CUDA/OpenVINO) or high-end CPU with >16 GB RAM. |
| `isnet-anime` | IS-Net variant trained on anime/style art. | High quality on illustrations. | CPU ok; GPU recommended for large batches. |
| `sam` | Meta’s Segment Anything Model integration. Requires extra dependencies. | Very high quality with prompts; slowest. | Strong GPU (≥8 GB VRAM). CPU-only inference is impractically slow. |

### Choosing Models by Subject
- Building materials, product packs: `isnet-general-use` (best edges) or `u2net` (faster fallback).
- Portraits & lifestyle shots: `u2net_human_seg` or `isnet-general-use`.
- Fashion flat-lays: `u2net_cloth_seg`.
- Anime, vector art, icons: `isnet-anime`.
- Batch background removal for simple catalog images: `u2netp` or `silueta`.

## Workflow Recipes
- **Single Product Image (best quality)**  
  ```bash
  rembg p --model isnet-general-use --alpha-matting \
    --post-process-mask originals/steel-beam.tif cutouts/steel-beam.png
  ```
- **Directory Batch (fast CPU mode)**  
  ```bash
  mkdir -p cutouts
  find originals -type f -iname '*.png' -print0 \
    | xargs -0 -I{} rembg i --model u2net "{}" "cutouts/$(basename "{}").png"
  ```
- **Keep Mask for Manual Finishing**  
  ```bash
  rembg p --model isnet-general-use \
    --output-mask masks/pipe-mask.png \
    originals/pipe.tif cutouts/pipe.png
  ```
- **HTTP Microservice**  
  ```bash
  rembg s --host 0.0.0.0 --port 7000 --model isnet-general-use
  # Sample POST
  curl -X POST -F file=@sample.png http://localhost:7000/ -o cutout.png
  ```
- **Python API (as documented upstream)**  
  ```python
  from rembg import remove
  from pathlib import Path

  data = Path("originals/brick-pallet.png").read_bytes()
  output = remove(data, model_name="isnet-general-use")
  Path("cutouts/brick-pallet.png").write_bytes(output)
  ```

## Hardware Acceleration & Sessions
- **CPU (default)**: Works everywhere; install `python-onnxruntime`. Good for moderate workloads.
- **CUDA**: Install the NVIDIA driver, CUDA toolkit, and `pip install onnxruntime-gpu`. Run with `--session cuda`. Expect 4–8× speedup on `isnet-*` models.
- **TensorRT**: For NVIDIA cards with TensorRT set up; run `--session tensorrt`. Highest throughput for batch servers.
- **OpenVINO**: Best on Intel CPUs/iGPUs. `pip install openvino-dev[onnx]` and run `--session openvino`.
- **DirectML / CoreML**: Supported by ONNX Runtime but not officially documented in rembg; use only if you confirm compatibility upstream.

Set `--device-id` when multiple GPUs are present. For servers, pin the `U2NET_HOME` environment variable to a shared model cache location to avoid re-download per user.

## Troubleshooting Checklist
- **`ModuleNotFoundError: onnxruntime`**: Ensure you installed either `python-onnxruntime` (system) or `onnxruntime-gpu` / `openvino` inside the virtualenv.
- **Slow first inference**: Run a quick sample (`rembg i sample.png sample-cut.png`) to cache model weights before production use.
- **Memory exhaustion on huge TIFFs**: Downscale a copy for previews (`magick image.tif -resize 50% preview.png`) or increase swap. `isnet-*` models need ~2 GB RAM per 4k image.
- **Server timeout**: Increase Gunicorn/NGINX proxy timeouts if fronting the rembg server. The `sam` model may take 10–20 s per image even on GPU.
- **Color shifts**: Always export source files as PNG/TIFF with embedded ICC profiles; `rembg p` preserves them.

## Further Reading
- rembg repository: https://github.com/danielgatis/rembg  
- Model weights & descriptions: https://github.com/danielgatis/rembg#models  
- ONNX Runtime Execution Providers: https://onnxruntime.ai/docs/execution-providers/  
- U<sup>2</sup>-Net paper: https://arxiv.org/abs/2005.09007  
- IS-Net paper & resources: https://github.com/xuebinqin/ISNet  
- Segment Anything Model: https://github.com/facebookresearch/segment-anything
