![](banner.png)

<h1 align="center">Aurora Recovery Project (AURP)</h1>

<p align="center">
AURP 基于 TWRP 16 recovery 底座，将由 Flutter 驱动的界面与运行时带入 Android Recovery 环境。
</p>

<p align="center">
  <a href="https://github.com/AuroraRecoveryProject/aurora_recovery">Recovery UI</a>
  ·
  <a href="./README.md">English</a>
</p>

## 这是什么

Aurora Recovery Project 是一个 Android recovery 项目，它将 TWRP 的底层 recovery 能力与现代 Flutter 应用层结合在一起。

TWRP 仍然负责 recovery 环境本身：启动、分区、刷写、解密、ADB、fastbootd、MTP、sideload、设备 bring-up 和硬件集成。Flutter 则用于面向用户的运行时与界面，使 recovery UI 可以用更现代的框架构建，并获得比传统 recovery UI 开发更快的迭代效率。

当前的 AURP 仍然是一个初步搭建的框架，并不具备完整 TWRP GUI 的全部功能。现阶段它需要依赖原始 TWRP recovery 环境运行，在这个环境之上启动 Flutter runtime 与 AURP 界面。后续目标是在 recovery 底层能力保持可用的前提下，逐步完整替换 TWRP 原始 GUI。

AURP 并不是把 Flutter 当作普通 Android App 来运行。Android Recovery 环境没有 SurfaceFlinger，也没有标准 Android 应用进程模型。因此，AURP 使用面向 recovery 的 embedder 路径，将 Flutter 帧直接渲染到 recovery 显示后端。

## 渲染架构

![](flutter_recovery_architecture.excalidraw.svg)

## 解决什么问题

