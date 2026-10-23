# 古月居 ROS 2 入门21讲｜第1–20讲总复习

这次不再按每天的学习记录复述，而是把已经学过的 ROS 2 知识串成一套完整体系，重点放在原理、命令、代码、模块之间的联系，以及四足机器人项目中的实际用途。

**当前环境：** Ubuntu 24.04 ARM64 + ROS 2 Jazzy，工作空间为 `~/dev_ws`。

**版本注意：** 教程部分内容基于 ROS 2 Humble，而当前环境使用 Jazzy。复习时以 ROS 2 通用原理为主，涉及具体软件包、Launch 文件和仿真插件时注意版本差异。

---

## 一、先建立整体知识地图

### ROS 2 基础架构

节点 · 话题 · 服务 · 动作 · 参数

### 通信系统

DDS · QoS · 分布式通信

### 工程系统

工作空间 · 功能包 · colcon · ament

### 坐标与模型

TF2 · URDF · Xacro

### 仿真与调试

Gazebo · RViz · RQt

### 四足机器人系统集成

传感器 → 感知 → 坐标转换 → 决策 → 控制

真正掌握 ROS 2，不是记住二十讲的命令，而是能解释：一个节点如何运行、节点之间如何交换数据、数据处于什么坐标系、机器人模型如何描述，以及整个系统如何构建、启动和调试。

---

## 二、第1–20讲知识总览

下面按已经学习的课程主题归类；部分小节的实际运行情况与概念学习情况分开处理。

| 课程范围 | 复习主题 | 必须掌握的内容 |
|---|---|---|
| 第1–3讲 | ROS 2 基本概念与基础通信 | ROS 2 架构、节点、通信机制 |
| 第4–6讲 | 命令行、环境与工程结构 | ROS CLI、工作空间、功能包、构建 |
| 第7–9讲 | 话题与服务通信 | Publisher/Subscriber、Service/Client、接口 |
| 第10–12讲 | 自定义接口、参数与动作通信 | 接口生成、参数读写、长任务反馈 |
| 第13讲 | 分布式通信 | 不同设备上的节点发现与通信 |
| 第14讲 | DDS/QoS | 可靠性、历史记录、队列深度、兼容性 |
| 第15讲 | Launch | 多节点启动、参数配置、组合启动 |
| 第16讲 | TF2 | 坐标系、静态与动态变换、查询 |
| 第17讲 | URDF/Xacro | Link、Joint、模型结构 |
| 第18讲 | Gazebo | 机器人仿真、物理属性、模型加载 |
| 第19讲 | RViz | 机器人、TF、传感器数据可视化 |
| 第20讲 | RQt | 节点图、日志、话题和服务调试 |

此前实际验证过的一个关键成果是：自定义服务学习中，`/add_two_ints` 接收 `3` 和 `5` 后返回 `sum=8`。

第13–15讲的部分 QoS 查询、Launch 示例，以及第18–20讲的仿真和图形工具仍需要通过实际运行确认。

---

## 三、工程基础：工作空间、功能包与构建

这是后面所有 ROS 2 开发的地基。尤其要分清楚：源码存在，不代表包已经被构建；构建成功，也不代表节点一定能运行。

### 1. 工作空间的标准结构

```text
dev_ws/
├── src/                 # 源码
│   ├── learning_service/
│   │   ├── package.xml
│   │   ├── CMakeLists.txt
│   │   ├── src/
│   │   └── srv/
│   └── ros2_21_tutorials/
├── build/               # 构建中间文件
├── install/             # 安装产物
└── log/                 # 构建日志
```

- `src/`：放置功能包源码。
- `build/`：CMake 等构建系统产生的中间文件。
- `install/`：安装后的程序、库、资源和环境配置。
- `log/`：构建过程日志。

`colcon build` 默认会在 `src` 中发现符合规则的功能包。如果出现 `0 packages finished`，应该先检查包发现情况，而不是直接认为构建成功。

### 2. 必须熟悉的命令

```bash
# 加载 ROS 2 环境
source /opt/ros/jazzy/setup.bash

# 进入工作空间
cd ~/dev_ws

# 查看源码中的包
colcon list

# 构建
colcon build

# 加载工作空间的安装环境
source ~/dev_ws/install/setup.bash

# 查看包
ros2 pkg list

# 查看指定包的位置
ros2 pkg prefix 包名

# 构建指定功能包
colcon build --packages-select 包名
```

