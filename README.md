<div align="center">

# Qwen Local AI Agent

**A self-hosted, privacy-first AI assistant built on Qwen3.8-27B-FP8, vLLM, FastAPI, React, and dual NVIDIA L40S GPUs.**

[![vLLM](https://img.shields.io/badge/vLLM-0.27.1-FF6F00?style=flat-square)](https://github.com/vllm-project/vllm)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.13.0-EE4C2C?style=flat-square&logo=pytorch&logoColor=white)](https://pytorch.org/)
[![CUDA](https://img.shields.io/badge/CUDA-12.9-76B900?style=flat-square&logo=nvidia&logoColor=white)](https://developer.nvidia.com/cuda-toolkit)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.136-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-19.3-61DAFB?style=flat-square&logo=react&logoColor=white)](https://react.dev/)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](./LICENSE)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Model & Hardware](#model--hardware)
- [Software Stack](#software-stack)
- [Features](#features)
- [Agent Design](#agent-design)
- [Tool Surface](#tool-surface)
- [Security Principles](#security-principles)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Development Roadmap](#development-roadmap)
- [Repository Status](#repository-status)
- [License](#license)

---

## Overview

This project implements a **local AI assistant** — a self-hosted alternative to cloud-based LLM services — designed for privacy-sensitive and air-gapped environments.

Unlike a thin wrapper around a remote API, the system owns the full inference path:

- **Model** — Qwen3.8-27B-FP8, running natively on the GPU
- **Inference** — vLLM for high-throughput, low-latency generation
- **Backend** — FastAPI orchestrating chat, streaming, and agent execution
- **Frontend** — React with a ChatGPT-style interface
- **Persistence** — SQLite for conversation and run history
- **Tool Layer** — a controlled, auditable execution surface for agent capabilities

The full stack runs on a private server with no outbound network dependencies.

---

## Architecture


---

## Model & Hardware

### Model

| Property | Value |
|---|---|
| **Name** | Qwen3.8-27B-FP8 |
| **Repository** | [huggingface.co/Qwen/Qwen3.8-27B-FP8](https://huggingface.co/Qwen/Qwen3.8-27B-FP8) |
| **Precision** | FP8 |
| **Parameters** | ~27B |
| **Context window** | 262,144 tokens native (served at 65,536) |
| **Capabilities** | Text, vision-language, tool-calling, long-context reasoning |

### Hardware

| Component | Specification |
|---|---|
| **GPU** | 2 × NVIDIA L40S |
| **VRAM** | 48 GB per GPU (96 GB total) |
| **Inference mode** | Tensor Parallelism (TP=2) |
| **System RAM** | 128 GB |
| **OS** | Ubuntu 22.04 LTS |

---

## Software Stack

<table>
<tr>
<td valign="top" width="33%">

**Backend**
- Python 3.10
- FastAPI
- Uvicorn
- SQLite
- vLLM client

</td>
<td valign="top" width="33%">

**Frontend**
- React 19
- Vite 8
- `react-markdown`
- `react-syntax-highlighter`
- KaTeX / LaTeX

</td>
<td valign="top" width="33%">

**Inference**
- Qwen3.8-27B-FP8
- vLLM 0.27.1
- NVIDIA CUDA 12.9
- Tensor Parallelism

</td>
</tr>
</table>

---

## Features

### Implemented

- ✅ **Local LLM inference** — Qwen3.8-27B-FP8, fully offline
- ✅ **Multi-GPU inference** — TP=2 across both L40S cards
- ✅ **Streaming responses** — token-by-token via SSE
- ✅ **Reasoning / answer separation** — `<think>` blocks parsed cleanly
- ✅ **Persistent conversations** — SQLite-backed history
- ✅ **ChatGPT-style interface** — clean, focused web UI
- ✅ **Rich rendering** — Markdown, code, LaTeX
- ✅ **Configurable reasoning effort** — auto / low / medium / high / ultra
- ✅ **Agent mode** — tool-calling loop with explicit backend authorization
- ✅ **Controlled tool execution** — auditable, least-privilege tool surface

### In Progress

- 🚧 **Persistent agent runs** — resume execution after client disconnect
- 🚧 **Preview pane** — live rendering of files, plots, and HTML
- 🚧 **Notebook execution** — cell-by-cell `.ipynb` runs
- 🚧 **Resource retrieval** — search across local document corpus

- 
---

## Agent Design

The agent is designed around **explicit, backend-registered tools**. The model never receives unrestricted access to the filesystem, network, or shell.
LLM
│
▼
Tool selection
│
▼
Backend authorization
│
▼
Specific tool
│
▼
Validated result
│
▼
LLM

This separation ensures a clean boundary between **model-generated intent** and **actual system actions**. Every tool invocation is:

1. Chosen by the model via structured XML
2. Validated by the backend against a schema
3. Authorized against a permission policy
4. Executed in a restricted environment
5. Returned to the model as a structured result

---

## Tool Surface

| Tool | Purpose | Access |
|---|---|---|
| `fs.read()` | Read a file | Read-only, jailed |
| `fs.write()` | Write a file | Sandbox-only |
| `fs.list()` | List a directory | Read-only, jailed |
| `py.run()` | Execute a Python script | Sandbox-only, no network |
| `gpu.status()` | Report GPU usage | Read-only |
| `resource.search()` | Search local document corpus | Read-only |
| `resource.read()` | Read a corpus document | Read-only |
| `db.query()` | Query the conversation DB | Read-only |

**Arbitrary shell execution is intentionally excluded.**

---

## Security Principles

- **No direct model access** to credentials, environment, or shell
- **File access** restricted to approved project directories
- **Python execution** restricted to controlled sandbox directories
- **Database access** read-only where possible
- **Sensitive data** stored server-side only, never in prompts
- **High-risk actions** require explicit user confirmation
- **Least-privilege permissions** for every tool

> Full details: [`docs/tool-security.md`](docs/tool-security.md)

---

## Project Structure

---

## Getting Started

> **Note:** The full system requires a GPU-enabled environment with vLLM and the Qwen3.8-27B-FP8 model locally available. See [`docs/deployment.md`](docs/deployment.md) for a complete setup guide.

### Prerequisites

- Python 3.10+
- CUDA 12.9 compatible GPU(s) with ≥ 80 GB total VRAM
- Node.js 20+
- ~50 GB disk for the model weights
- (Optional) Offline wheel cache for air-gapped installs

### 1. Start the vLLM inference server

```bash
source /path/to/vllm-env/bin/activate

VLLM_USE_FLASHINFER_SAMPLER=0 \
CUDA_VISIBLE_DEVICES=0,1 \
vllm serve /path/to/models/Qwen3.8-27B-FP8 \
  --tensor-parallel-size 2 \
  --max-model-len 65536 \
  --port 8002 \
  --gpu-memory-utilization 0.92 \
  --reasoning-parser qwen3 \
  --enable-auto-tool-choice \
  --tool-call-parser qwen3_xml \
  --enable-prefix-caching \
  --enable-chunked-prefill
Wait for Application startup complete. — the model takes 2–4 minutes to load
```

###2. Start the backend

```bash

source /path/to/vllm-env/bin/activate
cd backend
python -m uvicorn main:app --host 0.0.0.0 --port 8003
```

3. Start the frontend
```bash

cd frontend
npm install
npm run dev -- --host 0.0.0.0
```

4. Open
text

http://<server-ip>:5173/

Development Roadmap
Phase 1 — Local Inference

    ☑

    Qwen model deployment
    ☑

    vLLM inference pipeline
    ☑

    Multi-GPU inference (TP=2)
    ☑

    Streaming generation
    ☑

    Reasoning parser integration
    ☑

    Tool-call parser integration

Phase 2 — Chat Interface

    ☑

    React frontend
    ☑

    FastAPI backend
    ☑

    Persistent conversation history
    ☑

    Markdown rendering
    ☑

    Code block rendering
    ☑

    LaTeX rendering

Phase 3 — Agent Architecture

    ☑

    Agent loop design
    ☑

    Tool registry
    ☑

    File tools (fs.*)
    ☑

    Python execution tool (py.run)
    ☑

    GPU monitoring tool
    ☑

    Resource search interface
    ☑

    Database query tool
    ☑

    Tool authorization layer

Phase 4 — Advanced Interaction

    □

    Stop generation
    □

    Regenerate response
    □

    Multiple simultaneous conversations
    □

    Adaptive reasoning effort
    □

    File uploads
    □

    Image input (vision)
    □

    Tool execution UI panel
Repository Status

This repository currently contains the project's architecture and documentation.

Implementation files will be added incrementally as the system matures. Code quality and reproducibility are prioritised over rapid expansion.
License

This project is released under the MIT License — see LICENSE for details.
<div align="center">

Built with ❤️ for local AI systems 
</div> 
