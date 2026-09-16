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

text
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

Future tool-enabled execution will follow:

User
  │
  ▼
React UI
  │
  ▼
FastAPI Agent
  │
  ├──────────────► Qwen
  │                  │
  │                  ▼
  │             Tool decision
  │                  │
  │                  ▼
  ├──────────────► Controlled Tool
  │                  │
  │                  ▼
  │             Tool result
  │                  │
  └──────────────◄───┘
  │
  ▼
Final response
Model

Qwen3.8-27B-FP8

The project uses the FP8 version of the Qwen3.8-27B model for local inference.

Model repository:

https://huggingface.co/Qwen/Qwen3.8-27B-FP8

The model provides native support for vision-language interaction and a large context window.

Hardware

Current inference hardware:

2 × NVIDIA L40S
48 GB VRAM per GPU
Tensor parallel inference across both GPUs
128 GB system RAM
Ubuntu 22.04 LTS
Software Stack
Backend
Python
FastAPI
Uvicorn
SQLite
vLLM
Frontend
React
Vite
React Markdown
Syntax highlighting
KaTeX / LaTeX rendering
Inference
Qwen3.8-27B-FP8
vLLM
NVIDIA CUDA
Tensor Parallelism
Current Features

The project currently targets:

Local LLM inference
Qwen3.8-27B-FP8
Multi-GPU inference
Streaming responses
Separate reasoning and final answer handling
Persistent conversation history
ChatGPT-style web interface
Markdown rendering
Code rendering
LaTeX rendering
Configurable reasoning effort
Agent mode
Controlled tool execution architecture
Agent Design

The agent is intentionally designed around explicit backend tools.

The LLM itself does not receive unrestricted access to the server or user data.

Instead:

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

This provides a clear boundary between model-generated decisions and actual system actions.

Planned Tools

The initial tool surface is intended to include:

fs.read()
fs.write()
fs.list()

py.run()

gpu.status()

resource.search()
resource.read()

db.query()

Each tool will have a restricted purpose and defined permissions.

Arbitrary shell execution is intentionally excluded from the initial architecture.

Security Principles

The project follows several security principles:

The model does not directly access credentials.
The model does not receive unrestricted shell access.
File access is restricted to approved project directories.
Python execution is restricted to controlled execution directories.
Database access is read-only where possible.
Sensitive conversation data is stored server-side.
High-risk actions should require explicit user confirmation.
Tool permissions should follow least-privilege principles.

More details are available in:

docs/tool-security.md