- **降低 TWRP GUI 开发与迭代成本**：传统 TWRP GUI 开发流程复杂，界面修改、资源调整和功能验证往往需要较长的编译、打包、刷入和重启周期。AURP 将 Flutter 的开发方式引入 recovery 场景，热重载、热重启可以快速搭建 UI。
- **把成熟的 Flutter 生态带入 recovery 环境**：Flutter 的状态管理、国际化、FFI，以及 pub.dev 上大量成熟的第三方库。例如 [xterm.dart](https://github.com/TerminalStudio/xterm.dart) 能够有更完整的终端序列，对 vim/nano 等依赖大量终端序列的二进制支持更，可以直接使用 FFI 插件，例如 [flutter_pty](https://github.com/TerminalStudio/flutter_pty)。
- **保留底层 native 能力**：通过 FFI 和 recovery 侧 native 组件，AURP 可以继续访问底层分区、设备节点、命令行工具和硬件接口，不把 recovery 能力封死在纯 UI 层。例如目前已经实现的亮度调节、设置持久化、ROM 刷入等能力，都是在 AURP 的 Flutter 界面之下复用和集成 TWRP 原有的底层实现，而不是简单照搬 TWRP 原始 GUI。
- **支持多设备同步开发与 UI 适配**：通过配置 `custom_device.json`，当前框架可以同时开发多台设备，并将 UI 快速适配到不同设备上。
- **支持 CPU 或 GPU 渲染**：CPU 渲染不依赖任何 system/vendor 分区。**GPU 渲染依赖 vendor 分区，仅在 Snapdragon 8 Elite / 骁龙 8 Elite Gen 5 平台测试。**

## 项目组成

- **[aurora_recovery](https://github.com/AuroraRecoveryProject/aurora_recovery)**  
  公开的 Flutter recovery GUI 应用，也是 AURP 当前主要的可见入口。
- **[TWRP 16](https://github.com/TWRP-Test/android_bootable_recovery)**  
  提供 recovery 启动环境、分区、刷写、解密、ADB、MTP、sideload 和 fastbootd 等底层能力。
- **Flutter recovery runtime（闭源）**  
  在 Android Recovery 用户空间中运行 Flutter 界面，不依赖 Android SurfaceFlinger 或普通 Android App 运行时。
- **Recovery Display Embedder**  
  将 Flutter 帧通过 recovery 显示路径呈现到屏幕。

## Recovery 工具

AURP 也包含一些小型 recovery 用户空间实验和工具适配，例如终端/运行时测试、网络工具和命令行辅助组件。

例如 `cmatrix`、`curl`、`dhcpcd` 以及类似组件，部分会被适配或测试于受限的 Android Recovery 环境。

## 源码可见性

AURP 的 GUI 项目目前公开；Flutter embedding、engine/runtime、工具链和部分设备集成组件目前为闭源或内部组件。

## 开发状态

AURP 目前是一个早期框架和运行时验证环境，而不是功能完整的 TWRP 替代品。

当前已经围绕 Flutter 在 Android Recovery 中的运行和基础系统能力调用做了初步验证。GPU 渲染目前仅在 Snapdragon 8 Elite 和骁龙 8 Elite Gen 5 平台进行测试；音频能力仅在 ossi 的闭源设备树中支持；WLAN 需要依赖设备树集成，相关方案记录在 [TWRP QCOM WIFI 设备树集成方案](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/main/docs/TWRP/TWRP%20QCOM%20WIFI%20%E8%AE%BE%E5%A4%87%E6%A0%91%E9%9B%86%E6%88%90%E6%96%B9%E6%A1%88.md)。

由于个人时间极度有限，同时还需要维护多个相关项目，AURP 当前会进入低频维护状态，短期内不会持续推进大规模功能开发。

如果后续收集到较多真实使用反馈、明确需求或外部参与意愿，项目会根据反馈优先级继续推进功能补齐和架构完善。

## 文档

TWRP 编译文档：[TWRP 编译记录](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20%E7%BC%96%E8%AF%91%E8%AE%B0%E5%BD%95.md)

完整文档目录：[docs](https://github.com/AuroraRecoveryProject/aurora_recovery/tree/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs)

### Flutter

- [ARP 触控 Slop 排查记录](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/Flutter/ARP%20%E8%A7%A6%E6%8E%A7%20Slop%20%E6%8E%92%E6%9F%A5%E8%AE%B0%E5%BD%95.md)
- [Flutter Android 字体加载流程](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/Flutter/Flutter%20Android%20%E5%AD%97%E4%BD%93%E5%8A%A0%E8%BD%BD%E6%B5%81%E7%A8%8B.md)
- [刷入 Magisk 中整个 UI 加环境崩溃](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/Flutter/%E5%88%B7%E5%85%A5%20Magisk%20%E4%B8%AD%E6%95%B4%E4%B8%AA%20UI%20%E5%8A%A0%E7%8E%AF%E5%A2%83%E5%B4%A9%E6%BA%83.md)
- [调试底层库崩溃堆栈](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/Flutter/%E8%B0%83%E8%AF%95%E5%BA%95%E5%B1%82%E5%BA%93%E5%B4%A9%E6%BA%83%E5%A0%86%E6%A0%88.md)

### TWRP

- [TWRP OnePlus Pad2Pro 修改记录](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20OnePlus%20Pad2Pro%20%E4%BF%AE%E6%94%B9%E8%AE%B0%E5%BD%95.md)
- [TWRP QCOM WIFI 手动启动全流程(OEM)](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20QCOM%20WIFI%20%E6%89%8B%E5%8A%A8%E5%90%AF%E5%8A%A8%E5%85%A8%E6%B5%81%E7%A8%8B%28OEM%29.md)
- [TWRP QCOM WIFI 手动启动全流程](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20QCOM%20WIFI%20%E6%89%8B%E5%8A%A8%E5%90%AF%E5%8A%A8%E5%85%A8%E6%B5%81%E7%A8%8B.md)
- [TWRP QCOM WIFI 设备树集成方案](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20QCOM%20WIFI%20%E8%AE%BE%E5%A4%87%E6%A0%91%E9%9B%86%E6%88%90%E6%96%B9%E6%A1%88.md)
- [TWRP WIFI 对比](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20WIFI%20%E5%AF%B9%E6%AF%94.md)
- [TWRP 分支演化分析](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20%E5%88%86%E6%94%AF%E6%BC%94%E5%8C%96%E5%88%86%E6%9E%90.md)
- [TWRP 刷写 ROM 分析](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20%E5%88%B7%E5%86%99%20ROM%20%E5%88%86%E6%9E%90.md)
- [TWRP 编译记录](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20%E7%BC%96%E8%AF%91%E8%AE%B0%E5%BD%95.md)
- [TWRP 配置分析](https://github.com/AuroraRecoveryProject/aurora_recovery/blob/23466e5a8f4aa4b8ba7497ab0ba020217f6061e8/docs/TWRP/TWRP%20%E9%85%8D%E7%BD%AE%E5%88%86%E6%9E%90.md)

额外的构建笔记、运行时工具、设备 bring-up 记录和实现文档位于当前尚未公开的内部仓库中。
