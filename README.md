# Wheelbot_RDK - Wheel-Legged Robot Development Kit

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

## Overview

This repository contains the open-source code for a wheel-legged hybrid robot system developed for the National College Student Embedded Chip and System Design Competition. The project is built on the RDK X5 platform and implements an autonomous inspection system with multi-modal SLAM mapping capabilities.

**Project Features:**
- Wheel-legged hybrid locomotion system
- Autonomous navigation and inspection
- Multi-modal SLAM (Simultaneous Localization and Mapping)
- LiDAR-based environment perception
- ROS2-based modular architecture

**Status:** Partial release - ongoing optimization and development

## System Architecture

The system consists of several key components:

```
├── Hardware Layer
│   ├── RDK X5 Computing Platform
│   ├── Leishen M10/N10 LiDAR
│   ├── Wheel-Leg Actuators (DM4340 Motors)
│   ├── Hub Motors (6215)
│   └── Control Board (H7-based)
│
├── Software Layer (ROS2)
│   ├── lslidar_driver - LiDAR driver and point cloud generation
│   ├── mycar_control - Serial communication and robot control
│   ├── my_slam_tool - SLAM and mapping tools
│   └── robot_description - URDF robot model
│
└── Mechanical Design
    └── SolidWorks CAD files for all structural components
```

## Hardware Requirements

- **Computing Platform:** Horizon Robotics RDK X5
- **LiDAR:** Leishen M10/M10_P/M10_PLUS/N10 series
- **Motors:**
  - DM4340 actuator motors for leg joints
  - 6215 hub motors for wheels
- **Control Board:** STM32H7-based controller
- **Battery:** 6S LiPo battery
- **Sensors:**
  - IMU (Inertial Measurement Unit)
  - Camera
  - LiDAR
- **Communication:** Serial (UART) interface between RDK X5 and control board

## Software Dependencies

### Operating System
- Ubuntu 20.04 (recommended)
- ROS2 Foxy or later

### ROS2 Packages
```bash
# Core ROS2 packages
rclcpp
rclpy
std_msgs
sensor_msgs
geometry_msgs

# Visualization
rviz2
robot_state_publisher
joint_state_publisher

# Point Cloud Processing
pcl_conversions
libpcl-all-dev

# Additional utilities
diagnostic_updater
pluginlib
```

### System Libraries
```bash
sudo apt-get install -y \
    libpcap-dev \
    python3-colcon-common-extensions \
    ros-foxy-xacro
```

## Installation

### 1. Clone the Repository
```bash
cd ~
git clone https://github.com/Herd1s/Wheelbot_RDK.git
cd Wheelbot_RDK/code/RDK_X5
```

### 2. Build the Workspace
```bash
cd src
colcon build
source install/setup.bash
```

## Package Descriptions

### lslidar_driver
ROS2 driver for Leishen M10/N10 series LiDAR sensors.

**Features:**
- Network and UART data transmission support
- Point cloud generation
- Angular cropping and distance filtering
- LiDAR start/stop control via topics
- PCAP file playback support

**Launch Files:**
- `lsm10_uart_launch.py` - M10 UART interface
- `lsn10_net_launch.py` - N10 network interface
- `lslidar_double_launch.py` - Dual LiDAR configuration

### mycar_control
Serial communication interface for robot control.

**Features:**
- Bidirectional serial communication with control board
- Command publishing and subscription
- Keyboard input control
- Protocol implementation for motor control

**Nodes:**
- `serial_publisher1_node` - Publishes keyboard input as control commands
- `serial_subscribe1_node` - Subscribes to commands and sends via serial

### my_slam_tool
SLAM and mapping utilities for autonomous navigation.

**Features:**
- Integration with ROS2 navigation stack
- Map building and localization
- Path planning support

### robot_description
URDF model and visualization for the wheel-legged robot.

**Features:**
- Complete robot kinematic model
- Sensor configurations (LiDAR, IMU, camera)
- RViz visualization support
- Wheel and leg joint definitions

**Launch:**
```bash
ros2 launch robot_description display_robot.launch.py
```

## Usage

### Starting the LiDAR
```bash
# Terminal 1: Launch LiDAR driver
ros2 launch lslidar_driver lsn10_launch.py

# Terminal 2: Control LiDAR
ros2 topic pub -1 /lslidar_order std_msgs/msg/Int8 "data: 1"  # Start
ros2 topic pub -1 /lslidar_order std_msgs/msg/Int8 "data: 0"  # Stop
```

### Robot Control
```bash
# Terminal 1: Start serial subscriber (connects to control board)
ros2 run mycar_control serial_subscribe1_node

# Terminal 2: Start keyboard control publisher
ros2 run mycar_control serial_publisher1_node
# Use keyboard to send control commands
```

### Robot Visualization
```bash
ros2 launch robot_description display_robot.launch.py
```

### SLAM and Navigation
```bash
ros2 launch my_slam_tool my_slam_tool.launch.py
```

## Directory Structure

```
Wheelbot_RDK/
├── LICENSE                 # Apache 2.0 License
├── README.md              # This file (English)
├── README_cn.md           # Chinese README
├── code/
│   └── RDK_X5/
│       └── src/
│           ├── lslidar_driver/      # LiDAR driver package
│           │   ├── include/         # Header files
│           │   ├── src/             # Source files
│           │   ├── launch/          # Launch files
│           │   └── params/          # Configuration files
│           ├── lslidar_msgs/        # LiDAR message definitions
│           ├── mycar_control/       # Robot control package
│           │   ├── include/         # Header files
│           │   └── src/             # Source files
│           ├── my_slam_tool/        # SLAM utilities
│           │   ├── launch/          # Launch files
│           │   └── my_slam_tool/    # Python package
│           └── robot_description/   # Robot URDF model
│               ├── launch/          # Launch files
│               └── urdf/            # URDF/Xacro files
└── 结构件/                  # Mechanical design files (SolidWorks)
    ├── *.sldprt            # Part files
    └── *.sldasm            # Assembly files
```

## Control Protocol

The robot uses a custom serial protocol for communication:

**Message Format:** `[0xFF] [CMD] [DATA...] [CHECKSUM] [0xFC]`

**Commands:**
- Motion control (forward, backward, rotation)
- Mode switching (wheel mode, leg mode, hybrid mode)
- Speed control
- Emergency stop

## Development Notes

- This is a partial release; ongoing optimization is in progress
- The code is designed for the RDK X5 platform
- Tested with ROS2 Foxy on Ubuntu 20.04
- Serial communication requires appropriate permissions (`sudo usermod -a -G dialout $USER`)

## Contributing

Contributions are welcome! Please feel free to submit issues and pull requests.

## Acknowledgments

Special thanks to all contributors and supporters of this project!
感谢大喵！

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Contact

For questions or support, please open an issue on GitHub.

## References

- [Horizon Robotics RDK X5](https://developer.horizon.cc/)
- [ROS2 Documentation](https://docs.ros.org/en/foxy/index.html)
- [Leishen LiDAR](http://www.lslidar.com/)
