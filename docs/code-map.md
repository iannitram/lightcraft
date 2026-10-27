# Code map (our fork)

The code is upstream's Cargo workspace. Upstream's committed architecture summary is `docs/architecture.md` (layers L0-L6, enforced by `cargo xtask layers` inside `cargo xtask ci`); read it first. This map adds how the modules connect and what differs per platform (R04).

## Modules

| Path | Crate | Layer | Owns | Platform notes |
|---|---|---|---|---|
| `crates/geom`, `color`, `raster`, `tiff` | lightcraft-* | L0 | Matrices and warps, colour spaces, float image buffers, TIFF/IFD | pure, all |
| `crates/fetch` | lightcraft-fetch | L0 | Pinned HTTPS model downloads | all |
| `crates/sysmem` | lightcraft-sysmem | L0 | Returns freed allocator memory to the OS; the only `unsafe` crate | per OS (`malloc_zone_pressure_relief` on macOS) |
| `crates/raw` | lightcraft-raw | L1 | Raw decoders per maker (`src/vendor/*.rs`: orf, nef, arw, cr3, ...), demosaic, DNG colour model | pure, all |
| `crates/codecs`, `heif` | lightcraft-codecs, -heif | L1 | Standard codecs, ICC, encoders; optional HEIC | pure, all |
| `crates/meta` | lightcraft-meta | L1 | EXIF, XMP, IPTC | pure, all |
| `crates/develop` | lightcraft-develop | L1 | The non-destructive edit model, control specs, presets | pure, all |
| `crates/denoise-core`, `denoise`, `faces`, `scenes`, `slideshow`, `inpaint`, `geocode` | lightcraft-* | L1 | AI denoise, face detection, demo scenes, slides, hole filling, offline geocoding | pure, all (CPU inference) |
| `crates/testkit` | lightcraft-testkit | dev-only | Test tooling: a test that cannot run here records why, so `cargo xtask ci` reports it | pure, all |
| `crates/lensdb` | lightcraft-lensdb | L2 | Lens profile models and lensfun-format loader | pure, all |
| `crates/pipeline` | lightcraft-pipeline | L2 | The develop pipeline, CPU reference | pure, all |
| `crates/catalog`, `preview`, `merge`, `segment` | lightcraft-* | L3 | Library and persistence, preview cache and job pool, HDR/panorama merge, SAM 3 segmentation | all |
| `crates/gpu` | lightcraft-gpu | L3 | wgpu compute twin of the pipeline | Metal (macOS), Vulkan (Linux), DX12/Vulkan (Windows); proven on Apple and one AMD GPU only |
| `crates/engine` | lightcraft-engine | L4 | Session, command registry, history, render/export jobs, import, Lightroom catalog import | all |
| `crates/ui-egui`, `mcp` | lightcraft-ui-egui, -mcp | L5 | The Lightroom-style UI; the MCP server | all; native menus and file dialogs differ per OS |
| `apps/lightcraft`, `lightcraft-cli`, `lightcraft-web` | | L6 | Desktop app, CLI, WASM build | packaging in `packaging/` per OS |

## How they connect

```mermaid
flowchart TD
  app[apps/lightcraft] --> ui[ui-egui] --> engine
  cli[apps/lightcraft-cli] --> engine & mcp
  mcp --> engine
  engine --> catalog & pipeline & gpu & preview & merge & segment & lensdb & denoise & faces
  catalog --> develop & meta & raw
  gpu --> pipeline & develop & color
  pipeline --> raw & develop & inpaint & color
  merge --> raw & codecs & meta
  raw --> meta & tiff & color & raster
  codecs --> heif & color & raster
```

## Mac, PC and Linux

- CI: upstream's `ci.yml` runs `cargo xtask ci` on Linux x64 through the shared gate workflow; FreeBSD runs `cargo test`; macOS and Windows only build packages (gap 9). Our build issue adds macOS and Windows test jobs.
- Platform code sits behind `cfg(target_os)` in `sysmem`, `ui-egui` (menus, dialogs, settings) and `gpu` backends; a change there gets a `platform:*` label.
- Display profiles: macOS reads the screen's, Linux reads colord's, Windows has none yet (gap 22).
