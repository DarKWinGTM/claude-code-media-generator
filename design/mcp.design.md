# MCP Server Design - Full Media Suite

Version: 1.0
Date: 2026-01-14
Author: DarKWinGTM
Status: Design Phase

---

## Table of Contents

1. Overview
2. Requirements
3. Architecture
4. Tools Specification
5. Transport Layer
6. Configuration
7. Error Handling
8. Integration Guide
9. Implementation Roadmap
10. Testing Plan

---

## 1. Overview

### 1.1 Project Goal

Create a standalone MCP (Model Context Protocol) Server that provides image generation, video generation, and image editing capabilities through CLIProxyAPI with OAuth authentication.

### 1.2 Key Features

- Image generation via Gemini models
- Video generation via Veo models (future)
- Image editing (edit, inpaint, outpaint, upscale)
- Dual transport support (stdio + SSE HTTP)
- OAuth-based authentication (no API keys)
- Full metadata tracking and management

### 1.3 Design Principles

| Principle | Description |
|-----------|-------------|
| Reusability | Leverage existing image_gen.py logic |
| Modularity | Separate tools, client, transport layers |
| Extensibility | Easy to add new tools and models |
| Reliability | Comprehensive error handling |
| Security | OAuth-only, no hardcoded credentials |

---

## 2. Requirements

### 2.1 Functional Requirements

#### Image Generation
- FR-IMG-01: Generate image from text prompt
- FR-IMG-02: Support multiple aspect ratios (1:1, 3:4, 4:3, 9:16, 16:9)
- FR-IMG-03: Support multiple resolutions (1K, 2K, 4K)
- FR-IMG-04: Support batch generation (multiple prompts)
- FR-IMG-05: Auto-save with metadata

#### Video Generation (Veo)
- FR-VID-01: Generate video from text prompt
- FR-VID-02: Support image-to-video animation
- FR-VID-03: Async generation with status polling
- FR-VID-04: Support multiple durations

#### Image Editing
- FR-EDIT-01: Edit image with text prompt
- FR-EDIT-02: Inpainting (fill masked areas)
- FR-EDIT-03: Outpainting (extend boundaries)
- FR-EDIT-04: Upscaling (increase resolution)

#### Management
- FR-MGT-01: List available models
- FR-MGT-02: List generated files
- FR-MGT-03: Get file metadata
- FR-MGT-04: Delete generated files

### 2.2 Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| Response Time | < 120s for generation |
| Availability | 99% uptime |
| Concurrency | Support multiple requests |
| Storage | Auto-cleanup optional |

### 2.3 Dependencies

| Dependency | Version | Purpose |
|------------|---------|---------|
| Python | >= 3.10 | Runtime |
| mcp | latest | MCP protocol |
| httpx/aiohttp | latest | Async HTTP client |
| Pillow | latest | Image processing |
| CLIProxyAPI | running | Backend API |

---

## 3. Architecture

### 3.1 High-Level Architecture

```text
Architecture Overview

MCP Clients
  ├─ Claude Code CLI
  ├─ VSCode Extension
  └─ Other MCP Clients
  ↓
Transport Layer
  ├─ stdio (direct process)
  └─ SSE HTTP (port 8318)
  ↓
MCP Server Core
  ├─ Tool Registry
  ├─ Request Handler
  └─ Response Formatter
  ↓
Tool Handlers
  ├─ ImageTools
  ├─ VideoTools
  ├─ EditTools
  └─ ManagementTools
  ↓
CLIProxyAPI Client
  ├─ Request Builder
  ├─ Response Parser
  └─ Error Handler
  ↓
CLIProxyAPI (localhost:8317)
  ↓
Gemini / Veo Models (OAuth)
```

### 3.2 Component Diagram

```text
Components

mcp_server/
  │
  ├─ server.py
  │    └─ MCPServer class
  │         ├─ register_tools()
  │         ├─ handle_request()
  │         └─ start()
  │
  ├─ config.py
  │    └─ Settings class
  │         ├─ CLIPROXY_URL
  │         ├─ OUTPUT_DIR
  │         ├─ DEFAULT_MODEL
  │         └─ TRANSPORT_CONFIG
  │
  ├─ tools/
  │    ├─ image.py
  │    │    ├─ generate_image()
  │    │    └─ generate_image_batch()
  │    │
  │    ├─ video.py
  │    │    ├─ generate_video()
  │    │    ├─ get_video_status()
  │    │    └─ image_to_video()
  │    │
  │    ├─ edit.py
  │    │    ├─ edit_image()
  │    │    ├─ inpaint()
  │    │    ├─ outpaint()
  │    │    └─ upscale()
  │    │
  │    └─ management.py
  │         ├─ list_models()
  │         ├─ list_generated()
  │         ├─ get_file_info()
  │         └─ delete_file()
  │
  ├─ client/
  │    └─ cliproxy.py
  │         └─ CLIProxyClient class
  │              ├─ generate_image()
  │              ├─ generate_video()
  │              └─ edit_image()
  │
  └─ transport/
       ├─ stdio.py
       │    └─ StdioTransport class
       │
       └─ sse.py
            └─ SSETransport class
```

