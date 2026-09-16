# Qwen Local AI Agent

> A self-hosted AI assistant powered by Qwen3.8-27B-FP8, deployed locally on a dual-NVIDIA L40S server with vLLM, FastAPI, and React.

## Overview

This project explores the deployment and development of a local, ChatGPT-style AI assistant using a large language model hosted entirely on local infrastructure.

The system is built around **Qwen3.8-27B-FP8** and uses **vLLM** for high-performance inference across two NVIDIA L40S GPUs. A FastAPI backend handles conversations, streaming, reasoning control, and tool orchestration, while a React/Vite frontend provides the user interface.

The long-term goal is to develop a controlled AI agent capable of using explicitly authorized tools while maintaining clear boundaries between the language model, application backend, and external resources.

## Architecture

```text
                         ┌──────────────────────┐
                         │      React + Vite     │
                         │       Web Client      │
                         │        :5173          │
                         └──────────┬───────────┘
                                    │
                               HTTP / SSE
                                    │
                         ┌──────────▼───────────┐
                         │     FastAPI Backend    │
                         │         :8003          │
                         │                        │
                         │ • Chat management      │
                         │ • Streaming            │
                         │ • Reasoning handling   │
                         │ • Effort routing       │
                         │ • Conversation history │
                         │ • Tool orchestration   │
                         └──────────┬─────────────┘
                                    │
                           OpenAI-compatible API
                                    │
                         ┌──────────▼───────────┐
                         │         vLLM           │
                         │         :8002           │
                         │                         │
                         │ Tensor Parallel = 2     │
                         └──────────┬──────────────┘
                                    │
                       ┌────────────┴────────────┐
                       │                         │
                ┌──────▼──────┐           ┌──────▼──────┐
                │ NVIDIA L40S │           │ NVIDIA L40S │
                │   GPU 0     │           │   GPU 1     │
                │   ~46 GiB   │           │   ~46 GiB   │
                └─────────────┘           └─────────────┘
                       │                         │
                       └───────────┬─────────────┘
                                   │
                         Qwen3.8-27B-FP8
