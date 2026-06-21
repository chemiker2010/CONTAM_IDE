# CONTAM_IDE

**更易上手的 [CONTAM](https://www.nist.gov/services-resources/software/contam) 桌面 IDE 与开源 Rust 工具链，用于建筑通风与污染物传输建模。**

CONTAM_IDE 与 ContamW 使用相同的 `.prj` 项目格式，基于 ContamX 运行仿真，并提供更清晰、更友好的编辑体验。技术栈：Rust、Tauri 2、React、Three.js。

[English README](README.md)

---

## 项目简介

CONTAM 是美国 NIST 开发的多区通风与室内空气质量仿真程序；ContamW 是其成熟的 Windows 图形界面，用于绘制建筑草图、编辑项目并运行仿真。

**CONTAM_IDE** 面向 CONTAM 生态的补充与推广，而非替代 ContamW。项目希望：

- **降低使用门槛** — 延续熟悉的 Plan 平面工作流，界面更清晰，校验反馈直观，支持中英文
- **促进 CONTAM 发展** — 跨平台可用、代码开放，让学生、研究人员和工程师更容易接触、使用和扩展 CONTAM
- **保持完全兼容** — 读写标准 `.prj` 文件，项目可在 ContamW 与 CONTAM_IDE 之间自由流转

此外，项目提供纯 Rust 的 PRJ 库与命令行工具，便于自动化、CI 与自定义工作流，在不改变文件格式的前提下拓展 CONTAM 的应用场景。
---

## 当前可用功能

### 配套工具（Rust）

以下组件供开发者与进阶用户扩展 CONTAM 能力，与 ContamW 共用同一套 `.prj` 数据模型：

| 组件 | 说明 |
|------|------|
| **contam-prj-parser** | 解析/写入 ContamW `.prj`，生成结构化 `PrjProject` 模型 |
| **contam-sim** | ContamX C API 的安全封装（setup、步进、快照采集） |
| **contam-sys** | 与 `contam.lib` / `libcontamlib.a` 的 FFI 层 |
| **contam-cli** | `info`、`validate`、`export`、`run`、`version` 等命令 |
| **contam-catalog** | 离线 SQLite 预设库，可合并进项目（天气、物种、气流元件） |

### 桌面 IDE（`contam-ide`）

聚焦日常建模 — 打开项目即可在 Plan 画布上操作，常用功能触手可及：

| 功能域 | 当前可用 |
|--------|----------|
| **项目读写** | 新建项目、打开/保存 `.prj`、内存编辑与快照同步 |
| **界面布局** | 类 VS Code 外壳：菜单栏、活动栏、侧边栏、底部面板、属性检查器 |
| **Plan 画布** | 由 PRJ 几何数据驱动的 2D 平面图（区域、路径、墙体、网格） |
| **绘图工具** | 选择、绘制区域、水平/竖向隔断墙、室外路径、区间路径、门、风机放置 |
| **编辑操作** | Delete/Backspace 删除区域或路径、复制楼层、编辑区域/路径/元件属性 |
| **校验** | 项目校验，结果展示于「问题」面板 |
| **配置面板** | 运行控制、天气、物种、CFD、风压、动力学、占用等 |
| **仿真** | 在 IDE 内运行 ContamX、逐步推送结果、仿真监视与时间轴 |
| **国际化** | 中英文界面 |

### 持续完善中

CONTAM_IDE 正在逐步覆盖更多 ContamW 工作流。常见建模与仿真任务已可用，以下功能仍在开发：

- 撤销 / 重做
- 外轮廓角点拖拽改尺寸
- 沿墙体移动路径
- PRJ 源码文本编辑（Source 标签页仍为占位）
- 风管网络与控制元件的画布编辑
- 更多 ContamW 图标类型与对话框

---

## 架构概览

```
.prj 文件  →  contam-prj-parser  →  PrjProject  →  编辑  →  保存
                                              ↓
                                    contam-ide（Tauri + React）
                                              ↓
                                    contam-sim  →  ContamX（contam.lib）
```

详细开发文档见 [`docs/`](docs/README.md)。

---

## 快速开始

### 环境要求

- **Rust**（stable）与 **Cargo**
- **Node.js 22+** 与 **npm**（仅 IDE 需要）
- **ContamX 静态库**（仿真必需）：将 `contam.lib`（Windows）或 `libcontamlib.a`（Linux）放到仓库根目录 — 见 [`libs/README.md`](libs/README.md)

解析器与 CLI 无需原生库即可使用。

### 解析器与 CLI

```bash
cargo test -p contam-prj-parser
cargo run -p contam-cli -- info fixtures/test_OneZoneSS.prj
cargo run -p contam-cli -- validate fixtures/test_OneZoneSS.prj --roundtrip
```

### 仿真（需要 `contam.lib`）

```bash
cargo test -p contam-sim
cargo run -p contam-cli -- run fixtures/test_OneZoneSS.prj -v
```

### 桌面 IDE

```bash
cd contam-ide
npm install
npm run tauri dev
```

### 目录库数据

```bash
cargo run -p contam-catalog --bin seed_local_data   # → resources/local_data.db
```

---

## 仓库结构

| 路径 | 说明 |
|------|------|
| `contam-prj-parser/` | PRJ 解析/写入引擎 |
| `contam-sim/` | ContamX 仿真封装 |
| `contam-sys/` | 原生库 FFI |
| `contam-cli/` | 命令行工具 |
| `contam-catalog/` | SQLite 预设目录 |
| `contam-ide/` | Tauri 桌面应用 |
| `fixtures/` | 测试用示例 `.prj` |
| `docs/` | 开发者文档 |

---

## 项目状态

CONTAM_IDE 仍在积极开发中。它与 ContamW、ContamX 协同使用：按任务选择合适的工具，通过标准 `.prj` 文件共享项目。欢迎反馈与贡献，一起让 CONTAM 更易用、更广泛地被采用。

欢迎提交 Issue 与贡献代码。