注意：

- `colcon list` 用于查看构建工具发现的包。
- `ros2 pkg list` 用于查看当前 ROS 2 环境能识别的包。
- 两者用途不同，结果不一定完全相同。
- 如果包位于带有 `COLCON_IGNORE` 的目录下，可能需要指定正确的构建路径，或者调整仓库布局。

### 3. `package.xml` 与 `CMakeLists.txt`

| 文件 | 职责 |
|---|---|
| `package.xml` | 包元信息、依赖声明、构建类型 |
| `CMakeLists.txt` | 编译目标、依赖查找、安装规则 |
| `setup.py` | Python 包的安装与入口配置，取决于包结构 |
| `setup.cfg` | Python 可执行入口的安装位置等配置 |

C++ ROS 2 包常见配置：

```cmake
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)

add_executable(node_name src/node_name.cpp)
ament_target_dependencies(node_name rclcpp)

install(TARGETS
  node_name
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()
```

这里有三个重要点：

1. `find_package()` 找到依赖。
2. `ament_target_dependencies()` 为目标配置 ROS 2 依赖。
3. `install()` 决定构建后的可执行程序是否能通过 `ros2 run` 找到。

为什么有时编译成功，却运行时提示找不到可执行文件？一个常见原因就是缺少正确的安装规则，或没有加载对应工作空间的 `install/setup.bash`。

---

## 四、ROS 2 通信：节点、话题、服务、动作与参数

### 1. 节点 Node

节点是 ROS 2 程序中的基本运行单元。例如：

- 摄像头节点负责发布图像。
- 视觉节点负责检测目标。
- 控制节点负责产生控制指令。

查看节点：

```bash
ros2 node list
ros2 node info /节点名称
```

### 2. 四种常见通信机制

**话题 Topic**

发布者持续发布数据，订阅者接收数据，适合传感器流和状态信息。

例如：相机图像、机器人姿态、关节状态。

**服务 Service**

客户端发送请求，服务端返回响应，适合短时请求—响应操作。

例如：查询结果、触发一次配置操作。

**动作 Action**

适合耗时任务，可以反馈进度、返回最终结果，并支持取消。

例如：导航到目标点、执行一段运动任务。

**参数 Parameter**

保存节点运行配置，例如阈值、频率和模式开关。

例如：视觉置信度阈值、控制器参数。

判断题：如果一个任务可能持续数十秒，并且需要反馈进度和支持取消，应该优先考虑哪种通信机制？

答案：Action。

### 3. 话题常用命令

```bash
ros2 topic list
ros2 topic info /话题名
ros2 topic echo /话题名
ros2 topic hz /话题名
ros2 topic type /话题名
```

含义分别是查看话题列表、查看话题信息、查看消息内容、测量发布频率和查看消息类型。

### 4. 服务与接口

此前实际验证过的服务命令：

```bash
ros2 service call \
  /add_two_ints \
  example_interfaces/srv/AddTwoInts \
  "{a: 3, b: 5}"
```

预期返回：

```yaml
sum: 8
```

自定义服务文件 `AddTwoInts.srv`：

```text
int64 a
int64 b
---
int64 sum
```

`---` 上方是请求字段，下方是响应字段。

对于自定义接口，还要注意：接口生成、构建依赖和服务端/客户端代码是三个不同环节。仅创建 `.srv` 文件，并不会自动让 ROS 2 识别该接口。

### 5. 参数操作

```bash
ros2 param list
ros2 param get /节点名 参数名
ros2 param set /节点名 参数名 值
ros2 param dump /节点名
```

此前遇到过 `Node not found`。排查时先运行 `ros2 node list`，确认节点实际名称，而不是假设节点名称等于参数名称。

---

## 五、DDS、QoS、分布式通信与 Launch

### 1. DDS 是什么？

ROS 2 的常见通信实现依赖 DDS（Data Distribution Service）中间件。它帮助节点进行发现、数据传输和通信管理。

可以将通信过程简化为：

```text
ROS 2 节点
    ↓
ROS 2 Client Library（rclcpp / rclpy）
    ↓
ROS 2 中间层
    ↓
DDS 实现
    ↓
网络上的其他 ROS 2 节点
```

不需要一开始就研究 DDS 源码，但需要理解：ROS 2 节点并不是通过简单的函数调用来完成跨设备通信的。

