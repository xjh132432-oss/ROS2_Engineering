# Day04：ROS 2 通信机制基础

- 学习课程：古月居 ROS 2 入门第 7、8、9 讲
- 学习日期：2026-10-15
- 操作系统：Ubuntu
- ROS 2 版本：Jazzy

## 一、学习目标

学习 ROS 2 的两种基础通信机制：Topic（话题）和 Service（服务），理解通信模型、相关角色及其使用场景，并通过命令行验证服务通信。

## 二、学习内容

### 第 7 讲：话题通信（上）

理解 ROS 2 Topic 的基本通信模型。

核心概念：

- Publisher：发布消息。
- Subscriber：订阅消息。
- Topic：传输消息的通信通道。
- DDS：提供底层通信与节点发现等能力。

话题通信适用于持续传输数据的场景，例如摄像头图像、激光雷达数据和机器人里程计。

常用命令：

```bash
ros2 topic list
ros2 topic list -t
ros2 topic echo /topic_name
```

### 第 8 讲：话题通信（下）

学习 ROS 2 发布者与订阅者的代码实现思路。

核心概念：

- ROS 2 节点的基本结构。
- 发布者与订阅者的创建。
- 消息发布与接收。
- 回调函数。
- package.xml 与 CMakeLists.txt。
- 使用 colcon 构建 ROS 2 软件包。

本次完成课程学习，未将未经验证的独立代码实现记为实践成果。

### 第 9 讲：服务通信（上）

学习 ROS 2 Service 的请求—响应通信模型。

核心概念：

- Client：发送请求并接收响应。
- Server：处理请求并返回响应。
- Request：请求数据。
- Response：响应数据。
- srv：描述请求与响应数据结构的服务接口文件。

Topic 与 Service 的区别：

| 对比项 | Topic | Service |
|---|---|---|
| 通信模型 | 发布—订阅 | 请求—响应 |
| 主要角色 | Publisher、Subscriber | Client、Server |
| 典型用途 | 持续传输数据 | 请求处理并获取结果 |
| 接口文件 | msg | srv |

服务适用于短时间内可以完成的请求。对于需要持续反馈、支持取消的长时间任务，可以考虑 ROS 2 Action。

## 三、实践成果

本次使用 ROS 2 Jazzy 验证了 AddTwoInts 服务。

### 1. 查看服务接口

执行：

```bash
ros2 interface show example_interfaces/srv/AddTwoInts
```

接口定义：

```text
int64 a
int64 b
---
int64 sum
```

其中，分隔线 `---` 上方是请求字段，下方是响应字段。

### 2. 调用服务

执行：

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 3, b: 5}"
```

实际返回结果：

```text
response:
example_interfaces.srv.AddTwoInts_Response(sum=8)
```

### 3. 验证结论

- 成功查看 AddTwoInts 服务接口。
- 成功发送两个整数作为请求参数。
- 服务返回计算结果 8。
- 验证了 ROS 2 服务的请求—响应通信流程。

计算结果：

```text
3 + 5 = 8
```

## 四、本次总结

本次学习了 ROS 2 Topic 与 Service 的基本概念，并完成 AddTwoInts 服务的命令行调用验证。

通过实践，理解了服务名称、服务类型和请求数据的作用，以及客户端等待服务可用与成功接收响应之间的区别。

## 五、后续计划

继续按照古月居 ROS 2 入门课程顺序学习，从第 10 讲开始。

后续逐步完成 ROS 2 C++ 节点编写、构建和运行验证。