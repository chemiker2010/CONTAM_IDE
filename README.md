# CONTAM_IDE
**A modern open-source integrated development environment (IDE) for CONTAM-based indoor air quality and airflow simulation workflows.** 

**An easier way to work with [CONTAM](https://www.nist.gov/services-resources/software/contam) — a cross-platform desktop IDE and open Rust toolchain for building airflow and contaminant transport models.**
 
CONTAM_IDE uses the same native `.prj` format as ContamW, runs on ContamX, and offers a clearer, more approachable editing experience. Built with Rust, Tauri 2, React, and Three.js.

[中文介绍](README.zh-CN.md)

---
## Status：Pre release.
**Core PRJ workflow is implemented. The project is currently validating compatibility across a broader set of CONTAM models before the first open-source release.**

## What is this?

CONTAM is NIST’s multizone airflow and indoor air quality simulation program. ContamW is its established Windows GUI for sketching buildings, editing projects, and running simulations.

**CONTAM_IDE** is a community-oriented companion to that ecosystem — not a replacement. It aims to:

- **Make CONTAM easier to use** — familiar Plan-view workflow, clearer layout, validation feedback, and bilingual UI
- **Grow the CONTAM community** — cross-platform access, open code, and tools that lower the barrier for students, researchers, and practitioners
- **Stay fully compatible** — read and write standard `.prj` files; projects can move between ContamW and CONTAM_IDE

The project also includes a pure Rust PRJ library and CLI, so automation, CI, and custom workflows can extend what CONTAM can do without changing the file format.

---

## What's available today

### Supporting tools (Rust)

These components help developers and power users build on CONTAM — they use the same `.prj` model as ContamW:

| Component | Status |
|-----------|--------|
| **contam-prj-parser** | Parse and write ContamW `.prj` files into a structured `PrjProject` model |
| **contam-sim** | Safe wrapper around the ContamX C API (setup, step, snapshots) |
| **contam-sys** | FFI layer to `contam.lib` / `libcontamlib.a` |
| **contam-cli** | `info`, `validate`, `export`, `run`, `version` commands |
| **contam-catalog** | Offline SQLite presets merged into projects (weather, species, airflow elements) |

### Desktop IDE (`contam-ide`)

Focused on everyday modeling tasks — open a project and start drawing without hunting through menus:

| Area | Available today |
|------|-----------------|
| **Project I/O** | New project, open/save `.prj`, in-memory editing with snapshot sync |
| **Layout** | VS Code–style shell: menu bar, activity bar, sidebar, bottom panel, property inspector |
| **Plan canvas** | 2D floor plan from PRJ geometry (zones, paths, walls, grid) |
| **Drawing tools** | Select, draw zones, horizontal/vertical partition walls, outdoor paths, interzone paths, doors, fan placement |
| **Editing** | Delete zones/paths (Delete/Backspace), copy level, edit zone/path/element properties |
| **Validation** | Project validation with Problems panel |
| **Configuration** | Run control, weather, species, CFD, wind pressure, kinetic, occupancy panels |
| **Simulation** | Run ContamX from IDE, stream step results, sim monitor and timeline |
| **i18n** | English and Chinese UI |

### Still in progress

CONTAM_IDE is evolving toward broader ContamW workflow coverage. Common tasks work today; more advanced features are on the way:

- Undo / redo
- Exterior building outline drag-resize
- Move path along wall
- PRJ source text editor (Source tab is a placeholder)
- Duct network and control-element drawing on canvas
- Additional ContamW icon types and dialogs

---

## Architecture (brief)

```
.prj file  →  contam-prj-parser  →  PrjProject  →  edit  →  save
                                              ↓
                                    contam-ide (Tauri + React)
                                              ↓
                                    contam-sim  →  ContamX (contam.lib)
```

Detailed docs: [`docs/`](docs/README.md)

---

## Quick start

### Requirements

- **Rust** (stable) and **Cargo**
- **Node.js 22+** and **npm** (for the IDE only)
- **ContamX static library** for simulation: copy `contam.lib` (Windows) or `libcontamlib.a` (Linux) to the repo root — see [`libs/README.md`](libs/README.md)

Parser and CLI work without the native library.

### Parser & CLI

```bash
cargo test -p contam-prj-parser
cargo run -p contam-cli -- info fixtures/test_OneZoneSS.prj
cargo run -p contam-cli -- validate fixtures/test_OneZoneSS.prj --roundtrip
```

### Simulation (requires `contam.lib`)

```bash
cargo test -p contam-sim
cargo run -p contam-cli -- run fixtures/test_OneZoneSS.prj -v
```

### Desktop IDE

```bash
cd contam-ide
npm install
npm run tauri dev
```

### Catalog database

```bash
cargo run -p contam-catalog --bin seed_local_data   # → resources/local_data.db
```

---

## Repository layout

| Path | Description |
|------|-------------|
| `contam-prj-parser/` | PRJ parse/write engine |
| `contam-sim/` | ContamX simulation wrapper |
| `contam-sys/` | Native library FFI |
| `contam-cli/` | Command-line tool |
| `contam-catalog/` | SQLite preset catalog |
| `contam-ide/` | Tauri desktop application |
| `fixtures/` | Sample `.prj` files for tests |
| `docs/` | Developer documentation |

---

## Status

CONTAM_IDE is under active development. It works alongside ContamW and ContamX: use whichever tool fits your task, and share projects through standard `.prj` files. Feedback and contributions help make CONTAM more accessible to everyone.

Contributions and issue reports are welcome.
