# SN6140 首个测试版

标签建议：sn6140-test-v1

为 14F1:1F87 增加 AppleALC codec 匹配、layout 15 资源和 pin 配置。基于 CX8070 的 ThinkPad E590 适配。

本机环境：机械革命翼龙 15 Pro、Ryzen 7 8845H、子系统 1D05:1407、macOS 15.8、Lilu 1.7.3、alcid=15。维护者报告声卡恢复。完整稳定性测试仍待补充，其他机型未验证。

驱动版本标识 DBG-199-2026-10-26，x86_64 DEBUG；1.9.9 来自源码项目设置。

下载驱动 ZIP，备份完整 EFI 和恢复启动介质后，由使用者在测试 EFI 中替换 AppleALC.kext。启动异常时恢复原文件。首次测试从低音量开始。

附件包括驱动、源码快照和 SHA-256 校验值。源码快照不包含 Lilu / MacKernelSDK 依赖。
