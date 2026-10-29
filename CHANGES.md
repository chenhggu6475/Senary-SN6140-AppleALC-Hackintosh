# 适配变更

## 现象与证据

macOS 可枚举 14F1:1F87，但未创建可用音频端点。提供的 AppleALC 源码缺少 0x1F87；validateCodecs 按厂商与 codec ID 精确匹配，未匹配项会被移除。AMD 1022:15E3 控制器已有支持。

原日志还有资源请求空响应错误，未证明该错误单独由 codec 缺失引起。修改后维护者报告声卡恢复，但未提供修复后同类日志对照。

## 修改范围

1. 新增 Resources/SN6140/Info.plist，codec 为 8071（0x1F87）。
2. 以 CX8070 layout 15 为基础，仅保留布局 15 的资源引用。
3. layout15.xml 的完整 CodecID 改为 351346567（0x14F11F87）。
4. 将该 codec 的 AppleHDA 补丁目标从小端 861FF114 改为 871FF114，其余模板补丁保留。
5. PinConfigs 的 HDAConfigDefault 追加 SN6140/layout 15，保留原有条目。

没有新增内核 C++ 逻辑。使用源码项目现有的资源生成流程编译。

## 路径参考

| 功能 | 模板中的路径 |
|---|---|
| 耳机 | 0x10 → 0x16 |
| 扬声器 | 0x11 → 0x17 |
| 外接麦克风 | 0x19 → 0x13 |
| 内置麦克风 | 0x1A → 0x14 |

上述连接均存在于本机 Linux codec dump。保留模板的规范化 pin 配置和 EAPD 初始化；没有导入 Windows 驱动或厂商自定义 verbs。

源码中的 Experimental / NOT HARDWARE VALIDATED 标记意为“实验性／尚未经过硬件验证”，是最初候选版本的历史注释。现已收到本机恢复声音的反馈，但完整稳定性测试仍待补充，因此保留测试版定位。
