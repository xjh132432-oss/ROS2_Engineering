# 2026-10-13 学习记录

## 今日目标
- 配置 Ubuntu 虚拟机
- 尝试安装 ROS 2 Humble
- 排查 Ubuntu 软件源和网络问题

## 今日完成
- 配置 VMware 虚拟机并启动 Ubuntu
- 进入 Ubuntu 终端执行 Linux 命令
- 尝试安装 ROS 2 Humble Desktop
- 学习使用 apt 和 curl 排查安装问题

## 遇到的问题

### 1. ROS 2 软件包无法定位
执行：
sudo apt install ros-humble-desktop

报错：
Unable to locate package ros-humble-desktop

初步排查方向：
- Ubuntu 版本与 ROS 2 发行版是否匹配
- ROS 2 软件源是否正确配置
- APT 软件包索引是否正常

### 2. curl 无法安装或下载
遇到的问题：
- Unable to locate package curl
- curl: (56) OpenSSL SSL_read: Connection reset by peer
- 访问 GitHub HTTPS 连接超时

初步排查方向：
- VMware 网络连接
- Ubuntu DNS 与路由
- GitHub HTTPS 连接
- 软件源配置

## 当前进度
- Ubuntu 虚拟机：已启动
- ROS 2 Humble：尚未确认安装成功
- GitHub 下载问题：待排查

## 下一步计划
1. 检查 Ubuntu 版本和系统架构
2. 检查 VMware NAT 网络
3. 检查 Ubuntu 软件源
4. 修复网络或软件源问题
5. 完成 ROS 2 安装并测试 turtlesim