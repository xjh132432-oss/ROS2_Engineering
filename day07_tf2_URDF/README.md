# 古月居 ROS2 入门21讲｜第16、17讲学习记录

## 学习主题

- 第16讲：TF2 坐标变换
- 第17讲：URDF 机器人模型

学习环境：Ubuntu 24.04 ARM64、ROS 2 Jazzy  
工作空间：`~/dev_ws`  
教程仓库：`src/ros2_21_tutorials`

---

## 第16讲：TF2 坐标变换

### 1. 学习目标

理解机器人系统中不同坐标系之间的关系，掌握 TF2 的基本概念、静态坐标变换和坐标变换查询方法。

### 2. 核心知识

- **坐标系（Frame）**：描述机器人本体、传感器、地图等对象的位置和姿态。
- **父子坐标系**：通过坐标变换建立坐标系之间的关联。
- **TF 树**：多个坐标系通过父子关系组成树形结构。
- **静态变换**：适用于固定安装的部件，例如机器人底盘与相机之间的固定关系。
- **动态变换**：适用于随时间变化的关系，例如机器人运动时的位置和姿态。
- **`/tf`**：主要传递动态坐标变换。
- **`/tf_static`**：主要传递静态坐标变换。

常见坐标系包括 `map`、`odom`、`base_link` 和 `camera_link`。合理组织坐标系是机器人定位、导航、传感器融合和运动控制的基础。

### 3. 静态坐标变换实践

使用 `static_transform_publisher` 发布 `world` 到 `house` 的静态变换：

```bash
source /opt/ros/jazzy/setup.bash

ros2 run tf2_ros static_transform_publisher \
  --x 10 --y 5 --z 0 \
  --roll 0 --pitch 0 --yaw 0 \
  --frame-id world \
  --child-frame-id house