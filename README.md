<div align="center">
  <img src="public/assets/brand/desic-terminal-icon.png" width="88" alt="Desic Terminal" />

  <h1>Desic Terminal · Ubuntu 适配版</h1>

  <p><strong>AI 原生的 OKX USDT 永续合约交易终端 —— Ubuntu 适配 fork</strong></p>
  <p>每日自动同步上游最新版，保持 Ubuntu 22.04+（x64）开箱可构建、可打包、可运行。</p>

  <p>
    <strong><a href="https://github.com/xiazhi88/Desic-Terminal">上游仓库</a></strong>
    · <a href="https://desicterminal.cn/">官方网站</a>
    · <a href="https://github.com/xiazhi88/Desic-Terminal/releases">上游版本发布</a>
  </p>

  <p>
    <img alt="Ubuntu 22.04+" src="https://img.shields.io/badge/Ubuntu-22.04%2B-E95420?logo=ubuntu&logoColor=white" />
    <img alt="Tauri 2" src="https://img.shields.io/badge/Tauri-2-24C8DB?logo=tauri&logoColor=white" />
    <img alt="React 19" src="https://img.shields.io/badge/React-19-149ECA?logo=react&logoColor=white" />
    <img alt="Rust 2021" src="https://img.shields.io/badge/Rust-2021-000000?logo=rust" />
    <img alt="deb / AppImage" src="https://img.shields.io/badge/%E5%8C%85%E6%A0%BC-deb%20%7C%20AppImage-6D5DFB" />
  </p>
</div>

## 本仓库是什么

[Desic Terminal](https://github.com/xiazhi88/Desic-Terminal) 官方支持 Windows 与 macOS，本仓库在其基础上补齐 **Ubuntu（x64）** 的本地开发与本地打包支持，主要适配改动：

- 随包资源准备脚本支持 linux：Node sidecar（官方 SHASUMS256 校验）与打包 CPython 运行时（python-build-standalone，官方 digest 校验）
- Linux 窗口适配：splash 不启用系统级透明（`tauri.linux.conf.json`），规避无合成器/部分 Wayland 场景黑底
- 字体栈补 Ubuntu 常见字体回退（Liberation Sans Narrow / Ubuntu / Noto Sans CJK SC）
- 帮助中心、smoke 工具与数据目录识别 Linux（XDG 规范路径）
- 应用内更新对 deb 安装给出明确提示（自动更新仅支持 AppImage）

本仓库不改变任何产品行为，只做平台适配；产品完整功能介绍、教程与截图请看[上游仓库](https://github.com/xiazhi88/Desic-Terminal)。

## 在 Ubuntu 上构建

```bash
# 1. 系统依赖（Ubuntu 22.04 / 24.04）
sudo apt install -y libwebkit2gtk-4.1-dev libgtk-3-dev build-essential curl wget file \
  libxdo-dev libssl-dev libayatana-appindicator3-dev librsvg2-dev patchelf

# 2. 前端依赖（需要 Node.js 20+ 与 Rust stable）
npm install

# 3. 一键构建 deb / AppImage（自动准备随包 Node/Python 运行时并校验官方哈希）
npm run tauri build -- --bundles deb,appimage
```

安装运行：

```bash
sudo dpkg -i "src-tauri/target/release/bundle/deb/Desic Terminal_<版本>_amd64.deb"
```

开发模式：`npm run tauri dev`。

> 说明：应用内自动更新的签名材料只在上游 CI，本仓库本地构建的 deb 不含更新签名，不影响使用；deb 安装时应用内更新会提示手动下载，AppImage 安装支持应用内更新。

## WSL2 已知问题

部分 WSL2 环境（CPUID 宣告 AVX2 但未启用 XSAVE）会出现"非法指令"崩溃或窗口空白，需带以下环境变量启动；正常物理机与常规虚拟机**不需要**：

```bash
export WEBKIT_DISABLE_DMABUF_RENDERER=1   # WebKitGTK 在无 GPU 环境的渲染降级
export JSC_useJIT=0                       # 禁用会生成 AVX 指令的 JIT
export JSC_useWebAssembly=0
```

若主进程仍报非法指令，还需要 LD_PRELOAD 一个 16 字节原子操作的降级 shim（详见 [开发规范](docs/development-guidelines.md) 的 Linux 章节）。

## 同步策略

`main` 分支每日自动执行：同步上游最新提交 → 合并 → 验证（`npm run build` / `cargo check --workspace` / 配置安全冒烟）→ 编译 amd64 deb → 推送本仓库。上游出现无法自动处理的冲突时会暂停并保留现场，等待人工处理。

## 文档

与上游保持同一套文档：

| 文档 | 内容 |
| --- | --- |
| 📖 [**入门教程**](docs/getting-started.md) | 从安装、配置模拟盘、第一笔交易到 AI 助手与情报 |
| 🤖 [**AI 自动化指南**](docs/ai-automation-guide.md) | Profile、唤醒条件、Skill 版本与编排、复盘与迭代 |
| 📈 [**系统化策略指南**](docs/systematic-strategy-guide.md) | Python 策略编程、回测、参数调优 |
| 🔧 [策略协议规范](docs/systematic-python-strategy-protocol.md) | 策略运行时协议与安全边界 |
| 🏗 [产品规范](PRODUCT.md) | 产品边界与设计决策 |
| 🛠 [开发规范](docs/development-guidelines.md) | 架构、安全与平台约束 |

## 许可

同上游仓库，见 [LICENSE](LICENSE)。