### 2. QoS：为什么发布者与订阅者有时收不到数据？

QoS（Quality of Service）控制通信策略，常见配置包括：

| 策略 | 含义 |
|---|---|
| Reliability | 可靠传输或尽力而为 |
| Durability | 是否向后来加入的订阅者保留并提供历史数据 |
| History | 如何保存历史消息 |
| Depth | 历史消息队列的深度 |
| Deadline | 期望的数据更新周期 |
| Lifespan | 消息允许保留的最长时间 |

最常见的两组配置：

- `RELIABLE`：尽可能确保消息可靠送达。
- `BEST_EFFORT`：尽力发送，不保证每条消息都送达。

对于传感器数据，`BEST_EFFORT` 常用于对时效性要求高、允许少量丢帧的场景；对必须可靠交付的数据，则可能选择 `RELIABLE`。

发布者和订阅者的 QoS 必须兼容。比如，可靠性策略不兼容就可能导致通信无法建立。

检查命令：

```bash
ros2 topic info /话题名 --verbose
```

排查时应同时检查发布者和订阅者的 QoS，而不是只看话题是否存在。

### 3. 分布式通信

ROS 2 节点可以运行在不同设备上。例如：

```text
机器人电脑
  ├── 相机节点
  └── 控制节点
       │ 网络
       ▼
开发电脑
  ├── 视觉节点
  └── RViz
```

跨设备通信需要关注：

- 网络是否连通。
- ROS 2 发现机制是否正常。
- `ROS_DOMAIN_ID` 是否一致。
- DDS 网络配置和防火墙是否允许通信。
- 两端的话题类型与 QoS 是否兼容。

查看环境变量：

```bash
echo "$ROS_DOMAIN_ID"
echo "$RMW_IMPLEMENTATION"
```

空值不一定代表故障，可能只是使用默认配置。

### 4. Launch：如何一次启动多个节点？

Launch 用于组织多个节点、参数、配置文件及启动条件。

命令行启动单个节点：

```bash
ros2 run 包名 可执行文件名
```

通过 Launch 启动系统：

```bash
ros2 launch 包名 文件名.launch.py
```

例如，机器人系统可能同时启动相机、TF 发布器、视觉节点和控制节点。Launch 能将这些启动步骤集中管理，减少手动开多个终端的工作量。

复习 Launch 时重点掌握：

- `Node`：启动节点。
- `LaunchDescription`：组织启动动作。
- 参数传递：为节点设置运行参数。
- YAML 参数文件：集中管理配置。
- Include：复用其他 Launch 文件。
- 条件与启动参数：按需要决定启动哪些组件。

Launch 负责启动和组织系统，不会自动修复节点内部的逻辑错误，也不保证所有依赖都已经正确安装。

---

## 六、TF2 与 URDF/Xacro：机器人空间关系

这两讲是从普通 ROS 2 程序进入机器人开发的重要转折。

### 1. TF2：坐标变换

机器人中存在多个坐标系，例如：

```text
map
 └── odom
      └── base_link
           ├── camera_link
           └── lidar_link
```

这是一种示意结构，不代表每台机器人都采用完全相同的坐标树。

- `map`：常用于全局地图坐标。
- `odom`：常用于连续的局部里程计坐标。
- `base_link`：机器人本体参考坐标系。
- `camera_link`：相机本体坐标系。
- `lidar_link`：激光雷达本体坐标系。

TF2 维护坐标系之间的变换，使系统可以将一个坐标系中的数据转换到另一个坐标系中。

### 2. 静态变换与动态变换

| 类型 | 适用场景 |
|---|---|
| 静态变换 | 相机固定安装在机器人底盘上 |
| 动态变换 | 机器人移动、关节转动 |

- `/tf`：主要传递动态变换。
- `/tf_static`：主要传递静态变换。

静态变换发布命令：

```bash
ros2 run tf2_ros static_transform_publisher \
  --x 10 --y 5 --z 0 \
  --roll 0 --pitch 0 --yaw 0 \
  --frame-id world \
  --child-frame-id house
```

另一个终端查询：

```bash
ros2 run tf2_ros tf2_echo world house
```

预期平移量为 `[10, 5, 0]`。

