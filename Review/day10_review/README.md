# ROS 2 Engineering Learning Log

记录 ROS 2 学习过程、命令行实践、工程配置与踩坑经验。本文档以本机实际学习环境和已记录的学习进度为基线，持续更新。

## 学习环境

- 主机：Mac Apple Silicon
- 虚拟机：VMware Fusion
- 系统：Ubuntu 24.04 ARM64
- ROS 2：Jazzy
- 工作空间：`~/dev_ws`
- 教程仓库：`ros2_21_tutorials`
- 主要学习方式：命令行实操、查看节点/话题/服务/参数、通过 Launch 组合节点并验证通信

## 学习进度

截至 2026-10-23，已完成古月居 ROS 2 入门教程第 1–20 讲的学习与复习；本次推进到**阶段七：TF2 坐标变换**，重点围绕命令行检查与坐标变换原理进行实践。

当前复习覆盖：

1. 环境与工作空间：加载 ROS 2 / 工作空间环境，检查包与可执行程序
2. Node：节点发现、节点信息与节点/进程/功能包的区别
3. Topic：查看话题、类型、端点、数据和频率，以及命令行发布消息
4. Service、Action、Parameter：查看接口、调用服务、理解参数配置
5. DDS 与 QoS：通信域、中间件配置、端点 QoS 和跨设备通信排查
6. Launch：单节点启动与多节点启动编排
7. TF2：`/tf`、`/tf_static`、静态变换发布、变换查询和坐标树
8. URDF/Xacro：模型文件结构、Link/Joint 与 TF 的联系
9. Gazebo Sim、RViz、RQt：仿真、可视化和调试工具
10. 综合排查：按环境 → 节点 → 通信 → 数据 → 坐标 → 可视化的顺序定位问题

> 进度记录不等于所有示例都在每次复习中重新运行成功。执行前应确认对应节点、Launch 和依赖已安装并启动。

## 常用环境命令

```bash
source /opt/ros/jazzy/setup.bash
source ~/dev_ws/install/setup.bash

echo "$ROS_DISTRO"
cd ~/dev_ws
colcon list
ros2 pkg list
```

每个新终端都需要按需加载环境。源码中存在功能包，不代表它已经构建并安装到当前环境。

## 常用命令速查

| 目标 | 命令 |
|---|---|
| 查看节点 | `ros2 node list` |
| 查看节点端点 | `ros2 node info /节点名` |
| 查看话题和类型 | `ros2 topic list -t` |
| 查看话题类型 | `ros2 topic type /话题名` |
| 查看话题端点/QoS | `ros2 topic info /话题名 --verbose` |
| 观察消息 | `ros2 topic echo /话题名` |
| 查看消息频率 | `ros2 topic hz /话题名` |
| 查看服务 | `ros2 service list` |
| 查看服务类型 | `ros2 service type /服务名` |
| 查看接口定义 | `ros2 interface show 包名/msg/类型` 或 `包名/srv/类型` |
| 查看动作 | `ros2 action list` |
| 查看参数 | `ros2 param list`、`ros2 param get /节点名 参数名` |
| 导出参数 | `ros2 param dump /节点名` |
| 查看 TF 话题 | `ros2 topic list \| grep tf` |
| 查询坐标变换 | `ros2 run tf2_ros tf2_echo 目标坐标系 源坐标系` |
| 生成 TF 坐标树 | `ros2 run tf2_tools view_frames` |

## TF2 学习重点

- `/tf` 通常用于传输动态坐标变换。
- `/tf_static` 通常用于传输静态坐标变换。
- `ros2 topic list | grep tf` 只会过滤当前可发现的话题；如果 TF 发布器没有启动，可能没有匹配结果。
- `turtlesim` 的 `/turtle1/pose` 是 `turtlesim/msg/Pose`，它描述海龟的位姿，不等同于 TF 坐标变换。
- `static_transform_publisher` 可以发布父子坐标系之间的固定变换。
- `tf2_echo` 的参数顺序为目标坐标系在前、源坐标系在后。

### RQT：模块化可视化工具

### 1. RQT 的作用

课程来源：古月居《ROS2 入门 21 讲》第 20 讲《RQT：模块化可视化工具》（11:11）。

RQT 是基于 Qt 的模块化可视化工具。与使用 RViz 展示完整三维场景不同，RQT 可以根据需求加载不同插件，完成特定的可视化和调试任务。

### 2. 安装与启动

当前学习环境为 Ubuntu 24.04 ARM64 + ROS 2 Jazzy。课程使用 Humble，以下命令根据当前环境调整。

```bash
sudo apt update
sudo apt install ros-jazzy-rqt
source /opt/ros/jazzy/setup.bash
rqt
```

### 3. 课程涉及的主要功能模块

| 功能模块 | 主要用途 |
|---|---|
| 日志显示 | 查看 ROS 2 节点运行时产生的日志信息 |
| 图像显示 | 单独显示摄像头等话题发布的图像 |
| 发布话题数据 | 通过图形界面发布话题消息 |
| 调用服务请求 | 通过图形界面发起服务调用 |
| 绘制数据曲线 | 将数值型话题数据绘制成曲线，观察变化趋势 |
| 数据包管理 | 配合 ROS 数据包记录与回放相关工作 |
| 节点可视化 | 以图形方式观察节点之间的通信关系 |

### 4. 常用插件启动命令

```bash
# RQT 主界面
rqt

# 节点与话题关系图
ros2 run rqt_graph rqt_graph

# 日志显示
ros2 run rqt_console rqt_console

# 图像显示
ros2 run rqt_image_view rqt_image_view
```

部分插件需要额外安装：

```bash
sudo apt install ros-jazzy-rqt-common-plugins
sudo apt install ros-jazzy-rqt-graph ros-jazzy-rqt-console ros-jazzy-rqt-image-view
```

### 5. RQT 与 RViz 的区别

- **RQT**：模块化工具箱，按需加载插件，适合日志查看、图像显示、数据曲线和通信调试。
- **RViz**：三维可视化工具，适合显示机器人模型、TF 坐标系和传感器数据。

两者解决的问题不同，可以根据调试目标选择，也可以配合使用。

### 6. 与 ROS 2 命令行配合

```bash
# 查看节点
ros2 node list

# 查看话题
ros2 topic list

# 查看话题类型及发布、订阅信息
ros2 topic info /turtle1/pose

# 查看节点的通信接口
ros2 node info /turtlesim
```

CLI 用于精确查询系统状态，RQT 用于图形化观察和交互。排查问题时，可以先用 CLI 确认节点和话题，再使用对应的 RQT 插件辅助分析。

## 推荐的排查顺序

1. 检查发行版与环境是否加载。
2. 检查功能包是否能被发现、是否已构建。
3. 检查目标节点是否运行。
4. 检查话题名称、消息类型、发布者/订阅者和 QoS。
5. 用 `echo`、`hz` 验证消息是否实际流动。
6. 对涉及机器人位姿的数据，检查 TF 目标/源坐标系是否存在且变换可查询。
7. 最后结合 RViz、RQt 或 Gazebo 的实际运行情况定位问题。

## 文档维护

- `workflow.md`：记录本次学习实际做过的操作、验证方式与结果。
- `pitfalls.md`：单独记录错误、原因、排查方法和可复用经验。
- 每次提交前检查 `git status` 与 diff，避免把构建产物、日志或无关文件提交进去。
