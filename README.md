# 智能语音风扇系统

## 项目简介

该项目是一个基于 STM32 的智能语音风扇原型，集成本地按键、语音控制、LCD+触摸 GUI（LVGL）、ESP32 云网关和微信小程序远程控制。系统采用 FreeRTOS 任务调度，ESP32 负责与云端（华为云 IoT）交互，实现双向状态同步与远程指令下发。

## 核心功能

- 温湿度采集（DHT11），本地显示并可上报云端。
- 语音指令解析（通过语音模块串口返回 ID），支持挡位调节、开关、延时/定时等快捷控制。
- 本地按键控制与 GUI 触摸交互（LVGL）。
- 云端/手机控制：微信小程序通过华为云 IoT 下发命令，ESP32 转发到 STM32 执行。
- RTC 授时与周期定时支持。

## 目录概览（关键文件）

- 固件（STM32）: [32代码/](32代码/)
  - 启动与主程序: [32代码/User/main.c](32代码/User/main.c#L1-L200)
  - 语音串口驱动: [32代码/Hardword/voice_uart/voice_uart.c](32代码/Hardword/voice_uart/voice_uart.c#L1-L200)
  - ESP32 网关串口: [32代码/Hardword/esp32_uart/esp32_uart.c](32代码/Hardword/esp32_uart/esp32_uart.c#L1-L240)
  - 传感器: [32代码/Hardword/dht11/dht11.c](32代码/Hardword/dht11/dht11.c)
  - GUI 与 LVGL 配置: [32代码/LVGL/](32代码/LVGL/)

- 微信小程序（前端）: [微信小程序代码/](微信小程序代码/)
  - 主页面与云交互: [微信小程序代码/pages/index/index.js](微信小程序代码/pages/index/index.js#L1-L200)

- ESP32 代码（示例）: [ESP32代码/sketch_apr19a/sketch_apr19a.ino](ESP32代码/sketch_apr19a/sketch_apr19a.ino)
- 语音模块数据: [语音模块代码/asrpro.hd](语音模块代码/asrpro.hd)

## 编译与运行（简要）

- STM32 固件
  - 使用 Keil uVision 打开工程文件： [32代码/Project.uvprojx](32代码/Project.uvprojx)
  - 编译并通过 ST-Link 或相应下载器刷写到目标板。
  - 运行时依赖 FreeRTOS 与 LVGL 子模块（已包含于工程目录）。

- ESP32
  - 若为 Arduino sketch，可用 Arduino IDE 或 VS Code + PlatformIO 打开 [ESP32代码/sketch_apr19a/sketch_apr19a.ino](ESP32代码/sketch_apr19a/sketch_apr19a.ino) 并烧录。

- 微信小程序
  - 用微信开发者工具打开 `微信小程序代码` 目录，修改 `pages/index/index.js` 中的华为云配置（`CONFIG`）为你自己的 IAM/IoT 参数后编译预览。

## 常见修改点

- 修改云端设备 ID / 项目 ID / 密码：编辑 [微信小程序代码/pages/index/index.js](微信小程序代码/pages/index/index.js#L1-L40)。
- 语音命令映射：编辑 [32代码/User/main.c](32代码/User/main.c#L1-L200) 中 `Voice_task` 的映射规则。
- 串口协议解析（ESP32 ↔ STM32）：查看 [32代码/Hardword/esp32_uart/esp32_uart.c](32代码/Hardword/esp32_uart/esp32_uart.c#L1-L240)。

## 维护与扩展建议

- 如需增加新语音命令，统一在语音模块协议与 `Voice_task` 中添加映射；注意保持协议在语音模块、STM32、和云端之间一致。
- 小程序与云端协议（命令名与参数）需保持一致，例如 `SetGear`, `SetTimer`, `SetPeriodTimer`, `SyncTime`。

