# AI 3D Production Agent

## Purpose

Turn natural-language visual requirements into production-ready Blender scenes using the existing local Blender MCP Pro server.

This layer is an orchestration and quality-control layer. It does not replace Blender MCP and does not require a second Blender bridge.

## Architecture

Qwen3.5 4B (local)
→ OpenCode agent
→ Blender MCP Pro
→ Blender 5.2.2
→ render / inspection
→ iterative refinement
→ GLB/GLTF export

Optional:
Qwen2.5-VL 7B (local) → render/image evaluation

## Production stages

### 1. Scene discovery
Always inspect Blender and the scene before changes.

### 2. Visual decomposition
Convert the request into:
- subject and silhouette
- dimensions/proportions
- geometry/detail
- materials/PBR
- lighting/environment
- camera/composition
- animation if requested
- web/export requirements

### 3. Procedural construction
Prefer typed MCP tools and batch operations.

Examples:
- primitives + modifiers for hard-surface forms
- node-tree construction for materials
- geometry nodes for repeated/scattered detail
- cameras/lights through dedicated tools
- workflow tools for common studio/product setups

### 4. Visual QC
After meaningful changes:
- render an image or take a viewport screenshot
- inspect composition, silhouette, lighting and materials
- run mesh diagnostics where appropriate

### 5. Refinement
Fix only the largest visible problems first.

Suggested order:
1. silhouette/proportion
2. camera/composition
3. lighting
4. materials
5. secondary detail
6. optimization

### 6. Web optimization
For Three.js/R3F:
- export GLB/GLTF
- apply modifiers and triangulate only to the export copy
- avoid unnecessary polygon density
- keep texture resolution practical
- verify the exported asset

## Hardware-aware rules

The primary machine is CPU-only for Ollama inference. Therefore:
- keep Qwen3.5 4B as the primary orchestration model
- do not require large local 3D generative models
- use procedural Blender generation first
- use local vision analysis only when useful
- avoid cloud dependencies unless explicitly requested

## Important limitation

Qwen3.5 4B is the agent/orchestration model. It is not itself a mesh-generation or texture-generation model.

If dedicated local generative models are added later, they should be optional workers called by the production agent.

## Phase 2 success criteria

The agent is considered operational when it can take a request such as:

"Create a realistic premium burger product scene for a website"

and autonomously:
1. inspect Blender
2. construct the scene
3. create realistic materials
4. light and frame the scene
5. render it
6. inspect the result
7. refine obvious issues
8. export a web-ready GLB when requested
9. verify the result
