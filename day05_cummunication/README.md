# ROS2 入门21讲 Day05｜第10—12讲

## 一、学习内容

- 第10讲：自定义服务通信（Service）
- 第11讲：参数通信（Parameter）
- 第12讲：动作通信（Action）

学习环境：ROS 2 Humble

## 二、第10讲：自定义服务通信

### 1. 服务接口

使用 `.srv` 文件定义请求和响应，通过 `---` 分隔。

`AddTwoInts.srv`：

```text
int64 a
int64 b
---
int64 sum
```

### 2. 开发流程

1. 创建功能包及 `srv` 目录。
2. 编写自定义服务接口。
3. 配置 `package.xml` 和 `CMakeLists.txt`。
4. 使用 ROSIDL 生成接口代码。
5. 编写 C++ 或 Python 服务端和客户端。
6. 使用 `colcon build` 编译并加载工作空间。
7. 启动节点，验证请求和响应。

### 3. 核心 API

| API | 作用 |
|---|---|
| `create_service()` | 创建服务端 |
| `create_client()` | 创建客户端 |
| `wait_for_service()` | 等待服务上线 |
| `async_send_request()` | 异步发送请求 |
| `rclcpp::spin()` | 处理节点事件 |
| `rclpy.spin()` | 处理 Python 节点事件 |

### 4. 注意事项

- 正确配置接口生成依赖。
- 修改接口后重新编译。
- 客户端应等待服务上线。
- 节点需要通过 `spin()` 处理回调。
- 服务端没有响应时，客户端可能持续等待。

## 三、第11讲：参数通信

### 1. 参数概念

Parameter 是绑定在节点上的键值对，用于保存节点配置，例如传感器频率、机器人尺寸和 PID 参数。

参数支持整数、浮点数、布尔值、字符串及数组等类型，适合配置管理，不适合替代 Topic 传输高频数据。

### 2. 常用命令

```bash
# 查看节点
ros2 node list

# 查看节点参数
ros2 param list /节点名

# 获取参数
ros2 param get /节点名 参数名

# 修改参数
ros2 param set /节点名 参数名 值

# 保存参数
ros2 param dump /节点名 --output 参数.yaml

# 加载参数
ros2 param load /节点名 参数.yaml
```

### 3. C++ 参数操作

```cpp
// 声明参数
this->declare_parameter<int>("my_parameter", 10);

// 获取参数
int value = this->get_parameter("my_parameter").as_int();

// 修改参数
this->set_parameter(
    rclcpp::Parameter("my_parameter", 20));
```

### 4. Python 参数操作

```python
# 声明参数
self.declare_parameter("my_parameter", 10)

# 获取参数
value = self.get_parameter("my_parameter").value

# 修改参数
self.set_parameters([
    rclpy.parameter.Parameter(
        "my_parameter",
        value=20
    )
])
```

### 5. 注意事项

- ROS 2 Humble 默认要求先声明参数，再读取参数。
- 参数类型必须匹配。
- 参数不会自动持久化到磁盘。
- 需要长期保存时，可以使用 YAML 文件。

## 四、第12讲：动作通信 Action

### 1. Action 概念

Action 适用于长时间运行的任务，支持目标发送、执行反馈、最终结果和取消请求。

典型场景：机器人导航、机械臂运动和路径跟踪。

### 2. Action 接口

`.action` 文件由三个部分组成：

```text
# Goal
目标字段
---
# Result
结果字段
---
# Feedback
反馈字段
```

- Goal：客户端发送的目标。
- Result：任务结束后返回的结果。
- Feedback：任务执行期间的反馈。

### 3. 客户端与服务端

**Action Client**

- 发送目标。
- 接收反馈。
- 获取最终结果。
- 请求取消任务。

**Action Server**

- 接收并执行目标。
- 持续发送反馈。
- 返回最终结果。
- 处理取消请求。

### 4. 常用命令

```bash
# 查看 Action 列表
ros2 action list

# 查看接口定义
ros2 interface show turtlesim/action/RotateAbsolute

# 发送目标
ros2 action send_goal \
  /turtle1/rotate_absolute \
  turtlesim/action/RotateAbsolute \
  "{theta: 1.57}"

# 查看执行反馈
ros2 action send_goal \
  --feedback \
  /turtle1/rotate_absolute \
  turtlesim/action/RotateAbsolute \
  "{theta: 1.57}"
```

`1.57` 弧度约等于 90°。

### 5. 执行流程

1. 客户端发送 Goal。
2. 服务端接受目标并执行任务。
3. 服务端持续发送 Feedback。
4. 任务结束后返回 Result。
5. 客户端可以请求取消，服务端根据任务状态处理取消操作。

## 五、三大通信机制对比

| 对比维度 | Topic | Service | Action |
|---|---|---|---|
| 接口文件 | `.msg` | `.srv` | `.action` |
| 通信模型 | 发布—订阅 | 请求—响应 | 目标—反馈—结果 |
| 主要用途 | 连续数据传输 | 一次性请求 | 长时间任务 |
| 进度反馈 | 通过消息持续发布 | 无专门进度机制 | 支持 Feedback |
| 取消机制 | 无内置任务取消机制 | 无内置任务取消机制 | 支持取消请求 |
| 应用场景 | 图像、雷达、里程计 | 查询、开关、单次计算 | 导航、机械臂、轨迹执行 |

## 六、学习总结

1. 理解自定义 `.srv` 接口及服务端、客户端的基本结构。
2. 掌握参数声明、读取、修改及 YAML 持久化的基本方法。
3. 理解 Action 的 Goal、Feedback 和 Result。
4. 学会根据实际任务选择 Topic、Service 或 Action。
5. 为后续学习 ROS 2 节点生命周期、TF 坐标变换及机器人系统集成打下基础。