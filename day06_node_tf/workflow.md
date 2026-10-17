
### workflow.md

```markdown
# Workflow｜2026-10-17 ROS 2 第 13–15 讲

## 一、今日任务

将古月居 ROS 2 入门 21 讲中的第 13、14、15 讲作为一天的学习内容，整理学习记录。

### 第 13 讲：分布式通信

- [x] 学习 ROS 2 分布式通信的基本概念。
- [x] 理解 DDS 在 ROS 2 通信架构中的作用。
- [x] 理解 ROS 2 与 ROS 1 在通信架构上的区别。

### 第 14 讲：DDS 与 QoS

- [x] 学习 DDS、RMW 与 ROS 2 上层接口的关系。
- [x] 理解 Reliability。
- [x] 理解 Durability。
- [x] 理解 History 与 Depth。
- [ ] 根据实际终端输出确认 QoS 查询实践结果。

### 第 15 讲：Launch

- [x] 阅读 `simple.launch.py`。
- [x] 理解 `LaunchDescription` 与 `Node` 的作用。
- [x] 确认 `learning_launch` 可被 ROS 2 包列表识别。
- [x] 阅读并梳理 Launch 的重映射、命名空间与参数配置方式。
- [ ] 根据实际运行结果确认各 Launch 示例的执行情况。
- [ ] 进一步验证 YAML 参数加载与 RViz 启动。

## 二、环境信息

- 操作系统：Ubuntu 24.04 ARM64
- 虚拟机：VMware Fusion
- ROS 2：Jazzy
- 工作空间：`~/dev_ws`
- 教程仓库：`~/dev_ws/src/ros2_21_tutorials`

加载环境：

```bash
source /opt/ros/jazzy/setup.bash