# ESP32-S3 芯片约束快速反查

> **用途**：P1 阶段"芯片约束逐引脚反查"的速查表——每个引脚写入引脚表前，对照本表确认无冲突。
> ⚠️ **边界**：本表是快速检查参考，不是权威依据；以当前官方 ESP32-S3 datasheet / TRM 为准。数据若有出入，优先官方文档。

## Strapping 引脚（上电状态决定启动模式，不可作普通 IO）

| GPIO | 上电默认 | 影响 | 约束 |
|---|---|---|---|
| GPIO0 | 内部上拉 | 下载模式选择（与 GPIO46 组合） | 复位/下载时电平受控；不可当普通按键用 |
| GPIO3 | 内部上拉 | JTAG 信号源选择 | 上电期间电平受控 |
| GPIO45 | 内部下拉 | VDD_SPI 电压（1.8/3.3V） | 上电期间电平受控，不可接不可控拉高 |
| GPIO46 | 内部下拉 | 下载模式选择（与 GPIO0 组合） | 上电期间电平受控；GPIO46 仅输入 |

## 仅输入引脚

| GPIO | 说明 |
|---|---|
| GPIO46 | 仅输入，无输出能力；不能驱动外部负载 |

## 被 Flash / PSRAM 占用的引脚（取决于封装与存储配置）

| GPIO | 用途 |
|---|---|
| GPIO26–GPIO32 | PSRAM/Flash 数据线（Octal/Quad 配置相关） |
| GPIO33–GPIO37 | PSRAM/Flash 相关（Octal SPI） |
| GPIO47/48 | SPICLK_P / SPICLK_N（Flash 时钟）——**本项目 S1–S8 未用，但官方表与原理图确认 47/48 已被占用** |
| 部分封装 | GPIO37/38 等视封装与 Flash 位宽 |

> 反查方法：写引脚前查官方 `pin_mux`/datasheet 的"IO MUX"与存储接线表；与硬件档案"引脚占用统计"交叉比对。

## 复用功能注意

| 功能 | 引脚 | 注意 |
|---|---|---|
| 原生 USB（USB-OTG / USB-Serial-JTAG） | GPIO19 / GPIO20 | 两者复用同一物理脚；配成 USB_CDC 后不能再作普通 IO |
| ADC1 | GPIO1–GPIO10 | 可作普通 IO 或 ADC，功能互斥 |
| RTC GPIO | GPIO0–GPIO21（部分） | 睡眠唤醒可用；仍受 strapping/复用约束 |
| I2C / SPI / UART / I2S / SDMMC | 多路可选 | 写入前查是否与占用脚冲突 |

## 安全建议

1. 每个 GPIO 写进 `pin_config.h` 前，对照：strapping 表 → 仅输入表 → 存储占用表 → 复用功能表 → 硬件档案。
2. 网络教程给出的引脚布局一律回到官方 datasheet 核对，尤其 SDK 大版本差异（v4/v5/v6 引脚定义可能不同）。
3. 其他芯片：现查该芯片官方 datasheet，不套用 S3 约束。
