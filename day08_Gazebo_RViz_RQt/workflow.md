# Workflow｜古月居 ROS 2 第18、19、20讲

## 一、学习目标与进度

### 第18讲：Gazebo 仿真

- [x] 理解 World、Model、Physics、Collision 和 Inertial。
- [x] 理解 URDF/Xacro 与 SDF 的用途。
- [x] 安装 `ros-jazzy-ros-gz`。
- [x] 确认 `ros_gz_sim`、`ros_gz_bridge` 等软件包存在。
- [x] 确认 Gazebo Sim 版本为 `8.15.0`。
- [x] 找到教程中的 URDF、Xacro 及传感器模型文件。
- [ ] 启动 Gazebo 空白世界。
- [ ] 检查 Gazebo 话题和服务。
- [ ] 加载机器人模型并确认显示结果。
- [ ] 检查模型的碰撞、惯性和插件兼容性。

### 第19讲：RViz 可视化

- [x] 理解 RViz 的用途。
- [x] 理解 RViz 与 Gazebo 的区别。
- [x] 了解 RobotModel、TF、Image、LaserScan 和 PointCloud2。
- [x] 理解 URDF、TF2 与 RViz 的关系。
- [ ] 确认 `rviz2` 已安装。
- [ ] 启动 RViz 并检查界面。
- [ ] 加载机器人模型。
- [ ] 检查 Fixed Frame 和 TF。
- [ ] 尝试显示传感器数据。

### 第20讲：RQt 图形化调试

- [x] 了解 RQt 的插件机制。
- [x] 了解 `rqt_graph`、`rqt_console` 和 `rqt_plot`。
- [x] 了解 Service Caller 的用途。
- [x] 确认正确的软件包名称为 `ros-jazzy-rqt`。
- [ ] 安装 RQt。
- [ ] 启动 `rqt`。
- [ ] 使用 `rqt_graph` 检查节点和话题。
- [ ] 尝试使用其他插件调试 ROS 2 系统。

> 勾选状态以实际学习和终端验证为准。已了解相关概念不代表实践命令已成功运行。

## 二、环境信息

- 操作系统：Ubuntu 24.04 ARM64
- ROS 2：Jazzy
- Gazebo Sim：8.15.0
- 工作空间：`~/dev_ws`
- 教程仓库：`~/dev_ws/src/ros2_21_tutorials`

加载环境：

```bash
source /opt/ros/jazzy/setup.bash
```

## 三、Git 提交流程

在仓库中执行：

```bash
git status
git diff
```

确认 `README.md` 和 `workflow.md` 的内容准确后暂存：

```bash
git add README.md workflow.md
git diff --cached
```

确认暂存内容无误后提交：

```bash
git commit -m "docs(ros2): document lessons 18-20"
git status
```

## 四、后续实践顺序

1. 启动 Gazebo 空白世界。
2. 检查机器人 URDF 的物理属性与仿真兼容性。
3. 尝试将机器人模型加载到 Gazebo。
4. 使用 RViz 检查机器人模型和 TF 坐标系。
5. 安装并启动 RQt。
6. 使用 `rqt_graph` 检查 ROS 2 节点与话题关系。
7. 将 Gazebo、RViz、RQt 与 ROS 2 通信、TF2 和 URDF 知识结合起来。