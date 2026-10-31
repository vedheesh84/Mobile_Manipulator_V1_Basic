# Project: Autonomous Mobile Manipulator (Gripper Car)

**Entity Type:** Completed Robotics System & Proof of Work  
**Scope:** `03 — Projects & Experiments/Completed/Mobile Manipulator (Gripper Car)`  
**Status:** Completed / Established Reference  
**Underlying Workspace:** [gripper_car_ws](../../../02%20—%20Domains/ROS2/Mobile_Manipulator_ws/gripper_car_ws/README.md)  
**Primary Domain:** [ROS2](../../../02%20—%20Domains/ROS2/README.md) / Robotics  

---

## 1. Overview

The Autonomous Mobile Manipulator project is an end-to-end physical robot and software system combining a 4-wheel differential drive base with a 6-DOF articulated robotic arm, a parallel gripper, and visual/spatial perception sensors (YDLiDAR X2L and Raspberry Pi Camera). 

Developed over a 6-month intensive deployment at a robotics company, this project served as the end-to-end integration proving ground for ROS2 Humble, MoveIt2 motion planning, Nav2 autonomous navigation, SLAM mapping, and hardware abstraction bridges.

---

## 2. Intent

To design, fabricate, program, and validate a complete cyber-physical mobile manipulation system capable of autonomous navigation, environmental mapping, visual QR-target identification, and physical pick-and-place manipulation.

---

## 3. System Architecture & Components

The physical and simulated implementation is maintained in the [gripper_car_ws](../../../02%20—%20Domains/ROS2/Mobile_Manipulator_ws/gripper_car_ws/README.md) workspace:

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                           APPLICATION LAYER                                  │
│  • Pick & Place State Machine  • Autonomous Navigation  • QR Target Scan    │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
┌──────────────────────────────────────▼──────────────────────────────────────┐
│                           ROS2 MIDDLEWARE                                    │
│  Topics: /cmd_vel, /odom, /scan, /joint_states, /tf, /goal_pose             │
│  Actions: /navigate_to_pose, /follow_joint_trajectory                       │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
┌──────────────────────────────────────▼──────────────────────────────────────┐
│                           HARDWARE INTEGRATION                               │
│  • Base Bridge (DC motors + encoders) • Arm Bridge (PCA9685 PWM servos)     │
│  • YDLiDAR X2L Driver                 • Camera Driver (OpenCV / pyzbar)     │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
┌──────────────────────────────────────▼──────────────────────────────────────┐
│                         PHYSICAL ROBOT / GAZEBO                             │
│  Arduino Mega 2560 ──▶ 4WD Chassis + 6-DOF Arm + Gripper + Sensors          │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Subsystems:
1. **Mobile Base:** 4-wheel differential drive platform with encoder odometry and hardware bridge (`mobile_base_description`, `mobile_base_control`, `mobile_base_gazebo`).
2. **Robotic Arm & Gripper:** 6-DOF servo-driven arm controlled via MoveIt2 motion planning and unified servo bridge (`arm_description`, `arm_control`, `arm_hardware`).
3. **Perception & Sensors:** 2D LiDAR for Nav2 SLAM localization, USB/Pi Camera with OpenCV QR scanner (`lidar_bringup`, `camera_driver`).
4. **Master Bringup & Orchestration:** System launch files managing multi-node startup, transforms, and test suites (`robot_bringup`).

---

## 4. Evidence & Operational Lessons

1. **Spatial Transform Rigidity:** Accurate physical transform trees (`base_link` $\rightarrow$ `laser_frame`, `camera_link_optical`, and arm kinematics) are essential. Minor calibration offsets cause grasping failures even when visual detection succeeds.
2. **Subsystem Control Decoupling:** Separating velocity control (diff-drive base) from position/trajectory control (arm servos) via explicit hardware bridge interfaces prevents communication lockup.
3. **Simulation vs Hardware Parity:** Validating URDF kinematics, inertial matrices, and controller plugins in Gazebo before deploying on physical microcontrollers (Arduino Mega) dramatically reduces mechanical damage risks.

---

## 5. Relationships to Ecosystem Assets

- **Technical Reference Workspace:** [gripper_car_ws](../../../02%20—%20Domains/ROS2/Mobile_Manipulator_ws/gripper_car_ws/README.md)
- **Pedagogical Counterpart:** [ros2_mobile_manipulator_kit](../../../02%20—%20Domains/ROS2/Ros2%20learning%20kits/ros2_mobile_manipulator_kit/README.md)
- **Conceptual Grounding:** [ROS2 Articles Series H (Capstone Systems Thinking)](../../../02%20—%20Domains/ROS2/ROS2%20Articles/article_h1.md)
