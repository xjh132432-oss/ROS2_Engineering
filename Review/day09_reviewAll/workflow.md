
### workflow.md

```markdown
# ROS 2 总复习工作记录

## 日期
2026-10-20

## 学习内容
对古月居《ROS 2 入门21讲》第 1–20 讲进行阶段性总复习。

### 1. 基础与工程
- 回顾 ROS 2 架构、节点与工作空间。
- 整理功能包结构、`package.xml`、`CMakeLists.txt`。
- 复习 `colcon build`、环境加载及 CLI 常用命令。
- 总结 `COLCON_IGNORE` 可能导致功能包无法被发现的问题。

### 2. 通信机制
- 区分 Topic、Service、Action 与 Parameter 的适用场景。
- 回顾自定义消息、服务和动作接口的基本用途。
- 复习 DDS、QoS 与分布式通信。
- 记录已成功验证的 `AddTwoInts` 服务调用，结果为 `sum=8`。

### 3. Launch 与机器人描述
- 回顾多节点启动、参数传递与 Launch 文件组织。
- 复习 TF2 坐标系、静态/动态变换及查询方向。
- 梳理 URDF 的 Link、Joint、Visual、Collision、Inertial。
- 回顾 Xacro 宏、参数及机器人模型组合方式。

### 4. 仿真与调试
- 梳理 Gazebo Sim、RViz、RQt 的定位与用途。
- 回顾 ROS 2 Jazzy 与 `ros_gz` 的配套关系。
- 整理仿真启动、坐标系可视化和节点通信的后续验证方向。
- 记录 RQt 的正确安装包命名方式为 `ros-jazzy-rqt`。

## 当前环境
- Ubuntu 24.04 ARM64
- ROS 2 Jazzy
- 工作空间：`~/dev_ws`
- 教程仓库：`~/dev_ws/src/ros2_21_tutorials`

## 复习结论
已完成第 1–20 讲的知识体系梳理，形成从基础配置、通信机制到机器人模型、仿真调试的整体认知。

本次以总结和查漏补缺为主，不将尚未实际运行验证的内容记录为已完成实践。

## 后续待办
- [ ] 验证 QoS 配置与通信兼容性。
- [ ] 实际运行并检查 Launch 文件。
- [ ] 验证 TF2 与 URDF/Xacro 的联动。
- [ ] 启动 Gazebo Sim 并确认仿真环境正常。
- [ ] 使用 RViz、RQt 检查机器人模型与通信状态。