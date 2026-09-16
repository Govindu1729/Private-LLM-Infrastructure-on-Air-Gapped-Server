# Architecture

The application is separated into frontend, backend, inference, and tool/resource layers.

```text
Browser
  |
  v
React/Vite :5173
  |
  v
FastAPI :8003
  |
  +--> SQLite
  |
  v
vLLM :8002
  |
  +--> L40S GPU 0
  +--> L40S GPU 1
  |
  v
Qwen3.8-27B-FP8
```

The backend is the control boundary between the browser and model server.
