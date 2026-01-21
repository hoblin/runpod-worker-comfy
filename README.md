# runpod-worker-comfy

[ComfyUI](https://github.com/comfyanonymous/ComfyUI) as a serverless API on [RunPod](https://www.runpod.io/).

Based on [runpod-workers/worker-comfyui](https://github.com/runpod-workers/worker-comfyui).

## Quick Start

```bash
bin/build dev    # Build Docker image
bin/push dev     # Push to Docker Hub
bin/test-local   # Test locally with docker-compose
```

## Provisioned Models

Models are auto-downloaded to Network Volume on first startup.

| Model | Size | Purpose |
|-------|------|---------|
| Z-Image-Turbo | ~15GB | Fast text-to-image generation |
| Flux Kontext | ~12GB | Context-aware image generation |
| InfiniteYou | ~3GB | Character identity preservation |
| RealESRGAN x4+ | ~67MB | 4x image upscaling |
| Qwen2.5-VL 7B | ~15GB | Image/video captioning (auto-downloads on first use) |

**Requirements:** RunPod Network Volume (50GB minimum), RTX 4090 or equivalent (24GB VRAM)

## Qwen2.5-VL Captioning

[ComfyUI-Qwen2_5-VL](https://github.com/MakkiShizu/ComfyUI-Qwen2_5-VL) for vision-language tasks.

### Supported Input Types

| Type | Description |
|------|-------------|
| Single Image | Describe or analyze one image |
| Multi-Image | Compare multiple images |
| Video | Describe video content |

### Available Models

| Model | VRAM (8-bit) |
|-------|--------------|
| `Qwen/Qwen2.5-VL-3B-Instruct` | ~3 GB |
| `Qwen/Qwen2.5-VL-7B-Instruct` | ~7 GB |
| `Qwen/Qwen2.5-VL-32B-Instruct` | ~33 GB |
| `Qwen/Qwen2.5-VL-72B-Instruct` | ~67 GB |

### Nodes

| Node | Description |
|------|-------------|
| `DownloadAndLoadQwen2_5_VLModel` | Load VL model |
| `Qwen2_5_VL_Run` | Run inference |
| `Qwen2_5_VL_Run_Advanced` | Inference with system prompt |

## API Usage

### Request

```json
{
  "input": {
    "workflow": { ... },
    "images": [
      { "name": "input.png", "image": "base64_encoded_string" }
    ]
  }
}
```

### Response

```json
{
  "status": "COMPLETED",
  "output": {
    "message": "base64_or_s3_url",
    "status": "success"
  }
}
```

For text output (captioning):
```json
{
  "output": {
    "text": [{ "node_id": "4", "type": "text", "data": "Description..." }]
  }
}
```

## Configuration

| Environment Variable | Description | Default |
|---------------------|-------------|---------|
| `REFRESH_WORKER` | Stop worker after each job | `false` |
| `COMFY_POLLING_INTERVAL_MS` | Poll interval | `250` |
| `COMFY_POLLING_MAX_RETRIES` | Max retries | `500` |
| `SERVE_API_LOCALLY` | Enable local API | disabled |

### AWS S3 Upload (Optional)

| Variable | Description |
|----------|-------------|
| `BUCKET_ENDPOINT_URL` | S3 endpoint URL |
| `BUCKET_ACCESS_KEY_ID` | AWS access key |
| `BUCKET_SECRET_ACCESS_KEY` | AWS secret key |

## Local Testing

```bash
docker-compose up
```

- Worker API: http://localhost:8000
- ComfyUI: http://localhost:8188

## Workflows

Export workflows from ComfyUI: Settings → Enable Dev mode → Save (API Format)

Example workflows in `workflows/` directory.
