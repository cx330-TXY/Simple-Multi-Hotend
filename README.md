# 🚀[Simple-Multi-Hotend]

[![License](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](LICENSE)
[![Release](https://img.shields.io/github/v/release/yourusername/yourproject)](https://github.com/yourusername/yourproject/releases)
[![Klipper](https://img.shields.io/badge/Firmware-Klipper-red.svg)](https://www.klipper3d.org/)

[Simple-Multi-Hotend]是一种FDM3D打印多材料/多色解决方案。通过在打印打印过程中切换预热好的装载不同耗材的热端实现快速多材料打印。其目标是实现尽可能低成本且简单稳定的多材料打印，为了实现较高的适配性，热端切换只需要打印头XY轴运动就可实现，且切换结构与热端无关，可以通过修改热端固定件等实现其他热端的兼容。

---

## 核心特性
**自动预热**：基于打印进度预测提前触发加热指令，机械换刀完成后立即以目标温度开始打印。
**欠驱动切换机构**：无需庞大的擦料塔，只需极少量的底座擦拭即可确保喷嘴无混色。
**麦克斯韦定位机构**：每个热端独立控制，完美支持不同温度、不同特性的耗材混合打印（如 PLA + TPU + 水溶性支撑 PVA），绝不堵头。
**自动喷嘴偏移量校准**:

## ⚙️ 运作逻辑

1. **切片软件端**：在orcaslicer里设定更多打印头，开启Ooze预防功能提前设定从待机温度到工作温度需要的加热时间，给多色模型对应部分映射使用的打印头，切片出来的G-code里会根据设定的时间提前输出预热下一个要使用头的命令，当到达切换位置时再输出切换下一个头的命令和降温上一个头的命令。
2. **打印机端**：收到预热下一个头的命令时自动开始从待机温度加热到工作温度，收到换头命令时，先保存当前状态，然后清零当前头设置的偏移量，打印头基体根据停靠坞坐标把当前打印头释放回对应停靠坞，然后抓取下一个要使用的打印头，然后应用这个头的偏移量，最后回到之前保存的打印状态。

## 📦 硬件与软件需求

### 硬件 (Hardware)
* 具有工具更换架构（ToolChanger Kinematics）的 3D 打印机。
* 支持多路热敏和加热棒的主板（如 BTT Octopus, Manta M8P 等）或通过 CAN 工具板扩展。
*[您的换刀机构特定要求，如：磁吸换刀座、微型舵机锁止机构等]。

### 软件 (Software)
* **Klipper** 固件（强烈推荐，本项目包含所有需要的 Jinja2 宏指令）。
* **切片软件**：PrusaSlicer / SuperSlicer / OrcaSlicer（需配置多挤出机及自定义换刀 G-code）。
* [可选] 本项目提供的 Python 后处理脚本。

## 🛠️ 安装与配置指南 (Installation & Setup)

一. 配置文件修改
   1、将toolchange.cfg添加到fluidd里
   2、printer.cfg需要进行一下修改
   3、
二. 切片软件修改
   1、完成配置文件修改后测试T0、T1、UNTOOL等命令，出现切换失败等异常情况调整停靠坞特征点坐标解决，切换完美后开始进行切片软件配置。
   2、orca切片软件→打印机设置→打印机G-code→修改打印机起始G-code和耗材丝更换G-code。
   3、打印机设置→材料→按自己热端数设置更多挤出机。
   3、打印机设置→挤出机1、2、3……→设置回抽参数。
   4、工艺→材料→设置擦料塔和Ooze预防（自动预热）参数。
