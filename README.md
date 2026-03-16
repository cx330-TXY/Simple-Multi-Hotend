# 🚀[Simple-Multi-Hotend]

[![License](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](LICENSE)
[![Release](https://img.shields.io/github/v/release/yourusername/yourproject)](https://github.com/yourusername/yourproject/releases)
[![Klipper](https://img.shields.io/badge/Firmware-Klipper-red.svg)](https://www.klipper3d.org/)

[Simple-Multi-Hotend]是一种FDM3D打印多材料/多色解决方案。通过在打印打印过程中切换预热好的装载不同耗材的热端实现快速多材料打印

---

## 💡 为什么要做这个项目？

目前市场上的多材料打印方案主要分为两类：
1. **单喷头耗材切换 (如 AMS, MMU, ERCF)**：需要频繁退料、进料，产生庞大的擦料塔（浪费耗材），且极其耗时。
2. **传统多热端换刀 (ToolChanger)**：虽然避免了频繁进退料和大幅减少了擦料塔，但每次换刀时都需要等待新喷头加热、旧喷头降温，增加了巨大的整体打印时间。

**本项目解决了这一痛点！** 
通过切片软件后处理或固件级宏代码（Macro），本系统能够预测下一次换刀的时机，在当前喷头还在打印时，**提前在待机舱位预热下一个热端**。当需要换色/换料时，只需几秒钟的机械动作即可瞬间接力打印，无需原地等待升温！

## ✨ 核心特性 (Features)

* ⚡ **零等待换刀 (Zero-Wait Swap)**：基于打印进度预测提前触发加热指令，机械换刀完成后立即以目标温度开始打印。
* ♻️ **极限减少浪费 (Minimal Purge)**：无需庞大的擦料塔，只需极少量的底座擦拭即可确保喷嘴无混色。
* 🧱 **真正的多材料混搭 (True Multi-Material)**：每个热端独立控制，完美支持不同温度、不同特性的耗材混合打印（如 PLA + TPU + 水溶性支撑 PVA），绝不堵头。
* 💧 **智能防滴漏控制 (Smart Ooze Control)**：非工作状态的热端会自动降至待机温度，结合定制的喷嘴封堵座（Docking Seal），杜绝拉丝和滴漏。
* 🛠️ **高度可定制**：基于[Klipper 固件 / RepRapFirmware] 开发，硬件上适配主流 CoreXY 架构（如 Voron, RatRig）。

## ⚙️ 工作原理 (How it Works)

1. **切片解析阶段**：后处理脚本读取 G-code，计算当前耗材打印所需的剩余时间。
2. **预热触发机制**：当距离下一次换刀还有 `X` 秒（刚好等于目标热端的升温时间）时，向空闲舱位中的目标热端发送加热指令。
3. **换刀动作**：当前打印任务暂停，X/Y 轴移动至工具坞（Tool Dock）放下旧热端，抓取已达到工作温度的新热端。
4. **无缝衔接**：新热端进行快速擦拭（Wipe）后，立即返回模型继续打印。

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
