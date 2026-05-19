# STC8051U BLDC ESC – 无传感器无刷电子调速器

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-STC8051U-red)]()
[![Motor](https://img.shields.io/badge/Motor-BLDC--Sensorless-brightgreen)]()

基于 **STC8051U** 单片机设计的 **无传感器无刷直流电机（BLDC）电子调速器（ESC）**，适用于无人机、小型电动工具

<img width="1076" height="658" alt="Snipaste_2026-05-19_12-56-54" src="https://github.com/user-attachments/assets/f4ca5fc0-2f1b-44c6-860d-de533aad2825" />
<img width="1070" height="661" alt="Snipaste_2026-05-19_12-57-10" src="https://github.com/user-attachments/assets/62f9312a-ee8d-44d6-937f-07e93edf46c5" />

---

## 特性

- **主控芯片**：STC8051U（增强型8051内核，内置比较器/ADC，硬件PWM）
- **控制方式**：无传感器六步换向（120° 导通方式）
- **位置检测**：反电动势（BEMF）过零点检测，无需霍尔传感器
- **调制方式**：上桥臂PWM调制 + 下桥臂恒通（或互补PWM，根据配置可选）
- **启动方式**：三段式启动（预定位 → 强制换向 → 同步运行）
- **保护功能**：过流保护（通过ADC采样）、堵转保护、欠压保护
- **参数配置**：可通过串口（UART）在线调节PWM频率、启动参数等
- **开源协议**：MIT

---

## 工作原理

### 1. 六步换向法
三相BLDC的六个导通顺序（AB → AC → BC → BA → CA → CB），每60°电角度切换一次，每次导通两相（一相高端PWM，一相低端ON，第三相悬空）。

### 2. 反电动势过零点检测
- 悬空相的端电压与虚拟中性点电压比较（利用芯片内部比较器或外部电阻分压网络）
- 当BEMF过零点发生后，延迟30°电角度执行换相
- STC8051U内置比较器可直接用于过零检测，无需外部比较器芯片

### 3. 闭环控制
通过PWM占空比调节电机转速，根据换相周期计算实时转速，实现速度闭环（可扩展）。

---

## 硬件设计

### 主要元件清单

| 元件 | 型号/参数 | 数量 |
|------|-----------|------|
| MCU | STC8051U-40I-LQFP48 | 1 |
| MOSFET 驱动 | EG2181 / IR2101 (半桥) | 3 |
| N-MOSFET | 根据电压电流选择 (如 IRFS3607) | 6 |
| 电流采样电阻 | 0.001Ω ~ 0.01Ω 功率电阻 | 1 |
| 电压采样电阻分压 | 用于母线电压、反电动势 | 若干 |

### 典型电路结构
