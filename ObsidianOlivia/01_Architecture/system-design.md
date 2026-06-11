---
type: architecture
status: draft
tags: [robotics, infrastructure, core]
last_modified: 2026-05-21
---
# Olivia System Design Blueprint

## Compute & Infrastructure Node
- **Host OS:** Windows 11 Pro via WSL2 (Ubuntu Linux runtime).
- **Primary GPU Accelerator:** NVIDIA RTX 5090 (32GB VRAM Dedicated).
- **Local AI Engine:** Ollama serving `Qwen2.5-Coder-32b` (Context Window: 32k tokens).
- **Development Engine:** Aider Chat CLI (Git-integrated code execution loop).

## Workspace Boundaries
- Active Code Repository: `~/projects/OLIVIA/src/`
- Isolated Environments: `~/projects/OLIVIA/env/` (Exclusively python libraries)
- Memory Vault Space: `~/projects/OLIVIA/Obsidian/`

## Project Mission Goals
1. Establish a local-first, privacy-secure, air-gapped development pipeline.
2. Develop high-performance agentic algorithms without relying on heavy cloud dependencies.
EOF