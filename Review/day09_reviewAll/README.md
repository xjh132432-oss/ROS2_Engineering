# ROS 2 入门总复习（第 1–20 讲）

## 一、复习目标

对古月居《ROS 2 入门21讲》第 1–20 讲进行阶段性总复习，梳理 ROS 2 的核心架构、通信机制、工程构建流程、坐标变换、机器人模型、仿真与调试工具，为后续四足机器人项目开发打基础。

学习环境：
- Ubuntu 24.04 ARM64
- ROS 2 Jazzy
- 工作空间：`~/dev_ws`
- 教程仓库：`~/dev_ws/src/ros2_21_tutorials`

## 二、核心知识体系

### 1. ROS 2 基础与工程构建
- ROS 2 的基本架构及节点概念
- 环境配置、工作空间、功能包
- `colcon build`、`source install/setup.bash`
- `package.xml`、`CMakeLists.txt`
- 常用 CLI：`ros2 node`、`ros2 topic`、`ros2 service`、`ros2 interface`

### 2. 节点通信机制
- 话题 Topic：适用于连续数据流，采用发布/订阅模式
- 服务 Service：适用于请求/响应式交互
- 动作 Action：适用于耗时任务，支持反馈与取消
- 参数 Parameter：用于运行时配置节点
- 自定义接口：通过 `.msg`、`.srv`、`.action` 描述通信数据

已验证服务调用：

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 3, b: 5}"w