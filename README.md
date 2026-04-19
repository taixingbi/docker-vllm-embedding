# vLLM embeddings (Docker)

OpenAI-compatible embeddings on port **8001** (`/v1/embeddings`), using [vLLM](https://github.com/vllm-project/vllm) with `BAAI/bge-large-en-v1.5` by default.

## Publish image (GitHub Actions)

On push to `main` or manual **workflow_dispatch**, [.github/workflows/docker-push.yml](.github/workflows/docker-push.yml) builds the [Dockerfile](Dockerfile) and pushes:

- `<dockerhub_user>/docker-vllm-embedding-v1:latest`
- `<dockerhub_user>/docker-vllm-embedding-v1:<git_sha>`

Repository secrets: `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`.

## Local or GPU host (Compose)

Requires [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html).

```bash
docker compose up -d
```

Optional: create a `.env` in the repo root with `EMBED_MODEL` and/or `HUGGING_FACE_HUB_TOKEN` if you need them (see [docker-compose.yml](docker-compose.yml)). Weights are cached in the Compose volume `hf-cache`.

## Try embeddings

```bash
curl "http://127.0.0.1:8001/v1/embeddings" \
  -H "Content-Type: application/json" \
  -d '{"model":"BAAI/bge-m3","input":"hello world"}'
```

## pull image and run docker
```bash

docker rm -f vllm-embedding

taixingbi/docker-vllm-embedding-v1:latest
--model BAAI/bge-m3

docker run -d \
  --name vllm-embedding \
  --gpus all \
  -p 8001:8001 \
  taixingbi/docker-vllm-embedding-v1:latest \
  --model BAAI/bge-m3 \
  --host 0.0.0.0 \
  --port 8001 \
  --dtype half \
  --max-model-len 8192 \
  --max-num-seqs 64 \
  --gpu-memory-utilization 0.1
```
