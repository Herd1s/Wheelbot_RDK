# Wheelbot_RDK - 轮足复合机器人开发套件

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

## 项目概述

本仓库包含为全国大学生嵌入式芯片与系统设计竞赛开发的轮足复合机器人系统的开源代码。该项目基于RDK X5平台构建，实现了具有多模态SLAM建图能力的自主巡检系统。

**项目特点：**
- 轮足复合运动系统
- 自主导航和巡检
- 多模态SLAM（即时定位与地图构建）
- 基于激光雷达的环境感知
- 基于ROS2的模块化架构

**状态：** 部分开源发布 - 持续优化开发中

## 系统架构

系统由以下几个关键组件组成：

```
├── 硬件层
│   ├── RDK X5 计算平台
│   ├── 镭神 M10/N10 激光雷达
│   ├── 轮腿执行器 (DM4340 电机)
│   ├── 轮毂电机 (6215)
│   └── 控制板 (基于H7)
│
├── 软件层 (ROS2)
│   ├── lslidar_driver - 雷达驱动和点云生成
│   ├── mycar_control - 串口通信和机器人控制
│   ├── my_slam_tool - SLAM和建图工具
│   └── robot_description - URDF机器人模型
│
└── 机械设计
    └── SolidWorks CAD文件，包含所有结构组件
```

## 硬件要求

- **计算平台：** 地平线机器人 RDK X5
- **激光雷达：** 镭神 M10/M10_P/M10_PLUS/N10 系列
- **电机：**
  - DM4340 执行电机（用于腿部关节）
  - 6215 轮毂电机（用于车轮）
- **控制板：** 基于STM32H7的控制器
- **电池：** 6S LiPo电池
- **传感器：**
  - IMU（惯性测量单元）
  - 摄像头
  - 激光雷达
- **通信：** RDK X5与控制板之间的串口（UART）接口

## 软件依赖

### 操作系统
- Ubuntu 20.04（推荐）
- ROS2 Foxy 或更高版本

### ROS2 软件包
```bash
# 核心ROS2包
rclcpp
rclpy
std_msgs
sensor_msgs
geometry_msgs

# 可视化
rviz2
robot_state_publisher
joint_state_publisher

# 点云处理
pcl_conversions
libpcl-all-dev

# 其他工具
diagnostic_updater
pluginlib
```

### 系统库
```bash
sudo apt-get install -y \
    libpcap-dev \
    python3-colcon-common-extensions \
    ros-foxy-xacro
```

## 安装步骤

### 1. 克隆仓库
```bash
cd ~
git clone https://github.com/Herd1s/Wheelbot_RDK.git
cd Wheelbot_RDK/code/RDK_X5
```

### 2. 编译工作空间
```bash
cd src
colcon build
source install/setup.bash
```

## 功能包说明

### lslidar_driver
镭神 M10/N10 系列激光雷达的ROS2驱动程序。

**功能特点：**
- 支持网口和串口数据传输
- 点云生成
- 角度裁剪和距离过滤
- 通过话题控制雷达启停
- 支持PCAP文件回放

**启动文件：**
- `lsm10_uart_launch.py` - M10 串口接口
- `lsn10_net_launch.py` - N10 网口接口
- `lslidar_double_launch.py` - 双雷达配置

### mycar_control
机器人控制的串口通信接口。

**功能特点：**
- 与控制板的双向串口通信
- 指令发布和订阅
- 键盘输入控制
- 电机控制协议实现

**节点：**
- `serial_publisher1_node` - 将键盘输入发布为控制指令
- `serial_subscribe1_node` - 订阅指令并通过串口发送

### my_slam_tool
用于自主导航的SLAM和建图工具。

**功能特点：**
- 与ROS2导航栈集成
- 地图构建和定位
- 路径规划支持

### robot_description
轮足机器人的URDF模型和可视化。

**功能特点：**
- 完整的机器人运动学模型
- 传感器配置（激光雷达、IMU、摄像头）
- RViz可视化支持
- 车轮和腿部关节定义

**启动：**
```bash
ros2 launch robot_description display_robot.launch.py
```

## 使用说明

### 启动激光雷达
```bash
# 终端1：启动雷达驱动
ros2 launch lslidar_driver lsn10_launch.py

# 终端2：控制雷达
ros2 topic pub -1 /lslidar_order std_msgs/msg/Int8 "data: 1"  # 启动
ros2 topic pub -1 /lslidar_order std_msgs/msg/Int8 "data: 0"  # 停止
```

### 机器人控制
```bash
# 终端1：启动串口订阅器（连接控制板）
ros2 run mycar_control serial_subscribe1_node

# 终端2：启动键盘控制发布器
ros2 run mycar_control serial_publisher1_node
# 使用键盘发送控制指令
```

### 机器人可视化
```bash
ros2 launch robot_description display_robot.launch.py
```

### SLAM和导航
```bash
ros2 launch my_slam_tool my_slam_tool.launch.py
```

## 目录结构

```
Wheelbot_RDK/
├── LICENSE                 # Apache 2.0 许可证
├── README.md              # 英文说明文档
├── README_cn.md           # 本文件（中文说明）
├── code/
│   └── RDK_X5/
│       └── src/
│           ├── lslidar_driver/      # 雷达驱动包
│           │   ├── include/         # 头文件
│           │   ├── src/             # 源文件
│           │   ├── launch/          # 启动文件
│           │   └── params/          # 配置文件
│           ├── lslidar_msgs/        # 雷达消息定义
│           ├── mycar_control/       # 机器人控制包
│           │   ├── include/         # 头文件
│           │   └── src/             # 源文件
│           ├── my_slam_tool/        # SLAM工具
│           │   ├── launch/          # 启动文件
│           │   └── my_slam_tool/    # Python包
│           └── robot_description/   # 机器人URDF模型
│               ├── launch/          # 启动文件
│               └── urdf/            # URDF/Xacro文件
└── 结构件/                  # 机械设计文件（SolidWorks）
    ├── *.sldprt            # 零件文件
    └── *.sldasm            # 装配文件
```

## 控制协议

机器人使用自定义串口协议进行通信：

**消息格式：** `[0xFF] [CMD] [数据...] [校验和] [0xFC]`

**指令类型：**
- 运动控制（前进、后退、旋转）
- 模式切换（轮式模式、腿式模式、混合模式）
- 速度控制
- 急停

## 开发说明

- 这是部分开源发布，持续优化进行中
- 代码针对RDK X5平台设计
- 在Ubuntu 20.04上使用ROS2 Foxy测试
- 串口通信需要适当的权限（`sudo usermod -a -G dialout $USER`）

## 贡献

欢迎贡献！请随时提交问题和拉取请求。

## 致谢

特别感谢本项目的所有贡献者和支持者！
感谢大喵！

## 许可证

本项目采用Apache License 2.0许可证 - 详见[LICENSE](LICENSE)文件。

## 联系方式

如有问题或需要支持，请在GitHub上开启issue。

## 参考资料

- [地平线机器人 RDK X5](https://developer.horizon.cc/)
- [ROS2 文档](https://docs.ros.org/en/foxy/index.html)
- [镭神激光雷达](http://www.lslidar.com/)
