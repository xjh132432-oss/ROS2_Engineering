# Workflow — ROS 2 阶段七：TF2 与命令行复习

- 日期：2026-10-23
- 环境：Ubuntu 24.04 ARM64 / ROS 2 Jazzy / VMware Fusion
- 工作空间：`~/dev_ws`
- 本次主题：命令行复习与阶段七 TF2 坐标变换

## 1. 本次学习目标

- 熟练使用 ROS 2 CLI 检查节点、话题、服务、参数和动作。
- 能区分节点名、话题名、消息类型、服务类型与接口定义。
- 理解为什么基础 turtlesim 环境中可能看不到 `/tf` 和 `/tf_static`。
- 知道如何通过 `learning_tf` 的 Launch 示例启动 TF 相关节点并检查变换。
- 将学习记录整理为 README、workflow 与独立的 pitfalls 文档。

## 2. 命令行实操与知识点

### 2.1 检查 turtlesim 节点

执行过：

```bash
ros2 node info /turtlesim
```

观察到 `/turtlesim` 节点包含订阅、发布、服务端和动作服务端等端点。例如：

- 订阅 `/turtle1/cmd_vel`，类型为 `geometry_msgs/msg/Twist`
- 发布 `/turtle1/pose`，类型为 `turtlesim/msg/Pose`
- 提供 `/spawn`、`/kill` 等服务
- 提供 `/turtle1/rotate_absolute` 动作服务端

要点：`ros2 node info` 的参数必须是节点名；话题名和消息类型不能直接当作节点名传入。

### 2.2 区分话题、消息类型和服务

使用过的正确命令形式：

```bash
ros2 topic info /turtle1/cmd_vel
ros2 interface show geometry_msgs/msg/Twist
ros2 service type /spawn
ros2 interface show turtlesim/srv/Spawn
ros2 interface show turtlesim/srv/Kill
```

要点：

- `/turtle1/cmd_vel` 是话题名。
- `geometry_msgs/msg/Twist` 是消息类型，不是服务类型。
- `/spawn`、`/kill` 是服务名。
- `ros2 interface show` 用于查看消息、服务或动作接口的字段定义。

### 2.3 服务调用

成功调用过：

```bash
ros2 service call /spawn turtlesim/srv/Spawn
```

命令使用默认请求字段，返回中出现 `name='turtle2'`，说明服务为新海龟分配了名称。

随后调用过：

```bash
ros2 service call /kill turtlesim/srv/Kill "{name: turtle2}"
```

命令返回空的 `Kill_Response()`。这是正常现象：服务响应没有定义额外字段，不代表调用失败。若要进一步确认，可检查 turtlesim 窗口、相关话题列表，或再次调用删除同名海龟并观察服务端反馈。

### 2.4 参数导出

执行过：

```bash
ros2 param dump /turtlesim
```

输出包含 `background_r`、`background_g`、`background_b`、`holonomic`、`use_sim_time` 等参数，以及 `/parameter_events` 的 QoS 覆盖配置。

可选保存到文件：

```bash
ros2 param dump /turtlesim > turtlesim_params.yaml
```

要点：`param dump` 是导出节点参数配置；`param set` 修改运行时参数；`param load` 从 YAML 文件加载参数。加载前应确认文件内容和目标节点兼容。

### 2.5 检查 TF 话题

在基础 turtlesim 运行时执行过：

```bash
ros2 topic list
ros2 topic list | grep tf
```

当时话题列表包含 `/parameter_events`、`/rosout`、`/turtle1/cmd_vel`、`/turtle1/color_sensor`、`/turtle1/pose`；`grep tf` 没有输出。

结论：这表示当时可发现的话题中没有名称包含 `tf` 的条目。基础 turtlesim 不保证启动 TF 发布器，因此这一现象本身不说明 ROS 2 故障。

### 2.6 下一步验证 TF2 示例

此前的学习记录表明 `learning_tf` 中有 TF 相关节点和 `turtle_following_demo.launch.py`。在确认文件名与包安装状态后，可执行：

```bash
source /opt/ros/jazzy/setup.bash
source ~/dev_ws/install/setup.bash
ros2 launch learning_tf turtle_following_demo.launch.py
```

另开终端检查：

```bash
source /opt/ros/jazzy/setup.bash
source ~/dev_ws/install/setup.bash
ros2 node list
ros2 topic list | grep tf
```

此部分是建议的验证步骤；本 workflow 不将其记录为本次已经重新运行成功。

## 3. 本次形成的关键理解

- 节点、话题、消息类型、服务名和服务类型是不同概念，命令参数必须对应正确对象。
- Topic 传输连续消息；Service 处理请求并返回响应；Action 适合需要反馈、持续执行或支持取消的任务。
- `ros2 param dump` 能查看并导出节点参数，但不代表所有参数都能在运行时修改。
- `/turtle1/pose` 描述海龟位姿，不等同于 TF 变换。
- `/tf` 和 `/tf_static` 只有在相关 TF 发布器或系统组件启动并可被发现时才会出现。
- TF2 的 `lookup_transform(target_frame, source_frame, time)` 参数顺序是目标坐标系在前、源坐标系在后。

## 4. 提交前检查

```bash
git status
git diff
```

确认文档内容与实际操作一致后，再暂存并检查：

```bash
git add README.md workflow.md pitfalls.md
git diff --cached
git commit -m "docs: record ROS 2 stage 7 TF2 learning"
git status
```

以上提交命令是建议流程；只有实际执行并确认后，才能将提交标记为完成。