### 3.3 Data Flow

```text
Image Generation Flow

1. Client Request
   MCP Client → Tool Call: generate_image
   ↓
2. Tool Handler
   ImageTools.generate_image()
   → Validate parameters
   → Build request payload
   ↓
3. CLIProxyAPI Client
   CLIProxyClient.generate_image()
   → POST to localhost:8317/v1/chat/completions
   → Wait for response (timeout: 120s)
   ↓
4. Response Processing
   → Parse response JSON
   → Extract base64 image data
   → Save to output directory
   → Generate metadata
   ↓
5. Return Result
   → Return file path, metadata, success status
```

---

## 4. Tools Specification

### 4.1 Image Tools

#### generate_image

```json
{
  "name": "generate_image",
  "description": "Generate an image from a text prompt using Gemini image models",
  "inputSchema": {
    "type": "object",
    "properties": {
      "prompt": {
        "type": "string",
        "description": "Text description of the image to generate"
      },
      "model": {
        "type": "string",
        "enum": ["gemini-3-pro-image-preview", "gemini-2.5-flash-image"],
        "default": "gemini-3-pro-image-preview",
        "description": "Gemini model to use"
      },
      "aspect_ratio": {
        "type": "string",
        "enum": ["1:1", "3:4", "4:3", "9:16", "16:9"],
        "default": "1:1",
        "description": "Image aspect ratio"
      },
      "image_size": {
        "type": "string",
        "enum": ["1K", "2K", "4K"],
        "default": "1K",
        "description": "Image resolution"
      }
    },
    "required": ["prompt"]
  }
}
```

**Response:**
```json
{
  "success": true,
  "image_path": "/path/to/+generated_images/image_20260114_120000_0.png",
  "metadata": {
    "prompt": "...",
    "model": "gemini-3-pro-image-preview",
    "aspect_ratio": "1:1",
    "image_size": "1K",
    "file_size_bytes": 1234567,
    "timestamp": "2026-01-14T12:00:00"
  }
}
```

#### generate_image_batch

```json
{
  "name": "generate_image_batch",
  "description": "Generate multiple images from a list of prompts",
  "inputSchema": {
    "type": "object",
    "properties": {
      "prompts": {
        "type": "array",
        "items": { "type": "string" },
        "description": "List of text prompts"
      },
      "model": { "type": "string", "default": "gemini-3-pro-image-preview" },
      "aspect_ratio": { "type": "string", "default": "1:1" },
      "image_size": { "type": "string", "default": "1K" }
    },
    "required": ["prompts"]
  }
}
```

### 4.2 Video Tools (Veo)

#### generate_video

```json
{
  "name": "generate_video",
  "description": "Generate a video from a text prompt using Veo models",
  "inputSchema": {
    "type": "object",
    "properties": {
      "prompt": {
        "type": "string",
        "description": "Text description of the video to generate"
      },
      "duration": {
        "type": "string",
        "enum": ["5s", "10s", "15s"],
        "default": "5s",
        "description": "Video duration"
      },
      "aspect_ratio": {
        "type": "string",
        "enum": ["16:9", "9:16", "1:1"],
        "default": "16:9",
        "description": "Video aspect ratio"
      }
    },
    "required": ["prompt"]
  }
}
```

**Note:** Video generation is async. Returns job_id for status polling.

#### get_video_status

```json
{
  "name": "get_video_status",
  "description": "Check the status of a video generation job",
  "inputSchema": {
    "type": "object",
    "properties": {
      "job_id": {
        "type": "string",
        "description": "Video generation job ID"
      }
    },
    "required": ["job_id"]
  }
}
```

#### image_to_video

```json
{
  "name": "image_to_video",
  "description": "Animate a static image into a video",
  "inputSchema": {
    "type": "object",
    "properties": {
      "image_path": {
        "type": "string",
        "description": "Path to the source image"
      },
      "prompt": {
        "type": "string",
        "description": "Optional motion/animation description"
      },
      "duration": {
        "type": "string",
        "enum": ["5s", "10s"],
        "default": "5s"
      }
    },
    "required": ["image_path"]
  }
}
```

