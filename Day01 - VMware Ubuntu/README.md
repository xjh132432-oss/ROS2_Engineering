# Day01 - VMware Ubuntu 环境搭建

## 今日目标

在 Windows 上使用 VMware 创建 Ubuntu 虚拟机，为后续 ROS 2 和四足机器人开发准备环境。

## 今日完成

- [x] 下载 Ubuntu ISO 镜像
- [x] 配置 VMware 虚拟机
- [x] 排查虚拟机 `.vmx` 配置文件拒绝访问问题
- [x] 启动 Ubuntu 并观察启动日志

## 问题记录

### 1. VMware 配置文件拒绝访问

报错：`Cannot open configuration file ... 拒绝访问`

处理思路：检查虚拟机保存路径、目录权限和同名配置文件。

### 2. Ubuntu 启动日志

提示：`piix4_smbus ... SMBus Host Controller not enabled!`

初步判断：通常是 VMware 虚拟硬件检测产生的警告，需要结合后续启动结果判断是否存在实际问题。

## 当前进度

Ubuntu 虚拟机启动测试阶段，桌面是否成功进入尚未确认。

## 下一步

- [ ] 确认 Ubuntu 能正常进入桌面
- [ ] 检查网络、分辨率和系统更新
- [ ] 确认团队要求的 Ubuntu / ROS 2 版本
- [ ] 安装 ROS 2 并运行官方示例