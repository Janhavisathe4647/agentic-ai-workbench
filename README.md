# agentic-ai-workbench
# 🛡️ PS26117: Air-Gapped Multi-Tenant AI Workbench & Enterprise Platform

> **Version**: 4.0.0 (Single-Machine Localhost Consolidation)  
> **Target Platform**: Air-Gapped Workstation (`http://127.0.0.1:8000` / `http://localhost:5173`)  
> **Zero Egress Guarantee**: Strict `0` External Network Calls Verified  
> **Model Backend**: Ollama & **vLLM (100% OpenAI Endpoint Compatible)**  
> **Frontend Stack**: React + TypeScript + Vite + Zustand  

---

## 📖 Table of Contents
1. [Executive Overview](#-executive-overview)
2. [Key Features](#-key-features)
3. [System Architecture](#-system-architecture)
4. [vLLM & Ollama Local Model Integration](#-vllm--ollama-local-model-integration)
5. [Programmatic Enterprise Deliverables (.docx, .xlsx, .py)](#-programmatic-enterprise-deliverables-docx-xlsx-py)
6. [Frontend UI & Dynamic Chat Experience](#-frontend-ui--dynamic-chat-experience)
7. [Installation & Quickstart Guide](#-installation--quickstart-guide)
8. [API Reference & Endpoints](#-api-reference--endpoints)
9. [Automated Verification & Testing Suite](#-automated-verification--testing-suite)
10. [Repository Directory Structure](#-repository-directory-structure)

---

## 🌟 Executive Overview

**PS26117 Air-Gapped AI Workbench** is a mission-critical, sovereign agentic orchestration platform engineered for high-security industrial (**MRPL Refinery**) and academic (**AIET Governance**) environments. Operating 100% disconnected from the public internet, it combines:

- Multi-step **ReAct (Reason + Act)** autonomous agents with dynamic model routing.
- Specialized local Small Language Models (SLMs) running via **vLLM** or **Ollama**.
- Local Vector RAG (ChromaDB) for domain-specific SOPs and guidelines.
- Isolated AST-inspected Python execution sandbox (`--network none`).
- Programmatic enterprise document authoring for formatted Word (`.docx`), Excel (`.xlsx`), and PowerPoint (`.pptx`) files.
- Modern high-performance web interface built with React, TypeScript, Vite, and Zustand.

---

## ✨ Key Features

### 1. 🛡️ 100% Air-Gapped & Zero Network Egress
- **AST Static Analysis & Traffic Auditing**: Every tool call and Python code payload is inspected prior to execution.
- **Zero Internet Dependence**: 0 external API calls (0 telemetry, 0 cloud dependencies).

### 2. 💬 Streamlined Multi-Turn Chat & Thinking Animation
- **Single Active Thread Continuity**: Subsequent prompts append to the active chat thread without cluttering the sidebar with duplicate chat items.
- **Inline Activity Indicator**: Step-by-step ReAct agent reasoning (`Thinking...`) displays in real time with spinning indicators directly inside the chat turn without changing or reloading the page.

### 3. 📄 Automated Document & Excel Generator
- **Word Reports (`.docx`)**: Generates official technical inspection notes, compliance memorandums, and academic circulars.
- **Excel Sheets (`.xlsx`)**: Builds styled, formatted multi-column workbooks for engineering telemetry, vibration metrics, and financial records.
- **Python Sandbox (`.py`)**: Executes data analytics scripts safely inside a local sandbox.

### 4. 🏢 Multi-Tenant Domain Switching
- **MRPL Refinery Operations**: Equipment wear clearance tolerances (SOP-M-402), vibration velocity limits, and maintenance approval ticketing.
- **AIET Academic Governance**: Academic notices, circular generation, student fee analytics, and placement stats.

---

## 🏗️ System Architecture

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                       React + TypeScript Workbench UI                       │
│                     (Vite Dev Server: http://localhost:5173)                │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │ HTTP / REST / WebSockets
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FastAPI Web Server & API Layer                      │
│                           (http://127.0.0.1:8000)                           │
├─────────────────────────────────────────────────────────────────────────────┤
│  • /api/chat          • /api/upload          • /outputs (Static Deliverables)│
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          ReAct Agent Orchestration Engine                   │
│                        (core/agent.py & core/router.py)                     │
├───────────────────┬───────────────────┬───────────────────┬─────────────────┤
│    RAG Search     │   Office Doc Gen  │   Python Sandbox  │    Vision OCR   │
│   (ChromaDB)      │   (.docx/.xlsx)   │    (AST Safe)     │   (Qwen2-VL)    │
└───────────────────┴───────────────────┴───────────────────┴─────────────────┘
                                       │ HTTP POST /v1/chat/completions
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Local Model Engine (vLLM / Ollama)                       │
│      Port 8001: General/Tools  |  Port 8002: Code  |  Port 8003: Vision      │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## ⚡ vLLM & Ollama Local Model Integration

The platform provides native **OpenAI-compatible REST interface integration** (`/v1/chat/completions`) supporting both **vLLM** and **Ollama**.

### Model Endpoint Configuration (`config/config.py`):
- **General & Tool-Calling**: `http://127.0.0.1:8001/v1` (`Qwen2.5-1.5B-Instruct-AWQ`)
- **Coding Specialist**: `http://127.0.0.1:8002/v1` (`Qwen2.5-Coder-1.5B-Instruct-AWQ`)
- **Vision & OCR**: `http://127.0.0.1:8003/v1` (`Qwen2-VL-2B-Instruct`)

To switch model backends seamlessly:
```powershell
$env:BACKEND_MODE="vllm"
python run_server.py
```

---

## 📊 Programmatic Enterprise Deliverables (.docx, .xlsx, .py)

When requested, the agent invokes dedicated output tools that generate formatted files saved locally in `outputs/`:

| Deliverable Type | Tool Name | Output Format | Storage Path |
|---|---|---|---|
| **Word Approval Note** | `generate_approval_note` | Microsoft Word (`.docx`) | `outputs/approval-note.docx` |
| **Excel Telemetry Sheet** | `generate_report_sheet` | Microsoft Excel (`.xlsx`) | `outputs/MRPL_Inspection_Report.xlsx` |
| **Office Presentation** | `generate_office_document` | PowerPoint (`.pptx`) | `outputs/MRPL_Deliverable.pptx` |
| **Code Sandbox Output** | `execute_code_sandbox` | Python Log / Script | `outputs/` / Console stdout |

*All generated deliverables can be downloaded directly from the chat UI via the backend static mount (`http://127.0.0.1:8000/outputs/<filename>`).*

---

## 🖥️ Frontend UI & Dynamic Chat Experience

The workbench web application (`SIH_2026/workbench_frontend`) is built with React 18, TypeScript, Vite, and Zustand:

- **State Management**: [`src/store/workbenchStore.ts`](file:///d:/PS26117/TeamLagaan_Integration/Team%20Lagaan/SIH_2026/workbench_frontend/src/store/workbenchStore.ts) handles chat history, active chat turns, attachment state, theme management, and backend synchronization.
- **Main Tasks Page**: [`src/pages/Tasks.tsx`](file:///d:/PS26117/TeamLagaan_Integration/Team%20Lagaan/SIH_2026/workbench_frontend/src/pages/Tasks.tsx) renders dynamic message threads, ReAct reasoning steps, inspection result cards, and deliverable download buttons.
- **Composer**: [`src/components/chat/Composer.tsx`](file:///d:/PS26117/TeamLagaan_Integration/Team%20Lagaan/SIH_2026/workbench_frontend/src/components/chat/Composer.tsx) provides attachment uploads, prompt clearing, and model selection.

---

## 🚀 Installation & Quickstart Guide

### Prerequisites
- **Python**: 3.10+ (Tested on Python 3.14)
- **Node.js**: 18+ (Node binaries located at `C:\Program Files\nodejs` on Windows)

---

### Step 1: Set Up Backend Environment

```powershell
# Navigate to repository root
cd "d:\PS26117\TeamLagaan_Integration\Team Lagaan"

# Create virtual environment (optional)
python -m venv venv
.\venv\Scripts\activate

# Install Python dependencies
pip install -r requirements.txt
```

### Step 2: Start FastAPI Backend Server

```powershell
python run_server.py
```
*Backend runs on `http://127.0.0.1:8000`. Explore interactive API documentation at `http://127.0.0.1:8000/docs`.*

---

### Step 3: Start React Frontend Application

Open a second PowerShell terminal:

```powershell
# Navigate to workbench frontend directory
cd "d:\PS26117\TeamLagaan_Integration\Team Lagaan\SIH_2026\workbench_frontend"

# Launch Vite development server
$env:Path += ";C:\Program Files\nodejs"
& "C:\Program Files\nodejs\npm.cmd" run dev
```

*Open your browser at **[http://localhost:5173/](http://localhost:5173/)**.*

---

## 📡 API Reference & Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | System status, version info, and active endpoints |
| `POST` | `/api/chat` | Main ReAct agent loop execution endpoint |
| `POST` | `/api/upload` | Uploads documents/images to `data/uploads/` |
| `GET` | `/outputs/{filename}` | Serves generated `.docx`, `.xlsx`, and `.pptx` deliverables |
| `GET` | `/api/tools` | Discovers registered agent tools and schemas |
| `GET` | `/api/approvals` | Fetches human-in-the-loop approval tickets |
| `GET` | `/api/monitor` | Audits network egress metrics (`external_calls: 0`) |

---

## 🧪 Automated Verification & Testing Suite

The repository includes a comprehensive 8-stage verification suite powered by `pytest`:

```powershell
python -m pytest tests/test_verification_pipeline.py -v
```

### Verification Coverage:
- `test_stage_a_qwen3_api_configuration`: Validates model endpoint configuration.
- `test_stage_b_backend_fastapi_endpoints`: Verifies FastAPI routes and payload schemas.
- `test_stage_c_router_task_classification`: Tests specialist router dispatching.
- `test_stage_c_agent_execution_loop`: Verifies ReAct step-budget enforcement and tool calls.
- `test_stage_d_vision_tool_pipeline`: Tests OCR parameter extraction.
- `test_stage_e_real_end_to_end_inspection_workflow`: End-to-end test from image input to Word `.docx` output.
- `test_stage_f_verify_model_config`: Verifies network isolation & fallback parameters.
- `test_stage_g_sequential_model_servers`: Tests multi-port serving compatibility.

**Result**: `8/8 PASSED` (100% Verification Success).

---

## 📁 Repository Directory Structure

```text
Team Lagaan/
│
├── api/                                # FastAPI Web Server & API Layer
│   ├── routes_approvals.py             # Human approval workflow endpoints
│   ├── routes_chat.py                  # Agent chat execution & upload endpoints
│   ├── routes_monitor.py               # Zero network egress monitor
│   ├── routes_tools.py                 # Tool registry catalog endpoint
│   └── server.py                       # FastAPI entrypoint & static /outputs mount
│
├── config/                             # Multi-Tenant & Model Configuration
│   ├── config.py                       # vLLM / Ollama endpoint definitions & ports
│   └── org_profiles.json               # MRPL and College organization profiles
│
├── core/                               # Agent Orchestration & LLM Client
│   ├── agent.py                        # ReAct agent loop & guardrails
│   ├── llm_client.py                   # OpenAI-compatible LLM client & simulator
│   ├── prompts.py                      # Multi-tenant ReAct system prompts
│   └── router.py                       # Task classifier & model router
│
├── data/                               # Knowledge Ingestion & Datasets
│   ├── chroma_db/                      # ChromaDB vector store
│   ├── college/                        # College policies & datasets
│   ├── mrpl/                           # Refinery SOPs (SOP-M-402, SOP-SAFETY-108)
│   └── uploads/                        # Uploaded files and images
│
├── outputs/                            # Programmatically Generated Deliverables (.docx, .xlsx, .pptx)
│
├── SIH_2026/
│   └── workbench_frontend/             # React 18 + TypeScript + Vite Frontend Application
│       ├── src/
│       │   ├── components/             # Reusable UI components (Composer, Sidebar, TopHeader)
│       │   ├── pages/                  # Page routes (Tasks, MyFiles, ModelHub, Governance)
│       │   ├── services/api.ts         # Frontend API HTTP client
│       │   ├── store/workbenchStore.ts # Zustand global application state
│       │   └── utils/fileDownloader.ts # File downloader utility
│       ├── package.json                # NPM package config & scripts
│       └── vite.config.ts              # Vite server configuration
│
├── tests/                              # Automated Test Suite (pytest)
│   └── test_verification_pipeline.py   # 8-Stage end-to-end verification pipeline
│
├── tools/                              # Autonomous Agent Tools
│   ├── approval_tool.py                # Human-in-the-loop approval ticketing
│   ├── data_analysis_tool.py           # Pandas analytics tool
│   ├── document_tool.py                # Word (.docx) & Excel (.xlsx) generator
│   ├── rag_tool.py                     # ChromaDB semantic search tool
│   ├── registry.py                     # Tool registry decorator & schema formatter
│   ├── sandbox_tool.py                 # AST-inspected Python execution sandbox
│   └── vision_tool.py                  # Multimodal Vision & OCR tool
│
├── requirements.txt                    # Python dependencies
├── run_server.py                       # Server launcher script
└── README.md                           # Master Documentation
```

---
# detect-libc

Node.js module to detect details of the C standard library (libc)
implementation provided by a given Linux system.

Currently supports detection of GNU glibc and MUSL libc.

Provides asychronous and synchronous functions for the
family (e.g. `glibc`, `musl`) and version (e.g. `1.23`, `1.2.3`).

The version numbers of libc implementations
are not guaranteed to be semver-compliant.

For previous v1.x releases, please see the
[v1](https://github.com/lovell/detect-libc/tree/v1) branch.

## Install

```sh
npm install detect-libc
```

## API

### GLIBC

```ts
const GLIBC: string = 'glibc';
```

A String constant containing the value `glibc`.

### MUSL

```ts
const MUSL: string = 'musl';
```

A String constant containing the value `musl`.

### family

```ts
function family(): Promise<string | null>;
```

Resolves asychronously with:

* `glibc` or `musl` when the libc family can be determined
* `null` when the libc family cannot be determined
* `null` when run on a non-Linux platform

```js
const { family, GLIBC, MUSL } = require('detect-libc');

switch (await family()) {
  case GLIBC: ...
  case MUSL: ...
  case null: ...
}
```

### familySync

```ts
function familySync(): string | null;
```

Synchronous version of `family()`.

```js
const { familySync, GLIBC, MUSL } = require('detect-libc');

switch (familySync()) {
  case GLIBC: ...
  case MUSL: ...
  case null: ...
}
```

### version

```ts
function version(): Promise<string | null>;
```

Resolves asychronously with:

* The version when it can be determined
* `null` when the libc family cannot be determined
* `null` when run on a non-Linux platform

```js
const { version } = require('detect-libc');

const v = await version();
if (v) {
  const [major, minor, patch] = v.split('.');
}
```

### versionSync

```ts
function versionSync(): string | null;
```

Synchronous version of `version()`.

```js
const { versionSync } = require('detect-libc');

const v = versionSync();
if (v) {
  const [major, minor, patch] = v.split('.');
}
```

### isNonGlibcLinux

```ts
function isNonGlibcLinux(): Promise<boolean>;
```

Resolves asychronously with:

* `false` when the libc family is `glibc`
* `true` when the libc family is not `glibc`
* `false` when run on a non-Linux platform

```js
const { isNonGlibcLinux } = require('detect-libc');

if (await isNonGlibcLinux()) { ... }
```

### isNonGlibcLinuxSync

```ts
function isNonGlibcLinuxSync(): boolean;
```

Synchronous version of `isNonGlibcLinux()`.

```js
const { isNonGlibcLinuxSync } = require('detect-libc');

if (isNonGlibcLinuxSync()) { ... }
```

## Licensing

Copyright 2017 Lovell Fuller and others.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.


## 👥 Team & Credits

- **Team**: Lagaan (PS26117)
- **Role**: Agent Orchestration, Local Model Dispatch, Document Generation & UI Integration
- **License**: Proprietary / Sovereign Enterprise Workstation Use