### 4.3 Edit Tools

#### edit_image

```json
{
  "name": "edit_image",
  "description": "Edit an existing image based on a text prompt",
  "inputSchema": {
    "type": "object",
    "properties": {
      "image_path": {
        "type": "string",
        "description": "Path to the image to edit"
      },
      "prompt": {
        "type": "string",
        "description": "Edit instructions"
      },
      "mask_path": {
        "type": "string",
        "description": "Optional mask image for targeted editing"
      }
    },
    "required": ["image_path", "prompt"]
  }
}
```

#### inpaint

```json
{
  "name": "inpaint",
  "description": "Fill in masked areas of an image",
  "inputSchema": {
    "type": "object",
    "properties": {
      "image_path": { "type": "string" },
      "mask_path": { "type": "string" },
      "prompt": { "type": "string" }
    },
    "required": ["image_path", "mask_path", "prompt"]
  }
}
```

#### outpaint

```json
{
  "name": "outpaint",
  "description": "Extend image boundaries in specified direction",
  "inputSchema": {
    "type": "object",
    "properties": {
      "image_path": { "type": "string" },
      "direction": {
        "type": "string",
        "enum": ["left", "right", "up", "down", "all"],
        "description": "Direction to extend"
      },
      "prompt": { "type": "string" },
      "extend_pixels": {
        "type": "integer",
        "default": 256,
        "description": "Pixels to extend"
      }
    },
    "required": ["image_path", "direction", "prompt"]
  }
}
```

#### upscale

```json
{
  "name": "upscale",
  "description": "Upscale image resolution",
  "inputSchema": {
    "type": "object",
    "properties": {
      "image_path": { "type": "string" },
      "scale_factor": {
        "type": "number",
        "enum": [2, 4],
        "default": 2,
        "description": "Scale multiplier"
      }
    },
    "required": ["image_path"]
  }
}
```

### 4.4 Management Tools

#### list_models

```json
{
  "name": "list_models",
  "description": "List available generation models",
  "inputSchema": {
    "type": "object",
    "properties": {
      "type": {
        "type": "string",
        "enum": ["image", "video", "all"],
        "default": "all"
      }
    }
  }
}
```

#### list_generated

```json
{
  "name": "list_generated",
  "description": "List generated files",
  "inputSchema": {
    "type": "object",
    "properties": {
      "type": {
        "type": "string",
        "enum": ["image", "video", "all"],
        "default": "all"
      },
      "limit": {
        "type": "integer",
        "default": 20
      },
      "sort_by": {
        "type": "string",
        "enum": ["date", "size", "name"],
        "default": "date"
      }
    }
  }
}
```

#### get_file_info

```json
{
  "name": "get_file_info",
  "description": "Get metadata of a generated file",
  "inputSchema": {
    "type": "object",
    "properties": {
      "filename": { "type": "string" }
    },
    "required": ["filename"]
  }
}
```

#### delete_file

```json
{
  "name": "delete_file",
  "description": "Delete a generated file",
  "inputSchema": {
    "type": "object",
    "properties": {
      "filename": { "type": "string" },
      "confirm": {
        "type": "boolean",
        "default": false,
        "description": "Confirm deletion"
      }
    },
    "required": ["filename", "confirm"]
  }
}
```

---

## 5. Transport Layer

### 5.1 stdio Transport

Primary transport for direct Claude Code CLI integration.

```text
stdio Flow

Claude Code CLI
  ↓
spawn process: python run_mcp.py --transport stdio
  ↓
stdin → JSON-RPC requests
stdout ← JSON-RPC responses
stderr → Logging (optional)
```

**Configuration:**
```python
# run_mcp.py
if args.transport == "stdio":
    server.run_stdio()
```

### 5.2 SSE HTTP Transport

Secondary transport for shared access and web clients.

```text
SSE Flow

HTTP Client
  ↓
GET http://localhost:8318/sse
  ↓
Server-Sent Events stream
  ↓
POST http://localhost:8318/messages
  ↓
Response via SSE stream
```

**Configuration:**
```python
# run_mcp.py
if args.transport == "sse":
    server.run_sse(host="127.0.0.1", port=8318)
```

### 5.3 Dual Transport Mode

Support both transports simultaneously.

```text
Dual Mode

run_mcp.py --transport dual
  ↓
Main Thread: stdio transport
Background Thread: SSE HTTP server (port 8318)
```

