# Day04 Workflow

- 日期：2026-10-15
- 课程：古月居 ROS 2 入门第 7、8、9 讲
- 环境：Ubuntu + ROS 2 Jazzy

## 一、学习过程

### 1. 第 7 讲：话题通信（上）

学习 Topic 的基本通信模型，理解 Publisher、Subscriber 和 Topic 的作用，以及话题通信在传感器数据传输等场景中的应用。

涉及命令：

```bash
ros2 topic list
ros2 topic list -t
ros2 topic echo /topic_name
```

### 2. 第 8 讲：话题通信（下）

学习发布者和订阅者的基本编程结构，理解节点、消息、回调函数及 ROS 2 软件包构建流程。

涉及知识：

- ROS 2 节点。
- Publisher 与 Subscriber。
- 消息回调函数。
- package.xml。
- CMakeLists.txt。
- colcon build。

本次记录课程学习内容，不将未独立验证的代码实现记为已完成成果。

### 3. 第 9 讲：服务通信（上）

学习 Service 的请求—响应模型，理解 Client、Server、Request、Response 和 srv 服务接口文件。

## 二、命令行实践

### 1. 加载 ROS 2 环境

执行：

```bash
source /opt/ros/jazzy/setup.bash
```

成功进入 ROS 2 Jazzy 命令行操作环境。

### 2. 查看服务列表

执行：

```bash
ros2 service list
ros2 service list -t
```

执行时没有显示服务列表内容。

说明：当时没有发现可列出的服务，不能据此判断 ROS 2 安装失败。

### 3. 查看服务接口

执行：

```bash
ros2 interface show example_interfaces/srv/AddTwoInts
```

实际输出：

```text
int64 a
int64 b
---
int64 sum
```

结果：成功查看服务接口。

### 4. 第一次调用服务

尝试执行：

```bash
ros2 service call example_interfaces/srv/AddTwoInts "{a: 3,b: 5}"
```

出现错误：

```text
The passed service type is invalid
```

原因：命令参数没有按照正确的格式提供服务名称、服务类型和请求数据。

### 5. 第二次调用服务

执行：

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 3, b: 5}"
```

当时终端提示：

```text
waiting for service to become available...
```

说明客户端尚未发现可用的对应服务，仍在等待服务就绪。

### 6. 成功验证服务通信

服务可用后，再次执行：

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 3, b: 5}"
```

实际返回：

```text
requester: making request: example_interfaces.srv.AddTwoInts_Request(a=3, b=5)

response:
example_interfaces.srv.AddTwoInts_Response(sum=8)
```

验证结果：

- 请求参数：a = 3，b = 5。
- 服务响应：sum = 8。
- 请求发送成功。
- 响应接收成功。

## 三、本次完成情况

- [x] 学习第 7 讲：话题通信基础。
- [x] 学习第 8 讲：话题发布与订阅代码相关内容。
- [x] 学习第 9 讲：服务通信基础。
- [x] 查看 AddTwoInts 服务接口。
- [x] 完成 AddTwoInts 服务调用。
- [x] 验证 3 + 5 = 8。

## 四、本次问题与收获

### 问题

第一次调用服务时参数格式错误；之后调用时，由于服务尚未就绪，客户端等待服务可用。

### 收获

理解了服务名称、服务类型和请求数据的区别，并通过实际调用验证了 ROS 2 Service 的请求—响应流程。

## 五、下一步

下一次学习从古月居 ROS 2 入门第 10 讲继续。

本次学习截止第 9 讲，不提前记录后续课程内容。