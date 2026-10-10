# Clash-Client-Legacy

[English](README.md) · 简体中文

[![当前维护仓库](https://img.shields.io/badge/ProjectClash-%E5%BD%93%E5%89%8D%E7%BB%B4%E6%8A%A4%E4%BB%93%E5%BA%93-2563EB?logo=github)](https://github.com/ProjectClash/Clash-Client)
[![官方网站](https://img.shields.io/badge/Website-%E5%AE%98%E7%BD%91-2563EB)](https://clash.md/)
[![客户端下载](https://img.shields.io/badge/App_Store-%E4%B8%8B%E8%BD%BD-black?logo=apple&logoColor=white)](https://apps.apple.com/app/id6794257189)

> [!IMPORTANT]
> **后续开发已迁至 [Clash-Client](https://github.com/ProjectClash/Clash-Client)。**
>
> 本仓库原位于 `TokenPLS/Hako-Client`，于 2026 年 10 月 10 日转入 ProjectClash，作为历史档案保留，不再更新代码或发布新版本。
>
> 最新源码、编译说明、版本发布及[新问题反馈](https://github.com/ProjectClash/Clash-Client/issues)请前往当前维护仓库。仍待处理的反馈已连同讨论转移，原 Issue 链接会跳转至新位置。

历史提交、贡献者署名、分支、标签、发行版本和下载附件均予以保留。Star 与 Fork 关系仍属于本历史仓库。下方保留的旧名称和编译说明对应历史版本，当前使用方式请以新仓库为准。

<details>
<summary>历史 README</summary>

# Clash for Apple Platforms

[English](README.md) · 简体中文

[![官方网站](https://img.shields.io/badge/%E5%AE%98%E7%BD%91-%E8%AE%BF%E9%97%AE-2563EB)](https://clash.md/)
[![App Store 下载](https://img.shields.io/badge/App_Store-%E4%B8%8B%E8%BD%BD-black?logo=apple&logoColor=white)](https://apps.apple.com/app/id6794257189)
[![Telegram 频道](https://img.shields.io/badge/Telegram-%E9%A2%91%E9%81%93-26A5E4?logo=telegram&logoColor=white)](https://t.me/clashbyhako)
[![Telegram 交流群](https://img.shields.io/badge/Telegram-%E4%BA%A4%E6%B5%81%E7%BE%A4-26A5E4?logo=telegram&logoColor=white)](https://t.me/+t__WNRvjUbk3M2Nl)

基于 Hako 内核的原生规则代理客户端，适用于 iPhone、iPad、Mac 和 Apple TV。

安装官方应用请使用 App Store 链接。以下说明面向需要从源码构建的开发者。

## 关于本仓库

本仓库包含 Apple 各平台应用、扩展、共享库及构建所需资源。[Hako 内核](https://github.com/TokenPLS/Hako)和 [Adapter 组件](https://github.com/TokenPLS/Hako-Adapter)位于独立仓库，依赖源码提交固定在 [`Dependencies.lock.json`](Dependencies.lock.json) 中。

| 目录 | 内容 |
| --- | --- |
| `apple/HakoClient` | 各平台应用、扩展与 XcodeGen 工程配置 |
| `apple/HakoClientKit` | 共享配置与档案模型 |
| `apple/HakoClientUI` | 共享界面组件 |
| `apple/HakoMacClient` | macOS 组件 |

当前源码分发处于预发布阶段。App Store 应用版本与本仓库检出的源码分别管理；复现构建时请固定源码提交。

iOS 和 macOS 工程通过共享框架封装静态 Core；工程生成时会保留所需的头文件复制步骤。客户端使用的能力以所固定的 Kernel 和 Adapter 提交为准；iOS/tvOS 的 SDK 不包含 EasyTier。

## 从源码构建

### 环境要求

- macOS、Xcode 27.0，以及 iOS、macOS、tvOS SDK。
- 可在命令行使用的 XcodeGen 和 Git。
- 启用自动工具链选择的 Go，或安装固定内核绑定模块所选择的 Go 1.26.6 工具链。
- Python 3 和 PyYAML。

### 准备工程

```sh
git clone https://github.com/TokenPLS/Hako-Client.git
cd Hako-Client
python3 -m venv .build/python-env
source .build/python-env/bin/activate
python3 -m pip install PyYAML
python3 scripts/bootstrap.py
python3 scripts/configure.py
```

首次准备依赖时会获取固定提交的公开内核与 Adapter 源码，安装固定版本的 gomobile 工具，并构建五切片 SDK。此过程需要网络，可能耗时数分钟。配置脚本随后生成 Xcode 工程。

打开 `apple/HakoClient/HakoClient.xcodeproj`，选择相应构建方案：

| 平台 | 构建方案 |
| --- | --- |
| iPhone / iPad | `HakoClient` |
| Apple TV | `HakoTV` |
| Mac | `HakoMac` |

不签名编译 iOS 模拟器版本：

```sh
xcodebuild -project apple/HakoClient/HakoClient.xcodeproj \
  -scheme HakoClient -configuration Release \
  -destination 'generic/platform=iOS Simulator' CODE_SIGNING_ALLOWED=NO build
```

### 签名自己的构建

设置自己的 Bundle ID 前缀与 Apple Developer Team ID：

```sh
python3 scripts/configure.py --bundle-base org.yourname.clash --team YOURTEAMID
```

在 Xcode 中为应用和扩展配置签名与所需能力，包括 Network Extensions、App Groups，以及实际使用的 iCloud 能力。仓库不包含证书或描述文件。无签名构建只验证编译；真机安装需要自己的签名配置。

## 问题反馈

应用问题请提交到 [Issues](https://github.com/TokenPLS/Hako-Client/issues)，注明平台与系统版本、应用版本或源码提交、复现步骤，以及预期和实际行为。只分享复现所需的配置与日志，并移除凭据和订阅链接。

内核问题可提交到 [Hako](https://github.com/TokenPLS/Hako/issues)，数据包桥接与扩展生命周期问题请提交到 [Hako-Adapter](https://github.com/TokenPLS/Hako-Adapter/issues)。

## 许可证

[GPL-3.0](LICENSE)。第三方资源的许可证随资源保留。

</details>