---

## 6. Configuration

### 6.1 Configuration File

```python
# mcp_server/config.py

from pathlib import Path
from dataclasses import dataclass, field
from typing import List

@dataclass
class Settings:
    # CLIProxyAPI
    CLIPROXY_URL: str = "http://127.0.0.1:8317"
    CLIPROXY_AUTH: str = "Bearer ccs-internal-managed"
    REQUEST_TIMEOUT: int = 120

    # Output
    OUTPUT_DIR: Path = Path(__file__).parent.parent / "+generated_images"
    METADATA_DIR: Path = Path(__file__).parent.parent / "metadata"

    # Models
    DEFAULT_IMAGE_MODEL: str = "gemini-3-pro-image-preview"
    DEFAULT_VIDEO_MODEL: str = "veo-2"  # Future
    AVAILABLE_IMAGE_MODELS: List[str] = field(default_factory=lambda: [
        "gemini-3-pro-image-preview",
        "gemini-2.5-flash-image"
    ])

    # Transport
    SSE_HOST: str = "127.0.0.1"
    SSE_PORT: int = 8318

    # Defaults
    DEFAULT_ASPECT_RATIO: str = "1:1"
    DEFAULT_IMAGE_SIZE: str = "1K"
    DEFAULT_VIDEO_DURATION: str = "5s"

settings = Settings()
```

### 6.2 Environment Variables

```bash
# Optional overrides
export MCP_CLIPROXY_URL="http://127.0.0.1:8317"
export MCP_OUTPUT_DIR="/custom/output/path"
export MCP_SSE_PORT="8318"
export MCP_DEFAULT_MODEL="gemini-3-pro-image-preview"
```

### 6.3 Claude Code Integration

```json
// ~/.claude/settings.json
{
  "mcpServers": {
    "media-gen": {
      "command": "python",
      "args": [
        "/home/node/workplace/AWCLOUD/CLAUDE/claude-code-image-generator/run_mcp.py",
        "--transport", "stdio"
      ],
      "env": {
        "MCP_OUTPUT_DIR": "/home/node/workplace/AWCLOUD/CLAUDE/claude-code-image-generator/+generated_images"
      }
    }
  }
}
```

---

## 7. Error Handling

### 7.1 Error Categories

| Category | Code Range | Description |
|----------|------------|-------------|
| Client Errors | 400-499 | Invalid parameters, missing required fields |
| Server Errors | 500-599 | CLIProxyAPI errors, generation failures |
| Timeout Errors | 504 | Generation timeout |
| Connection Errors | 503 | CLIProxyAPI not available |

### 7.2 Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "GENERATION_FAILED",
    "message": "Image generation failed: Model returned empty response",
    "details": {
      "model": "gemini-3-pro-image-preview",
      "prompt": "...",
      "http_status": 500
    }
  }
}
```

### 7.3 Error Codes

| Code | Description | Recovery |
|------|-------------|----------|
| `INVALID_PROMPT` | Empty or invalid prompt | Provide valid prompt |
| `INVALID_MODEL` | Unknown model name | Use supported model |
| `CLIPROXY_UNAVAILABLE` | Cannot connect to CLIProxyAPI | Start CLIProxyAPI |
| `GENERATION_TIMEOUT` | Request timed out | Retry or use faster model |
| `GENERATION_FAILED` | Model returned error | Check prompt/parameters |
| `FILE_NOT_FOUND` | Requested file doesn't exist | Check filename |
| `SAVE_FAILED` | Cannot save output | Check disk space/permissions |

---

## 8. Integration Guide

### 8.1 Claude Code CLI

After configuration in settings.json:

```bash
# Tools will be available automatically
claude "Generate an image of a sunset over mountains"

# Claude will use generate_image tool
```

### 8.2 Direct MCP Client

```python
# Example: Direct MCP client usage
import asyncio
from mcp import ClientSession, StdioServerParameters

async def main():
    server_params = StdioServerParameters(
        command="python",
        args=["run_mcp.py", "--transport", "stdio"]
    )

    async with ClientSession(server_params) as session:
        # List tools
        tools = await session.list_tools()
        print(tools)

        # Generate image
        result = await session.call_tool(
            "generate_image",
            {"prompt": "A beautiful sunset", "aspect_ratio": "16:9"}
        )
        print(result)

asyncio.run(main())
```

### 8.3 HTTP/SSE Client

```python
# Example: SSE client usage
import httpx
import json

