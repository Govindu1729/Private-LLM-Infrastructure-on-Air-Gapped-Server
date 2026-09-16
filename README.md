# Qwen Local AI Agent

A self-hosted AI assistant built around Qwen3.8-27B-FP8, vLLM, FastAPI, React, and NVIDIA L40S GPUs.

The project explores the design of a local ChatGPT-like AI system with streaming inference, reasoning control, persistent conversations, file interaction, and controlled tool execution.

---

## Overview

This project is designed as a local AI assistant rather than a simple interface around an LLM API.

The system consists of:

- Qwen3.8-27B-FP8 as the language model
- vLLM for high-performance inference
- FastAPI as the backend and orchestration layer
- React as the web interface
- SQLite for persistent conversation history
- A controlled tool architecture for future agent capabilities
- NVIDIA L40S GPUs for local inference

The model and inference stack run on a private server.

---

## Architecture

```text
┌──────────────────────────────┐
│          Browser             │
│       React Frontend         │
└──────────────┬───────────────┘
               │
               │ HTTP / SSE
               ▼
┌──────────────────────────────┐
│       FastAPI Backend        │
│                              │
│  Chat API                    │
│  Conversation Management     │
│  Agent / Tool Orchestration  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│            vLLM              │
│                              │
│       Qwen3.8-27B-FP8        │
│       Tensor Parallelism     │
└──────────────┬───────────────┘
               │
               ▼
       NVIDIA L40S GPUs
