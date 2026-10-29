# Senary SN6140 的 AppleALC 适配

为 Senary / Conexant SN6140（HDA codec ID **14F1:1F87**）补充 AppleALC 匹配和音频资源。

本项目基于 [acidanthera/AppleALC](https://github.com/acidanthera/AppleALC)，参考 MacPeet 的 CX8070 / ThinkPad E590 layout 15 资源。它是社区定制适配，与 AppleALC 官方发行版分开维护。

## 当前状态

维护者在机械革命翼龙 15 Pro 上替换此驱动后，报告 macOS 声卡恢复工作。尚未收到逐项稳定性测试结果，当前作为**测试版**发布，不宣称适用于全部 SN6140 机器。

| 项目 | 本次环境 |
|---|---|
| 电脑 | 机械革命翼龙 15 Pro |
| CPU | AMD Ryzen 7 8845H |
| HDA 控制器 | AMD 1022:15E3 |
| Codec | SN6140，14F1:1F87 |
| Codec 子系统 | 1D05:1407 |
| Codec revision | 0x100100 |
| macOS | 15.8，24H23 |
| Lilu（运行时） | 1.7.3 |
| 布局 | alcid=15 |
| 驱动标识 | DBG-199-2026-10-26，x86_64 |

包内版本号 1.9.9 来自所下载 AppleALC 源码的项目设置，不代表此适配由上游正式发布。支持 macOS 其他版本的情况未验证；macOS 26 缺少 AppleHDA 的环境不在此次验证范围内。

## 下载与安装

从本仓库的 [Releases（发行版）](https://github.com/chenhggu6475/Senary-SN6140-AppleALC-Hackintosh/releases) 下载 SN6140 驱动 ZIP。

1. 先确认硬件 codec ID。仅有 Windows 的“Senary Audio”名称不足以确认兼容。
2. 备份完整现用 EFI，准备并验证可启动的恢复 EFI。
3. 解压驱动包，只使用其中的 AppleALC.kext。
4. 在测试 EFI 中替换原来的 AppleALC.kext，避免出现重复条目。
5. OpenCore 的 Kernel → Add 中保持 AppleALC 启用，排在 Lilu 后面。原有路径保持不变。
6. 使用 alcid=15；本机测试沿用了 alcdelay=1000，未验证是否必要。
7. 保留现有 USB、Wi-Fi、ACPI 等配置。此次适配不需要重置 NVRAM。
8. 重启后先调低音量，检查声音设置中的设备，再测试输出和录音。

本仓库的 source-overlay 是编译资源，里面的 PinConfigs.kext **不能单独加入 EFI**。不要使用它替代编译后的 AppleALC.kext。

## 核验加载与回滚

以下命令只读，不需要 sudo：

```sh
ioreg -r -n AppleALC -l -w0 | grep VersionInfo
system_profiler SPAudioDataType
```

预期加载标识为 DBG-199-2026-10-26。若启动异常，从原 EFI 或恢复介质启动，恢复备份的 AppleALC.kext。若有刺耳噪声，立即暂停并静音，不要反复佩戴耳机复现。

一键更新工具可能把此定制驱动覆盖为官方版，更新前应核对并备份。

## 已知限制与待测试项目

- 适配资源未按子系统 ID 强制限制匹配；相同 codec 的其他机器也可能匹配，但其引脚连接未验证。
- 内置麦克风沿用 CX8070 的 0x14 输入路径。Linux 采集时使用 0x13，虽两者均有连接，macOS 各输入端点仍需逐项验证。
- 连续播放、耳机插拔、静音、冷启动、Windows 切回 macOS、睡眠唤醒的稳定性待记录。
- Ubuntu 下维护者确认扬声器、耳机和话筒均正常。此结果是硬件参考，不等同于 macOS 各项已通过。
- 驱动为 DEBUG 构建。尚未提供经验证的 RELEASE 构建。

测试记录见 [TESTING.md](TESTING.md)。源码变更见 [CHANGES.md](CHANGES.md)，构建方法见 [BUILD.md](BUILD.md)。

## 来源与致谢

- [AppleALC](https://github.com/acidanthera/AppleALC)：驱动框架、codec 数据库及原始资源。
- MacPeet：CX8070 / CX11880 的 ThinkPad E590 layout 15 基础资源。
- [Lilu](https://github.com/acidanthera/Lilu)、[MacKernelSDK](https://github.com/acidanthera/MacKernelSDK)：构建与运行依赖。
- 本项目由维护者实机检测、测试，借助 OpenAI Codex 完成分析和源码修改。

保留原有作者信息，遵循 [LICENSE](LICENSE) 中的 BSD 3-Clause 许可证。英文许可原文保留；中文摘要见 [LICENSE.zh-CN.md](LICENSE.zh-CN.md)。
