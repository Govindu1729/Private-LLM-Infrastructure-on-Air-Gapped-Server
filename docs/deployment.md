# Deployment

## Runtime

- Ubuntu 22.04 LTS
- Python 3.10.12
- PyTorch 2.13.0+cu129
- vLLM 0.27.1+cu129
- CUDA 12.9 user-space toolkit
- NVIDIA L40S × 2

## Ports

| Service | Port |
|---|---:|
| Frontend | 5173 |
| FastAPI | 8003 |
| vLLM | 8002 |

## Startup order

1. vLLM
2. FastAPI
3. React/Vite frontend
