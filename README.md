# 🤖 Automatic TCM Delivery Robot (自动中药房配送机器人)

<p align="center">
  <img src="src/ros2-slam-auto-navigation/images/robot_thumbnail.png" alt="Robot" width="300"/>
</p>

> 基于 **ROS2 Humble** 的智能中药房配送机器人，集成 SLAM 建图与 Nav2 自主导航功能，可在中药房环境中实现自主采药与药品配送。

---

## 📋 项目概述

本项目构建了一个完整的差速驱动移动机器人系统，能够在 **Gazebo** 仿真环境中完成自主建图与导航任务。机器人搭载激光雷达和摄像头传感器，通过 **SLAM Toolbox** 实现即时定位与地图构建（SLAM），并利用 **Nav2** 导航栈进行路径规划与自主导航。

### 主要功能

- ✅ **Gazebo 仿真** — 在仿真环境中模拟中药房场景
- ✅ **SLAM 建图** — 使用 SLAM Toolbox 在线异步建图
- ✅ **自主导航** — 基于 Nav2 的路径规划与自主避障
- ✅ **ROS2 Control** — 差速驱动控制器与关节状态发布
- ✅ **Rviz2 可视化** — 实时监控机器人状态、地图和导航规划
- ✅ **Twist Mux** — 多源速度指令管理（导航/遥控手柄）

---

## 🏗️ 机器人硬件结构

| 组件 | 规格 |
|------|------|
| **底盘** | 500mm × 300mm × 200mm |
| **驱动轮** | 直径 100mm，间距 350mm，差速驱动 |
| **万向轮** | 前后各一个，直径 100mm |
| **激光雷达** | 360° 扫描，12m 探测距离，10Hz 更新率 |
| **摄像头** | RGB 640×480，水平 FOV 62.4° |

### TF 坐标变换树

![TF Tree](src/ros2-slam-auto-navigation/images/tf2_frames.png)

---

## 📂 项目结构

```
src/ros2-slam-auto-navigation/
├── CMakeLists.txt              # 编译配置
├── package.xml                 # ROS2 包描述
├── config/                     # 配置文件
│   ├── gazebo_params.yaml      # Gazebo 仿真参数
│   ├── mapper_params_online_async.yaml  # SLAM Toolbox 参数
│   ├── my_controller.yaml      # 控制器参数（差速驱动）
│   └── twist_mux.yaml          # Twist Mux 速度多路复用
├── description/                # 机器人 URDF 描述
│   ├── robot.urdf.xacro        # 主 URDF 文件
│   ├── robot_base.xacro        # 底盘与车轮定义
│   ├── lidar.xacro             # 激光雷达传感器
│   ├── camera.xacro            # RGB 摄像头传感器
│   ├── depth_camera.xacro      # 深度摄像头（可选）
│   ├── ros2_control.xacro      # ROS2 Control 硬件接口
│   ├── gazebo_control.xacro    # Gazebo 控制（备选）
│   └── inertial_macros.xacro   # 惯性参数宏
├── launch/                     # 启动文件
│   ├── launch_sim.launch.py    # 启动仿真环境
│   ├── slam_navigation.launch.py  # 启动 SLAM+导航+Rviz
│   └── rsp.launch.py           # 机器人状态发布器
├── worlds/                     # Gazebo 世界文件
│   └── simple.world            # 简单仿真场景
└── images/                     # 图片资源
```

---

## 🔧 环境依赖与安装

### 1. 安装 ROS2 Humble

