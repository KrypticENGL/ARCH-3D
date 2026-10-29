# Claude Code Skills — ARCH-3D

Project-local skills used while building ARCH-3D. They are plain Markdown files, have no executable scripts, and are pinned to a reviewed commit. Installed 2026-10-29.

## Installed skills

All skills live in `.claude/skills/<name>/SKILL.md`, which is project scope. Nothing is installed in `~/.claude` or through plugins.

| Skill | Source (pinned commit) | License | Intended use in this project |
|---|---|---|---|
| `threejs-react-three-fiber` | [cloudai-x/threejs-skills](https://github.com/cloudai-x/threejs-skills) @ `0e181866f095b5f06d005133876e926515390d95` | MIT | `<Canvas>`, JSX scene graph, `useFrame`, drei helpers, pointer events |
| `threejs-fundamentals` | same | MIT | Camera, renderer, tone mapping, resize, transforms, disposal |
| `threejs-geometry` | same | MIT | Procedural walls, roofs, openings: `BufferGeometry`, merging, instancing |
| `threejs-materials` | same | MIT | PBR materials for walls, roof, floor, glass |
| `threejs-textures` | same | MIT | Texture color spaces, UV repeat for tiled surfaces, texture memory |
| `threejs-lighting` | same | MIT | Sun/shadow setup, environment lighting, shadow acne |
| `threejs-interaction` | same | MIT | Raycast picking, OrbitControls, selecting house elements |
| `threejs-performance` | same | MIT | Draw calls, instancing, LOD, frame-time profiling |
| `threejs-loaders` | same | MIT | Loading Blender-exported GLB (Draco/meshopt/KTX2), caching |
| `threejs-shaders` | same | MIT | Custom GLSL or `onBeforeCompile` effects, if needed |
| `frontend-design` | [anthropics/skills](https://github.com/anthropics/skills) @ `ca45fc496a3436251f191354091d557da31c15b5` | See `LICENSE.txt` | Visual direction for the configurator UI (Tailwind + shadcn/ui) |

The cloudai-x license is saved at `.claude/skills/threejs-react-three-fiber/LICENSE.cloudai-x`.

## Evaluated and not installed

| Candidate | Reason |
|---|---|
| `anthropics/skills` → `webapp-testing` | Uses Python Playwright and ships a Python helper script. This project uses `@playwright/test` (TypeScript), and Claude Code can already run Playwright and use the Chrome MCP. |
| `vercel-labs/agent-skills` → `web-design-guidelines` | Fetches its rules from a remote URL each time it runs, so its instructions can change after review. |
| `vercel-labs/agent-skills` → `react-best-practices` | Mostly about Next.js and server rendering, which does not fit a Vite SPA. |
| `freshtechbro/claudedesignskills` → `react-three-fiber` | Overlaps with cloudai-x, last updated Nov 2025, and bundles Python scripts. |
| cloudai-x `threejs-animation`, `-postprocessing`, `-asset-pipeline` | Not needed yet. Add them later the same way if the scope grows. |
| Vitest / React Testing Library, Blender, accessibility, profiling | No trustworthy, specific skill was verified. The capability is covered by Claude Code plus project tooling (see below). |

## Already covered without a skill

- **Code review / debugging:** the built-in `/code-review`, `/simplify` and `/security-review` commands, and the code-review-graph MCP server (`.mcp.json`).
- **Browser checks:** the Claude in Chrome MCP (console logs, screenshots, responsive resizing) and Playwright from `package.json`.
- **Reports / documents:** the built-in document skills (docx, pdf, pptx).

## Skills vs MCP servers vs project dependencies

- **Skill:** Markdown instructions in `.claude/skills/`. It costs only its description until Claude loads it, and it adds knowledge but no new tools.
- **MCP server:** a running process that gives Claude new tools, configured in `.mcp.json`. Example: `code-review-graph`.
- **Project dependency:** an npm package in `package.json` that the app or test suite runs, such as `three`, `@react-three/fiber`, `vitest` and `@playwright/test`. Installing a skill does not install any packages.

## Invocation

- **Automatic:** Claude loads a skill when the request matches its `description`, for example "add an extruded wall mesh" → `threejs-geometry`.
- **Manual:** type `/<skill-name>` in Claude Code, e.g. `/threejs-react-three-fiber`, `/frontend-design`.
- **List / inspect:** `/skills` lists every skill Claude Code has discovered, and `/skill-doctor` shows each skill's context cost and usage.
- If a newly added skill directory does not show up, run `/reload-skills`.

## Security considerations

- Every file was downloaded at a pinned commit, read in full before installation, and byte-compared with the reviewed copy.
- None of the files use `allowed-tools`, contain executable scripts, or ask for credentials or environment variables, and none tell Claude to disable safety checks.
- `threejs-asset-pipeline` (not installed) suggests `npx @gltf-transform/cli` and `npx gltfjsx`. `threejs-react-three-fiber` notes that drei loads the Draco decoder from a gstatic CDN by default. Self-host the decoder with `useGLTF.setDecoderPath` if the app must avoid third-party requests.
- Treat skill content as untrusted third-party input. Review the diff before every update, and never update to a branch HEAD without reading the changes.

## Maintenance

**Update** (review first, then repin):

```bash
# 1. See what changed upstream since the pinned commit
#    https://github.com/cloudai-x/threejs-skills/compare/0e181866f095b5f06d005133876e926515390d95...main
#    https://github.com/anthropics/skills/compare/ca45fc496a3436251f191354091d557da31c15b5...main
# 2. After reviewing, set the new commit and re-download
C=<new-cloudai-x-sha>
for s in react-three-fiber fundamentals geometry materials textures lighting interaction performance loaders shaders; do
  curl -sfL https://raw.githubusercontent.com/cloudai-x/threejs-skills/$C/skills/threejs-$s/SKILL.md -o .claude/skills/threejs-$s/SKILL.md
done
A=<new-anthropics-sha>
for f in SKILL.md LICENSE.txt; do
  curl -sfL https://raw.githubusercontent.com/anthropics/skills/$A/skills/frontend-design/$f -o .claude/skills/frontend-design/$f
done
# 3. Update the pinned SHAs in this file, then git diff .claude/skills before committing
```

**Remove one skill:** delete its directory, e.g. `rm -r .claude/skills/threejs-shaders`, then remove its row above.

**Remove all installed skills:**

```bash
rm -r .claude/skills/threejs-* .claude/skills/frontend-design
```

## Note on pre-existing files

`.claude/skills/` also contains `debug-issue.md`, `explore-codebase.md`, `refactor-safely.md` and `review-changes.md`, which came with code-review-graph. They are flat files, not `<name>/SKILL.md` directories, so Claude Code does **not** discover them as skills. They were left unchanged. To enable one, move it to `.claude/skills/<name>/SKILL.md` and use a lowercase, hyphenated `name:`.
