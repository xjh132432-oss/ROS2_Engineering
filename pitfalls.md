# ROS 2 Engineering 踩坑记录

记录 ROS 2 学习与开发过程中遇到的真实问题、原因分析、解决方法和验证结果，避免重复踩坑，并沉淀可复用的工程经验。

开发环境：Ubuntu 24.04 ARM64、ROS 2 Jazzy、VMware Fusion。

## 1. Ubuntu 虚拟机架构选择错误

**问题现象**

最初尝试使用 x86_64（AMD64）Ubuntu 镜像在 Apple Silicon Mac 上创建虚拟机，遇到架构不兼容问题。

**原因分析**

Apple Silicon 使用 ARM64 架构，原生虚拟化应优先选择 ARM64 系统镜像，而不是 AMD64 镜像。

**解决方法**

使用 Ubuntu 24.04 ARM64 Desktop 镜像重新创建虚拟机。

**经验总结**

- Apple Silicon 主机优先使用 ARM64 系统镜像。
- 安装软件时注意软件包是否支持 ARM64。
- 遇到安装失败时，先确认操作系统架构。

**状态：已解决。**

## 2. ROS 2 发行版与教程版本不一致

**问题现象**

学习古月居《ROS 2 入门21讲》时，教程中的部分环境和命令基于 Humble，而当前系统安装的是 Jazzy。

**原因分析**

ROS 2 不同发行版在依赖包、工具版本和部分 API 上存在差异，不能直接假定教程中的所有步骤都适用于当前环境。

**解决方法**

- 确认当前发行版：

  ```bash
  echo $ROS_DISTRO
  ```

- 初始化 Jazzy 环境：

  ```bash
  source /opt/ros/jazzy/setup.bash
  ```

- 安装依赖时使用与当前发行版对应的软件包名称。
- 遇到命令或 API 不兼容时，先核对 Jazzy 对应文档。

**经验总结**

遇到教程与实际环境不一致时，先确认版本，再判断是操作错误、依赖缺失还是发行版差异。

**状态：持续注意。**

## 3. `colcon build` 显示 0 packages finished

**问题现象**

执行：

```bash
colcon build
```

返回：

```text
Summary: 0 packages finished
```

同时，`colcon list` 没有列出预期的功能包。

**原因分析**

工作空间中的 `src` 目录可能为空、功能包不在当前扫描路径下，或者目录中存在影响包发现的忽略标记。

本次排查中，教程仓库存在 `COLCON_IGNORE`，可能导致其内部功能包未被正常发现。

**排查方法**

```bash
cd ~/dev_ws

ls -la
ls -la src
colcon list

find src -name package.xml
find src -name COLCON_IGNORE
```

**解决思路**

- 确认当前目录是工作空间根目录。
- 确认 `src` 中存在有效的 ROS 2 功能包。
- 检查 `COLCON_IGNORE` 的作用范围。
- 必要时明确指定构建路径，例如：

  ```bash
  colcon build --base-paths src/ros2_21_tutorials/learning_tf
  ```

  上述路径应替换为实际包含功能包的目录。

**经验总结**

`0 packages finished` 不代表构建成功了所有预期模块。应先确认包发现结果，再执行构建。

**状态：已定位相关排查方向，后续根据实际构建结果确认。**

## 4. ROS 2 自定义服务调用

**问题现象**

学习 Service 通信时，需要确认服务请求、响应和接口类型是否正确。

**验证命令**

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 3, b: 5}"
```

**实际结果**

返回结果中的 `sum` 为 `8`。

**经验总结**

- 服务名、服务类型和请求字段必须匹配。
- 请求内容需要符合接口定义。
- 可使用以下命令辅助排查：

  ```bash
  ros2 service list
  ros2 service type /add_two_ints
  ros2 interface show example_interfaces/srv/AddTwoInts
  ```

**状态：已验证。**

## 5. TF2 坐标变换查询

**问题现象**

查询坐标变换时，需要确认目标坐标系、源坐标系以及变换方向。

**验证方式**

先发布静态坐标变换：

```bash
source /opt/ros/jazzy/setup.bash

ros2 run tf2_ros static_transform_publisher \
  --x 10 --y 5 --z 0 \
  --roll 0 --pitch 0 --yaw 0 \
  --frame-id world \
  --child-frame-id house