这里最容易混淆的是 `lookup_transform(target_frame, source_frame, time)` 的参数顺序：先目标坐标系，再源坐标系。它用于获取将源坐标系中的数据转换到目标坐标系所需的变换。

### 3. URDF：描述机器人结构

URDF 使用 XML 描述机器人部件与关节关系。

- `Link`：刚性部件。
- `Joint`：连接部件并定义运动关系。
- `Visual`：外观模型。
- `Collision`：碰撞模型。
- `Inertial`：质量、质心和惯性参数。

常见关节：

| 类型 | 运动特点 |
|---|---|
| `fixed` | 固定连接 |
| `revolute` | 有限角度旋转 |
| `continuous` | 连续旋转 |
| `prismatic` | 直线移动 |
| `floating` | 六自由度浮动 |
| `planar` | 平面运动 |

例如，相机固定在底盘上：

```xml
<joint name="camera_joint" type="fixed">
  <parent link="base_link"/>
  <child link="camera_link"/>
  <origin xyz="0.2 0 0.5" rpy="0 0 0"/>
</joint>
```

表示相机相对于底盘沿 X 轴偏移 0.2 米、沿 Z 轴偏移 0.5 米，没有相对旋转。

### 4. Xacro 的作用

Xacro 在 URDF 基础上提供变量、宏和文件包含机制，减少重复配置。

查看本机模型：

```bash
cd ~/dev_ws/src/ros2_21_tutorials/learning_urdf

cat urdf/mbot_base.urdf
cat urdf/mbot_base.xacro
xacro urdf/mbot_base.xacro
```

此前已找到 `mbot_base`、带相机、Kinect、激光雷达的模型，以及传感器相关 Xacro 文件。下一步要重点理解这些传感器如何通过 Link 和 Joint 接入机器人坐标树。

---

## 七、Gazebo、RViz、RQt：仿真和调试

### 1. 三个工具解决三个不同的问题

**Gazebo Sim：机器人在虚拟世界中如何运行？**

负责仿真世界、物理交互和模拟传感器。

**RViz：机器人和数据看起来是什么样？**

显示机器人模型、TF、图像、激光雷达、点云和地图。

**RQt：ROS 2 系统内部的连接是否正常？**

使用插件查看节点关系、日志、数值曲线以及服务调用。

你的环境已安装 `ros-jazzy-ros-gz`，并确认 Gazebo Sim 版本为 `8.15.0`。

### 2. 常用命令

```bash
# Gazebo Sim
ros2 launch ros_gz_sim gz_sim.launch.py gz_args:="empty.sdf"

# RViz
rviz2

# RQt
rqt

# 节点与话题图
rqt_graph
```

RQt 的正确安装命令是：

```bash
sudo apt install ros-jazzy-rqt
```

此前使用下划线形式 `ros_jazzy_rqt` 导致 APT 无法定位软件包。不要把正确命令写成已经安装成功，除非实际验证过。

### 3. Gazebo 仿真中的关键区别

- URDF 能够显示，不代表模型具备完整的物理仿真参数。
- Gazebo Classic 的 `gazebo_ros` 插件不一定兼容现代 Gazebo Sim。
- Jazzy 常见的现代集成方式是 Gazebo Harmonic + `ros_gz`。
- `ros_gz_bridge` 用于桥接 Gazebo Transport 与 ROS 2 数据，不等于所有话题都会自动互通。
- `use_sim_time: true` 让节点使用仿真时钟，涉及 TF 和传感器时间戳时尤其重要。

---

## 八、综合实战：从启动节点到机器人仿真

现在把前面二十讲串成一个完整系统。

假设目标是让四足机器人利用相机检测目标，并根据目标位置做出响应。

### 第一步：工程构建

功能包 · CMake · colcon · 环境加载

### 第二步：ROS 2 通信

相机话题 · 视觉节点 · 参数 · 服务 / 动作

### 第三步：空间关系

URDF/Xacro · TF2 · 相机坐标 → 机器人坐标

### 第四步：仿真与调试

Gazebo · RViz · RQt · Launch

### 第五步：机器人系统集成

感知结果 → 坐标转换 → 决策 → 控制

例如，视觉节点检测到目标在相机坐标系中的位置。它不能直接假设这个坐标就是机器人本体坐标系中的位置，需要利用 TF2 获取对应的坐标变换，再将结果提供给决策或控制节点。

这就是学习 ROS 2、计算机视觉和系统集成的交汇点。
