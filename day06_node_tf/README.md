# 古月居 ROS 2 入门 21 讲｜第 13–15 讲

## 一、学习概况

- 学习日期：2026-10-17
- 学习内容：第 13、14、15 讲
- 学习平台：Ubuntu 24.04 ARM64（VMware Fusion）
- ROS 2 版本：Jazzy
- 教程仓库：https://github.com/guyuehome/ros2_21_tutorials
- 工作空间：`~/dev_ws`
- 教程目录：`~/dev_ws/src/ros2_21_tutorials`

本次将第 13、14、15 讲作为一天的完整学习内容，重点学习 ROS 2 分布式通信、DDS/QoS 通信机制，以及 Launch 启动与配置管理。

## 二、第 13 讲：ROS 2 分布式通信

### 1. 核心内容

- 理解 ROS 2 分布式通信的基本原理。
- 理解 ROS 2 使用 DDS 作为底层通信基础之一。
- 理解 ROS 2 节点可以通过分布式通信机制发现彼此并交换数据。
- 认识 ROS 2 与 ROS 1 在通信架构上的区别。
- 理解 ROS 2 不依赖 ROS 1 那种中心化 Master 进行节点发现。

### 2. 学习目标

能够从系统架构层面理解 ROS 2 节点之间如何通信，为后续学习 QoS、机器人多节点协同和分布式机器人系统打下基础。

## 三、第 14 讲：DDS 与 QoS

### 1. 核心内容

理解 ROS 2 通信中 DDS、RMW 与 ROS 2 上层 API 之间的关系。

重点学习 QoS（Quality of Service，服务质量）配置。

### 2. QoS 关键策略

#### Reliability：可靠性

- `RELIABLE`：尽可能保证消息可靠传递。
- `BEST_EFFORT`：尽力发送消息，不保证每条消息都能送达。

#### Durability：持久性

- `VOLATILE`：不为后加入的订阅者保留历史消息。
- `TRANSIENT_LOCAL`：发布者保留一定的历史消息，供符合条件的后加入订阅者获取。

#### History：历史消息策略

- `KEEP_LAST`：保留最近指定数量的消息。
- `KEEP_ALL`：在资源限制允许的情况下保留消息。

#### Depth：队列深度

指定 `KEEP_LAST` 策略下保留的消息数量。

### 3. 相关实践命令

```bash
source /opt/ros/jazzy/setup.bash

ros2 run demo_nodes_cpp talker