# Start SSE connection
with httpx.stream("GET", "http://localhost:8318/sse") as response:
    # Send request
    httpx.post("http://localhost:8318/messages", json={
        "jsonrpc": "2.0",
        "method": "tools/call",
        "params": {
            "name": "generate_image",
            "arguments": {"prompt": "A sunset"}
        },
        "id": 1
    })

    # Read response from SSE stream
    for line in response.iter_lines():
        if line.startswith("data:"):
            data = json.loads(line[5:])
            print(data)
```

---

## 9. Implementation Roadmap

### Phase 1: Core Infrastructure (Priority: P0)

| Task | Description | Status |
|------|-------------|--------|
| 1.1 | Create project structure | Pending |
| 1.2 | Implement config.py | Pending |
| 1.3 | Implement CLIProxyAPI client | Pending |
| 1.4 | Implement basic MCP server | Pending |

### Phase 2: Image Tools (Priority: P0)

| Task | Description | Status |
|------|-------------|--------|
| 2.1 | Implement generate_image | Pending |
| 2.2 | Implement generate_image_batch | Pending |
| 2.3 | Test with Claude Code | Pending |

### Phase 3: Management Tools (Priority: P1)

| Task | Description | Status |
|------|-------------|--------|
| 3.1 | Implement list_models | Pending |
| 3.2 | Implement list_generated | Pending |
| 3.3 | Implement get_file_info | Pending |
| 3.4 | Implement delete_file | Pending |

### Phase 4: Transport Layer (Priority: P1)

| Task | Description | Status |
|------|-------------|--------|
| 4.1 | Implement stdio transport | Pending |
| 4.2 | Implement SSE transport | Pending |
| 4.3 | Implement dual mode | Pending |

### Phase 5: Edit Tools (Priority: P2)

| Task | Description | Status |
|------|-------------|--------|
| 5.1 | Implement edit_image | Pending |
| 5.2 | Implement inpaint | Pending |
| 5.3 | Implement outpaint | Pending |
| 5.4 | Implement upscale | Pending |

### Phase 6: Video Tools (Priority: P2)

| Task | Description | Status |
|------|-------------|--------|
| 6.1 | Implement generate_video | Pending |
| 6.2 | Implement get_video_status | Pending |
| 6.3 | Implement image_to_video | Pending |

**Note:** Phase 6 depends on CLIProxyAPI Veo support (GitHub Issue #94)

---

## 10. Testing Plan

### 10.1 Unit Tests

```text
tests/
├── test_config.py
├── test_client.py
├── test_tools/
│   ├── test_image.py
│   ├── test_video.py
│   ├── test_edit.py
│   └── test_management.py
└── test_transport/
    ├── test_stdio.py
    └── test_sse.py
```

### 10.2 Integration Tests

| Test | Description |
|------|-------------|
| IT-01 | Generate image via stdio |
| IT-02 | Generate image via SSE |
| IT-03 | Batch generation |
| IT-04 | Error handling |
| IT-05 | Claude Code integration |

### 10.3 Test Commands

```bash
# Run all tests
pytest tests/

# Run specific test
pytest tests/test_tools/test_image.py

# Integration test with Claude Code
claude "Generate a test image of a red circle"
```

---

## Appendix A: File Structure

```text
claude-code-image-generator/
├── mcp_server/
│   ├── __init__.py
│   ├── server.py
│   ├── config.py
│   ├── tools/
│   │   ├── __init__.py
│   │   ├── image.py
│   │   ├── video.py
│   │   ├── edit.py
│   │   └── management.py
│   ├── client/
│   │   ├── __init__.py
│   │   └── cliproxy.py
│   └── transport/
│       ├── __init__.py
│       ├── stdio.py
│       └── sse.py
├── +generated_images/
├── metadata/
├── tests/
│   ├── __init__.py
│   ├── test_config.py
│   ├── test_client.py
│   ├── test_tools/
│   └── test_transport/
├── image_gen.py          # Existing CLI script
├── run_mcp.py            # MCP server launcher
├── requirements.txt
├── design.md             # Original design
├── mcp.design.md         # This document
└── README.md
```

---

## Appendix B: Dependencies

```text
# requirements.txt

# MCP Protocol
mcp>=1.0.0

# HTTP Client
httpx>=0.25.0
aiohttp>=3.9.0

# Image Processing
Pillow>=10.0.0

# Utilities
python-dotenv>=1.0.0

# Testing
pytest>=7.0.0
pytest-asyncio>=0.21.0
```

---

---

> Full history: [mcp.changelog.md](../changelog/mcp.changelog.md)
