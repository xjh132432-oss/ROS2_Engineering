# Pitfalls — ROS 2 命令行与 TF2

本文记录学习中遇到或本次复习确认的常见问题。区分“实际遇到的问题”和“排查时需要记住的事项”，不把未验证的推断当作已确认故障。

## 1. 把话题名当作节点名

**现象**

```bash
ros2 node info /turtle/cmd_vel
```

提示找不到节点。

**原因**

`/turtle1/cmd_vel` 是话题名，不是节点名；本次命令中也与实际话题名不一致。

**正确做法**

```bash
ros2 node list
ros2 topic list
ros2 topic info /turtle1/cmd_vel
```

先确认对象类型，再选择对应的 `ros2 node` 或 `ros2 topic` 子命令。

## 2. 把消息类型传给 `ros2 node info`

**错误示例**

```bash
ros2 node info geometry_msgs/msg/Twist
```

**原因**

`geometry_msgs/msg/Twist` 是消息类型。`ros2 node info` 需要节点名称。

查看消息字段应使用：

```bash
ros2 interface show geometry_msgs/msg/Twist
```

## 3. 混淆 `ros2 interface` 与 `ros2 service`

**现象**

尝试执行不存在的 `ros2 service show`，或把话题名传给 `ros2 interface show`。

**原因**

这些命令的用途不同：

```bash
ros2 service list
ros2 service type /spawn
ros2 service info /spawn
ros2 interface show turtlesim/srv/Spawn
```

- `service list`：列出服务。
- `service type`：查看服务类型。
- `service info`：查看服务端点信息。
- `interface show`：查看接口字段定义。

`ros2 interface show` 的类型名应采用类似 `包名/msg/类型`、`包名/srv/类型` 的格式，而不是 `/turtle1/cmd_vel` 这样的运行时话题名。

## 4. 把 `geometry_msgs/msg/Twist` 当作服务类型

**现象**

```bash
ros2 service call /turtle1/cmd_vel geometry_msgs/msg/Twist "{...}"
```

报错称传入的类型不是服务。

**原因**

`/turtle1/cmd_vel` 是 Topic，`Twist` 是消息类型；它们不是 Service。

**正确做法：发布话题消息**

```bash
ros2 topic pub --once \
  /turtle1/cmd_vel \
  geometry_msgs/msg/Twist \
  "{linear: {x: 2.0}, angular: {z: 0.0}}"
```

Service 则使用 `ros2 service call <服务名> <服务类型> <请求内容>`。

## 5. `ros2 service call /spawn ...` 没有请求内容

**现象**

```bash
ros2 service call /spawn turtlesim/srv/Spawn
```

调用成功并返回 `name='turtle2'`。

**解释**

请求字段采用默认值，服务仍可能成功处理请求并自动分配名称。若需要指定位置、朝向和名称，可显式提供请求字段：

```bash
ros2 service call /spawn turtlesim/srv/Spawn \
  "{x: 3.0, y: 4.0, theta: 1.57, name: 'turtle3'}"
```

## 6. `Kill_Response()` 是空的

**现象**

```bash
ros2 service call /kill turtlesim/srv/Kill "{name: turtle2}"
```

返回：

```text
response: turtlesim.srv.Kill_Response()
```

**原因**

`Kill` 服务的响应没有额外字段，所以响应对象为空是正常的，不应仅凭空响应判断失败。

**验证方法**

- 观察 turtlesim 窗口中对应海龟是否消失。
- 检查相关话题是否还存在。
- 查看接口定义：

```bash
ros2 interface show turtlesim/srv/Kill
```

## 7. `ros2 param dump` 输出包含 `qos_overrides`

**现象**

参数导出结果除了 `background_r/g/b`、`holonomic` 等，还包含 `/parameter_events` 的 QoS 配置。

**解释**

参数转储可以包含节点参数中的嵌套配置，例如参数事件话题的 QoS 覆盖项；不要把它误认成普通业务参数。

```bash
ros2 param dump /turtlesim
ros2 param get /turtlesim background_r
```

导出文件再加载前，应检查目标节点与参数配置是否匹配。

## 8. `ros2 topic list | grep tf` 没有输出

**现象**

基础 turtlesim 话题列表中没有 `/tf` 或 `/tf_static`，运行 `grep tf` 也没有输出。

**原因与判断**

`grep tf` 只筛选名称中包含 `tf` 的当前可发现话题。基础 turtlesim 不保证启动 TF2 广播器，因此没有输出本身并不代表 ROS 2 出错。

**排查方法**

先确认当前运行的节点和 Launch：

```bash
ros2 node list
ros2 topic list
```

如果要验证教程中的 TF 示例，启动 `learning_tf` 中实际存在且已安装的 Launch 文件，再检查：

```bash
ros2 topic list | grep tf
```

`/turtle1/pose` 是 `turtlesim/msg/Pose`，不是 `/tf` 的替代品。

## 9. TF 命令中的坐标系顺序

查询变换时，常用命令形式：

```bash
ros2 run tf2_ros tf2_echo 目标坐标系 源坐标系
```

TF2 API 的对应参数顺序是：

```text
lookup_transform(target_frame, source_frame, time)
```

不要把目标坐标系和源坐标系顺序颠倒。执行前还要确认两个坐标系确实存在于当前 TF 树中。

## 10. 新终端找不到包或节点

**常见原因**

当前终端没有加载 ROS 2 或工作空间环境，或者包尚未构建/安装。

**排查步骤**

```bash
source /opt/ros/jazzy/setup.bash
source ~/dev_ws/install/setup.bash
echo "$ROS_DISTRO"
ros2 pkg list
ros2 node list
```

`colcon list` 关注源码目录中可发现的包，`ros2 pkg list` 关注当前环境能够识别的包。两者结果不同不一定是同一个问题，应结合构建与安装状态判断。
