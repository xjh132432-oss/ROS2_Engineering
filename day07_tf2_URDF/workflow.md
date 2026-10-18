# Workflow｜古月居 ROS2 第16、17讲

## 一、学习内容

### 第16讲：TF2 坐标变换
- [x] 学习坐标系、父子坐标系与 TF 树。
- [x] 理解静态变换与动态变换的区别。
- [x] 了解 `/tf` 和 `/tf_static`。
- [x] 学习 `static_transform_publisher` 的使用方法。
- [x] 学习使用 `tf2_echo` 查询坐标变换。
- [x] 理解 `TransformStamped`、`StaticTransformBroadcaster` 和 `lookup_transform()`。
- [x] 阅读 `learning_tf` 相关源码。

### 第17讲：URDF 机器人模型
- [x] 学习 URDF 的基本结构。
- [x] 理解 Link、Joint、Visual、Collision 和 Inertial。
- [x] 了解 `fixed`、`revolute`、`continuous`、`prismatic` 等关节类型。
- [x] 学习 Xacro 的基本用途。
- [x] 理解 `robot_state_publisher`、`joint_state_publisher` 和 RViz 的作用。
- [x] 阅读 `learning_urdf` 相关目录及示例文件。

## 二、实践任务

### 第16讲：TF2
- [ ] 运行静态坐标变换发布器。
- [ ] 使用 `tf2_echo world house` 验证平移和旋转数据。
- [ ] 运行教程中的 TF2 综合示例。

### 第17讲：URDF
- [ ] 对比基础机器人模型与带相机、Kinect、激光雷达的模型。
- [ ] 执行 `xacro urdf/mbot_base.xacro` 检查展开结果。
- [ ] 根据本机依赖情况运行模型展示 Launch 文件。
- [ ] 在 RViz 中检查机器人模型和坐标系。

> 说明：知识点勾选表示已学习相关内容；实践任务需在实际运行验证后再勾选。

## 三、Git 提交流程

在工作空间或对应 Git 仓库中执行：

```bash
git status
git diff