# 构建方法

## 版本来源

本次基于维护者提供的 AppleALC-master 源码 ZIP。其 Xcode 项目 MODULE_VERSION 为 1.9.9，Changelog 顶部为 1.9.8；没有可核实的 Git 提交号，不能将持续变化的 master 分支视为固定基线。

发行版同时提供实际适配的完整源码快照，用于审查和重建。该快照不含编译缓存或第三方构建依赖。相同版本信息不保证逐字节重建；日期、工具链、资源生成等会影响二进制。

## 本次使用的环境

- Xcode 16.4，macOS SDK 15.5。
- Lilu 1.7.2 DEBUG 开发文件：需要 Contents/Resources 中的 Headers 和 Library。
- MacKernelSDK：维护者下载的 master 快照，未记录提交号。
- x86_64，Debug 配置；运行机器继续使用 Lilu 1.7.3。

Lilu 和 MacKernelSDK 的下载链接分别为：

- https://github.com/acidanthera/Lilu/releases
- https://github.com/acidanthera/MacKernelSDK

不要把仅用于构建的 Lilu 自动复制到 EFI。

## 准备

优先解压本发行版的源码快照。若自行选择其他上游版本，应审查 source-overlay 的差异，尤其避免直接覆盖新版 PinConfigs 而丢掉上游新增条目。patches/SN6140.patch 是本次基线的 Git 补丁，应用前先运行 git apply --check。

在源码根目录放入：

- Lilu.kext，含开发用头文件和库。
- MacKernelSDK/，含 Headers 和 Library。

完整 Xcode 的许可和首次运行组件需要由构建者自行完成。以下命令仅编译，不安装驱动。用文本编辑器把示例 Xcode 路径改为本机实际位置。

```sh
export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
mkdir -p build-cache/modules build-cache/precompiled
xcodebuild -project AppleALC.xcodeproj -target AppleALC \
  -configuration Debug -arch x86_64 -jobs 2 \
  CODE_SIGNING_ALLOWED=NO \
  CLANG_MODULE_CACHE_PATH="$PWD/build-cache/modules" \
  MODULE_CACHE_DIR="$PWD/build-cache/modules" \
  SHARED_PRECOMPS_DIR="$PWD/build-cache/precompiled" build
```

通常产物位于 build/Debug/AppleALC.kext。不要同时在同一源码目录运行多个构建。Debug 表示调试构建；Release 表示发布构建，此项目尚未验证后者。