```

再打开另一个终端：

```bash
source /opt/ros/jazzy/setup.bash
ros2 run tf2_ros tf2_echo world house
```

**预期结果**

平移量为：

```text
Translation: [10.000, 5.000, 0.000]
```

**经验总结**

- `world` 是父坐标系，`house` 是子坐标系。
- `tf2_echo` 的两个参数分别为目标坐标系和源坐标系。
- 查询前确认发布节点仍在运行，并检查坐标系名称是否一致。

**状态：已整理验证方法，实际运行状态以终端结果为准。**

## 6. Gazebo Classic 与 Gazebo Sim 混淆

**问题现象**

执行 `gazebo` 或 `gz` 命令时，发现命令不存在，无法直接启动仿真。

**原因分析**

Gazebo Classic 与新一代 Gazebo Sim 是不同的仿真体系，命令、软件包和 ROS 集成方式存在区别。

当前环境使用 ROS 2 Jazzy，应优先使用对应的 Gazebo Sim 与 `ros_gz` 工具链。

**排查命令**

```bash
source /opt/ros/jazzy/setup.bash

command -v gz
gz sim --versions

ros2 pkg list | grep -E 'ros_gz|gz_sim'
```

**处理方法**

根据当前环境检查和安装对应的软件包，例如：

```bash
sudo apt update
sudo apt install ros-jazzy-ros-gz
```

安装后重新加载 ROS 2 环境，并检查 `gz` 命令是否可用。

**经验总结**

不要将 Gazebo Classic 的启动命令、安装方式直接套用到 Gazebo Sim。先确认仿真器版本与 ROS 集成包。

**状态：已确认当前环境采用 Gazebo Sim 生态；完整仿真启动仍需验证。**

## 7. RQt 软件包安装失败

**问题现象**

执行：

```bash
sudo apt install ros_jazzy_rqt
```

系统提示找不到软件包。

**原因分析**

ROS 2 二进制软件包通常采用连字符分隔发行版与包名，不能随意使用下划线代替。

**正确命令**

```bash
sudo apt update
sudo apt install ros-jazzy-rqt
```

需要额外插件时，可以检查对应的软件包：

```bash
sudo apt install '~nros-jazzy-rqt*'
```

**经验总结**

安装 ROS 2 软件包时，优先从当前发行版的官方文档或软件包索引确认准确名称。

**状态：已定位命名错误；安装与插件可用性需要根据实际结果确认。**

## 8. ROS 2 参数查询的节点名与参数名

**问题现象**

执行参数查询时，出现缺少参数名或 `Node not found` 等错误。

例如：

```bash
ros2 param get background_b
```

提示缺少 `parameter_name`。

**原因分析**

`ros2 param get` 需要两个参数：节点名称和参数名称。

**正确格式**

```bash
ros2 param get <node_name> <parameter_name>
```

例如：

```bash
ros2 param get /sim background_b
```

该示例只有在 `/sim` 节点实际存在且具有 `background_b` 参数时才有效。

**排查方法**

```bash
ros2 node list
ros2 param list /sim
```

如果节点不存在，应先检查启动终端、节点实际名称和当前 ROS 2 环境。

**经验总结**

节点名称与参数名称是两个不同概念。先确认节点存在，再查询其参数。

**状态：已明确命令格式，具体参数以实际节点为准。**

## 9. Python 模块导入失败

**问题现象**

ROS 2 Python 节点运行时出现 `ModuleNotFoundError` 或类似导入错误。

**排查方法**

```bash
echo $ROS_DISTRO
which python3
python3 --version
ros2 pkg list | grep <package_name>
```

针对 `tf_transformations` 等模块，还需要确认当前 ROS 2 发行版对应的依赖包是否已安装。

**经验总结**

- 确认终端已加载 ROS 2 环境。
- 确认使用的 Python 解释器与 ROS 2 环境匹配。
- 区分系统 Python 包、虚拟环境包与 ROS 2 软件包。
- 不要在没有确认依赖来源前，直接通过 `pip` 随意安装同名包。

**状态：记录排查流程，具体依赖需根据错误信息确认。**

---

## 通用排错流程

遇到 ROS 2 问题时，优先按照以下顺序排查：

1. **环境**：确认 Ubuntu 架构、ROS 2 发行版和环境变量。
2. **路径**：确认工作目录、工作空间和功能包位置。
3. **依赖**：确认软件包、Python 模块和接口依赖。
4. **发现**：确认节点、话题、服务或功能包是否存在。
5. **配置**：检查名称、参数、QoS 和启动文件。
6. **验证**：使用 CLI、日志和最小化示例复现问题。
7. **记录**：保留错误信息、原因、解决方法及验证结果。

## 记录原则

- 只将实际遇到或实际验证的问题记录为已解决。
- 区分确定原因、可能原因和待验证假设。
- 修复后记录可复现的命令和结果。
- 遇到新问题时持续追加条目，避免覆盖已有经验。

> 踩坑记录的价值不仅在于保存解决命令，更在于理解错误产生的原因，并形成下一次可以复用的排查方法。