# 古月居 ROS 2 入门21讲｜第18、19、20讲

## 一、学习概览

| 课程 | 学习主题 | 核心内容 |
|---|---|---|
| 第18讲 | Gazebo 三维物理仿真 | 仿真世界、机器人模型、物理属性、ROS 2 集成 |
| 第19讲 | RViz 三维可视化 | 机器人模型、TF 坐标系、传感器数据显示 |
| 第20讲 | RQt 图形化调试 | 节点关系、话题、服务、日志可视化 |

**学习环境：** Ubuntu 24.04 ARM64、ROS 2 Jazzy、Gazebo Sim 8.15.0  
**工作空间：** `~/dev_ws`  
**教程仓库：** `~/dev_ws/src/ros2_21_tutorials`

## 二、第18讲：Gazebo 仿真

### 1. 核心知识

- **World：** 定义仿真场景。
- **Model：** 表示仿真世界中的机器人或其他实体。
- **Physics：** 模拟重力、碰撞和动力学。
- **Collision：** 定义碰撞几何体。
- **Inertial：** 描述质量、质心和惯性。
- **Sensor：** 模拟相机、激光雷达等传感器。
- **ros_gz_bridge：** 在 Gazebo Transport 和 ROS 2 之间桥接数据。

相关技术的作用：

- URDF/Xacro：描述机器人结构。
- SDF：描述仿真世界、模型及相关配置。
- Gazebo Sim：提供三维仿真环境。
- ROS 2：运行机器人感知、控制和通信节点。

### 2. 安装与验证

安装 Jazzy 对应的 Gazebo 集成：

```bash
sudo apt update
sudo apt install ros-jazzy-ros-gz
```

加载 ROS 2 环境并验证：

```bash
source /opt/ros/jazzy/setup.bash

ros2 pkg list | grep -E 'ros_gz|gz_sim'
command -v gz
gz sim --versions
```

已确认安装成功，Gazebo Sim 版本为 `8.15.0`，`ros_gz_sim`、`ros_gz_bridge` 等软件包可被 ROS 2 识别。

### 3. 机器人模型资源

教程中的模型位于：

```text
learning_urdf/
└── urdf/
    ├── mbot_base.urdf
    ├── mbot_base.xacro
    ├── mbot_with_camera.urdf
    ├── mbot_with_kinect.urdf
    ├── mbot_with_laser.urdf
    └── sensors/
        ├── camera_gazebo.xacro
        ├── kinect_gazebo.xacro
        └── lidar_gazebo.xacro
```

检查模型：

```bash
cd ~/dev_ws/src/ros2_21_tutorials/learning_urdf

grep -nE 'gazebo|plugin|collision|inertial|joint|link' \
  urdf/mbot_base.urdf
```

需要注意，能够在 RViz 中显示的 URDF 不一定能直接在 Gazebo Sim 中正常仿真。旧版 Gazebo Classic 插件可能需要调整。

### 4. 仿真实践

启动空白世界：

```bash
source /opt/ros/jazzy/setup.bash

ros2 launch ros_gz_sim gz_sim.launch.py gz_args:="empty.sdf"
```

查看 Gazebo 话题和服务：

```bash
gz topic -l
gz service -l
```

当前已完成软件安装和版本检查；仿真世界启动、机器人模型加载仍需实际验证。

## 三、第19讲：RViz 可视化

### 1. 核心知识

RViz 是 ROS 2 的三维可视化工具，用于观察机器人模型、坐标变换和传感器数据。

常见 Display：

| Display | 用途 |
|---|---|
| RobotModel | 显示机器人模型 |
| TF | 显示坐标系及其关系 |
| LaserScan | 显示激光雷达数据 |
| Image | 显示相机图像 |
| PointCloud2 | 显示三维点云 |
| Map | 显示地图 |

**Gazebo 与 RViz 的区别：** Gazebo 提供仿真环境和物理交互；RViz 主要负责 ROS 数据的可视化。

### 2. URDF、TF2 与 RViz

```text
URDF / Xacro
     │
     ▼
机器人模型
     │
     ▼
robot_state_publisher
     │
     ▼
TF 坐标变换
     │
     ▼
RViz 可视化
```

RViz 中需要正确设置 Fixed Frame，并确认机器人模型、TF 和传感器话题正常。

### 3. 实践命令

检查 RViz 是否安装：

```bash
source /opt/ros/jazzy/setup.bash
ros2 pkg list | grep rviz2
```

启动 RViz：

```bash
rviz2
```

若模型无法显示，检查 `robot_description`、TF 坐标变换、Fixed Frame 以及相关话题。

## 四、第20讲：RQt 图形化调试

### 1. 核心知识

RQt 是 ROS 2 的图形化调试框架，可以通过插件观察和调试 ROS 2 系统。

| 工具 | 用途 |
|---|---|
| `rqt_graph` | 查看节点与话题连接 |
| `rqt_console` | 查看和过滤日志 |
| `rqt_plot` | 绘制数值话题曲线 |
| Service Caller | 通过图形界面调用服务 |

RQt 可以辅助检查节点是否运行、话题是否连接、数据是否变化以及服务是否正常响应。

### 2. 安装与启动

ROS 2 Jazzy 使用连字符格式的软件包名称。

安装基础包：

```bash
sudo apt update
sudo apt install ros-jazzy-rqt
```

如需安装相关插件：

```bash
sudo apt install '~nros-jazzy-rqt*'
```

加载环境并启动：

```bash
source /opt/ros/jazzy/setup.bash

rqt
```

查看节点关系：

```bash
rqt_graph
```

此前使用下划线格式的 `ros_jazzy_rqt` 安装失败，原因是软件包名称不正确。正确命令的安装和启动结果仍需验证。

## 五、三讲的知识联系

| 技术 | 主要作用 |
|---|---|
| URDF/Xacro | 描述机器人结构 |
| TF2 | 管理和查询坐标变换 |
| Gazebo | 仿真机器人与环境的交互 |
| RViz | 可视化机器人模型和传感器数据 |
| RQt | 图形化调试节点、话题、服务和日志 |

对于四足机器人开发，这些工具可以用于机器人模型检查、传感器数据观察、节点通信排查和仿真系统调试。

## 六、学习总结

本次学习覆盖 Gazebo 仿真、RViz 三维可视化和 RQt 图形化调试，进一步串联了 ROS 2、TF2 和 URDF 的知识。

已验证：Gazebo 集成包安装成功，Gazebo Sim 版本为 `8.15.0`。

待验证：Gazebo 世界启动、机器人模型加载、RViz 实际显示，以及 RQt 安装和插件运行。