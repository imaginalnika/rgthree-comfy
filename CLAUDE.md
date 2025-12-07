# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What is rgthree-comfy

A ComfyUI custom nodes extension providing workflow nodes (Context, Reroute, Power Lora Loader, etc.) and UI improvements (progress bar, fast muters, bookmarks). It's installed as a ComfyUI custom node under `custom_nodes/`.

## Build Commands

```bash
# Build frontend (TypeScript + SASS)
npm run build
# or directly:
python __build__.py

# Build with import path fixing
python __build__.py --fix

# Build keeping test directories
python __build__.py --no-tests
```

The build process:
1. Copies `src_web/` to `web/` (excluding `.ts`, `.scss`, `typings/`)
2. Compiles TypeScript
3. Compiles SASS
4. Rewrites import paths for ComfyUI's extension loading

## Architecture

### Python Backend (`py/`)
- Node implementations as classes with `NAME` class attribute
- Registered in `__init__.py` via `NODE_CLASS_MAPPINGS`
- `py/server/` contains HTTP API endpoints
- `py/config.py` handles `rgthree_config.json` settings

### TypeScript Frontend (`src_web/`)
- `src_web/comfyui/` - Node UI implementations and ComfyUI integration
  - `base_*.ts` - Base classes for node types
  - `rgthree.ts` - Core extension singleton
- `src_web/common/` - Shared utilities (mapped as `rgthree/common/*`)
- `src_web/lib/` - Third-party libraries (mapped as `rgthree/lib/*`)
- `src_web/scripts_comfy/` - Dummy type declarations for ComfyUI's own scripts

### Output (`web/`)
- Compiled JS/CSS served by ComfyUI
- `WEB_DIRECTORY = "./web/comfyui"` tells ComfyUI where to find frontend code

### Path Mappings (tsconfig.json)
```
"rgthree/common/*" -> src_web/common/*
"rgthree/lib/*"    -> src_web/lib/*
"scripts/*"        -> src_web/scripts_comfy/*
```

Build rewrites these to relative paths for browser loading under `/extensions/rgthree-comfy/`.

## Configuration

User settings stored in `rgthree_config.json` (copy from `rgthree_config.json.default`). Accessed via `py/config.py` and `src_web/comfyui/config.ts`.