参考 [ROS2 Humble 官方安装指南](https://docs.ros.org/en/humble/Installation.html)

### 2. 创建工作空间

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
```

### 3. 安装依赖包

```bash
# Xacro URDF 处理器
sudo apt install ros-humble-xacro

# Gazebo 仿真
sudo apt install ros-humble-gazebo-ros-pkgs

# ROS2 Control
sudo apt install ros-humble-ros2-control ros-humble-ros2-controllers ros-humble-gazebo-ros2-control

# SLAM Toolbox
sudo apt install ros-humble-slam-toolbox

# Nav2 导航栈与 Twist Mux
sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup ros-humble-twist-mux
```

### 4. 克隆并编译

```bash
cd ~/ros2_ws/src
git clone <repository_url>
cd ~/ros2_ws
colcon build
source install/setup.bash
```

---

## 🚀 快速使用

### 一键启动（推荐）

**终端 1 — 启动仿真环境：**

```bash
ros2 launch ros2_slam_auto_navigation launch_sim.launch.py world_file:=~/ros2_ws/src/Automatic-TCM-delivery-robot/src/ros2-slam-auto-navigation/worlds/simple.world
```

> 该命令会启动 Gazebo 仿真、加载世界场景、生成机器人，并启动差速驱动控制器和 Twist Mux。

**终端 2 — 启动 SLAM 建图与导航：**

```bash
ros2 launch ros2_slam_auto_navigation slam_navigation.launch.py slam_params_file:=~/ros2_ws/src/Automatic-TCM-delivery-robot/src/ros2-slam-auto-navigation/config/mapper_params_online_async.yaml use_sim_time:=true
```

> 该命令会启动 SLAM Toolbox（在线异步建图）、Nav2 导航栈和 Rviz2 可视化界面。

### 分步启动

如需单独启动各组件，可按照以下顺序执行：

```bash
# 1. 启动 Gazebo 仿真
ros2 launch ros2_slam_auto_navigation launch_sim.launch.py world_file:=<world_file_path>

# 2. 启动 SLAM Toolbox（在线异步模式）
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=<params_path> use_sim_time:=true

# 3. 启动 Nav2 导航栈
ros2 launch nav2_bringup navigation_launch.py use_sim_time:=True

# 4. 启动 Rviz2 可视化
ros2 run rviz2 rviz2 use_sim_time:=True -d /opt/ros/humble/share/nav2_bringup/rviz/nav2_default_view.rviz
```

---

## 🎮 使用指南

### 使用 Rviz2 进行可视化

启动后 Rviz2 会自动打开，您可以在其中：

1. **查看机器人状态** — 机器人在 Gazebo 中的实时位姿
2. **查看传感器数据** — 激光雷达扫描点云、摄像头画面
3. **查看建图进度** — SLAM Toolbox 实时构建的地图
4. **设置导航目标** — 使用「2D Nav Goal」工具下达导航目的地

### 自主导航

当地图构建完成（即使只是部分地图），即可通过 Rviz2 下发导航目标：

1. 点击 Rviz2 工具栏中的 **「2D Nav Goal」**
2. 在地图上点击目标位置并拖拽设置朝向
3. Nav2 将自动规划路径并控制机器人自主行驶至目标点

> 💡 **提示：** 如果使用手柄，可通过 Twist Mux 切换速度指令来源。当前配置中手柄指令（`cmd_vel_joy`）优先级高于导航指令（`cmd_vel`）。

---

## ⚙️ 关键配置说明

| 配置文件 | 说明 |
|---------|------|
| `my_controller.yaml` | 差速驱动控制器参数（轮距、轮径、PID） |
| `mapper_params_online_async.yaml` | SLAM Toolbox 建图参数（分辨率、闭环检测、扫描匹配） |
| `twist_mux.yaml` | 速度指令多路复用（导航/手柄优先级） |
| `gazebo_params.yaml` | Gazebo 仿真参数（发布频率） |

---

## 🐛 常见问题排查

| 问题 | 可能原因 | 解决方法 |
|------|---------|---------|
| Gazebo 无法启动 | 缺少依赖 | 检查 `gazebo_ros_pkgs` 是否正确安装 |
| TF 变换错误 | URDF 未正确发布 | 检查 `rsp.launch.py` 和 Xacro 文件 |
| 导航失败 | SLAM 未运行或参数不匹配 | 确认 SLAM Toolbox 已启动并发布地图；检查 Nav2 参数（机器人 footprint、传感器源等） |
| 地图无法保存 | 路径权限问题 | 检查 `mapper_params_online_async.yaml` 中的 `map_file_name` 路径 |
| 仿真时间不同步 | `use_sim_time` 未正确设置 | 确保所有节点参数中 `use_sim_time:=true` |

---

## 📚 参考资料

- [ROS2 Humble 官方文档](https://docs.ros.org/en/humble/index.html)
- [ROS2 tf2 教程](https://docs.ros.org/en/humble/Tutorials/Intermediate/Tf2/Introduction-To-Tf2.html)
- [Nav2 导航框架](https://github.com/ros-navigation/navigation2)
- [SLAM Toolbox](https://github.com/SteveMacenski/slam_toolbox)
- [Gazebo 仿真](http://gazebosim.org/)

---

## 🤝 贡献

欢迎贡献代码！如有 Bug 报告或功能建议，请提交 Issue 或 Pull Request。

## 📄 许可证

本项目基于 MIT 许可证开源 — 详见 `LICENCE` 文件。
