# AWR1843 Developer Guide

## 1. 文档简介

本文档面向已经完成 AWR1843 官方 Out of Box Demo 运行的开发者，介绍如何基于 TI mmWave SDK 对 AWR1843 雷达单板进行基础二次开发。

本教程不会介绍软件安装、Demo 烧录等基础内容，而是重点介绍官方工程结构、CFG 参数配置、数据格式解析以及如何修改官方 Demo，实现自定义功能。

建议读者已经完成《Quick Start Guide》的全部内容，并能够独立完成官方 Demo 的编译、烧录及运行。

本文主要包括以下内容：

- mmWave SDK 工程结构介绍
- CFG 配置文件详解
- 雷达串口数据格式解析
- Advanced Frame（高级帧）配置
- 官方 Demo 的二次开发
- 自定义 CLI 指令开发
- 添加温度检测功能
- 添加雷达型号查询功能

---

# 2. 工程结构介绍

## 2.1 mmWave SDK 目录结构

介绍 SDK 的整体目录组成，重点说明：

- packages
- demo
- drivers
- control
- datapath
- utils

说明各目录的作用。

---

## 2.2 Out of Box Demo 工程结构

介绍：

```
mmw/
├── common/
├── mss/
├── dss/
└── profiles/
```

说明：

- MSS 工程负责什么
- DSS 工程负责什么
- common 中包含哪些公共模块
- profiles 中保存哪些配置文件

---

## 2.3 程序启动流程

介绍整个 Demo 的启动过程：

```
main()
    ↓
SOC 初始化
    ↓
CLI 初始化
    ↓
mmWave 初始化
    ↓
等待配置文件
    ↓
sensorStart
    ↓
开始采集
    ↓
DSP 信号处理
    ↓
UART 输出
```

帮助开发者理解整个程序运行流程。

---

# 3. CFG 配置文件

## 3.1 CFG 配置简介

介绍：

- CFG 文件是什么
- CLI 如何解析 CFG
- 每一条命令对应哪个模块

---

## 3.2 常用配置命令

分别介绍：

### channelCfg

配置收发通道

### adcCfg

ADC 输出格式

### profileCfg

介绍：

- 起始频率
- Ramp 时间
- ADC Sample
- Sample Rate

以及这些参数对：

- 最大距离
- 距离分辨率

的影响。

给出对应公式。

---

### chirpCfg

介绍 Chirp 的组成。

---

### frameCfg

介绍：

- Frame Period
- Number of Chirps
- Loop

并说明：

Frame 如何决定：

- 最大速度
- 速度分辨率

给出对应计算公式。

---

### guiMonitor

介绍控制输出哪些 TLV。

例如：

```
Point Cloud
Range Profile
Heatmap
Statistics
```

---

## 3.3 CFG 参数修改实例

例如：

修改：

```
最大距离

距离分辨率

帧率
```

说明需要修改哪些参数。

---

# 4. 雷达数据格式解析

## 4.1 数据输出流程

介绍：

```
DSP
    ↓
TLV 打包
    ↓
UART
    ↓
PC
```

---

## 4.2 Packet Header

详细介绍：

Magic Word

Version

Platform

Frame Number

Packet Length

Num TLV

Subframe Number

等等。

---

## 4.3 TLV 数据格式

介绍：

TLV Header

TLV Payload

以及各种：

Type

Length

Data

---

## 4.4 常见 TLV 类型

例如：

- Detected Points
- Side Info
- Range Profile
- Noise Profile
- Heatmap
- Statistics

介绍各自的数据格式。

---

## 4.5 数据解析流程

介绍 PC 如何解析：

```
寻找 Magic Word

↓

读取 Header

↓

循环解析 TLV

↓

得到 Point Cloud
```

---

# 5. Advanced Frame 配置

## 5.1 为什么使用 Advanced Frame

介绍：

普通 Frame

Advanced Frame

适用于：

不同距离模式

不同速度模式

交替工作

---

## 5.2 SubFrame

介绍：

SubFrame 的概念。

多个 Profile 如何组合。

---

## 5.3 配置实例

举例：

SubFrame0：

近距离高分辨率

SubFrame1：

远距离检测

说明配置方法。

---

# 6. 官方 Demo 二次开发

## 6.1 Demo 的整体框架

介绍：

哪些模块适合修改。

哪些模块建议不要修改。

---

## 6.2 添加新的 CLI 指令

介绍：

CLI Table

Command Handler

如何增加：

```
myCommand
```

---

## 6.3 添加温度检测

实现：

```
getTemperature
```

返回：

```
TEMP:42.6
```

介绍：

调用哪些 API。

输出格式。

---

## 6.4 添加雷达型号查询

实现：

```
getRadarType
```

返回：

```
RADAR_TYPE:A
```

介绍：

如何注册命令。

如何定义不同板卡。

---

## 6.5 添加自定义状态信息

例如：

```
Firmware Version

Board Version

Compile Time

Serial Number
```

介绍如何统一管理。

---

# 7. 自定义通信协议

## 7.1 CLI 通信协议

介绍：

CLI 的发送格式。

返回格式。

---

## 7.2 自定义命令设计

例如：

```
getTemperature

getRadarType

getVersion

getStatus
```

建议统一：

```
OK:xxx

ERROR:xxx
```

便于上位机解析。

---

## 7.3 上位机通信示例

介绍：

串口发送：

```
getRadarType
```

返回：

```
OK:getRadarType:A
```

说明通信流程。

---

# 8. 常见问题（FAQ）

介绍开发过程中常见问题，例如：

- CFG 无法下载
- sensorStart 失败
- UART 无数据
- UniFlash 无法连接
- Demo Visualizer 无法识别串口
- 编译失败
- CLI 指令无响应

并给出对应解决方案。

---

# 9. 参考资料

列出本文参考的官方资源：

- TI mmWave SDK User Guide
- mmWave SDK API Guide
- AWR1843 Technical Reference Manual (TRM)
- AWR1843 Datasheet
- mmWave Demo Visualizer User Guide
- TI E2E Forum

方便开发者进一步查阅。
