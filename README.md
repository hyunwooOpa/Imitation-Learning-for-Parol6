# PAROL6 Teleoperation

Leader-arm teleoperation for the PAROL6 6-DOF arm, built for
imitation learning data collection.

> [!CAUTION]
> The arm is still under improvement, and the code will be uploaded soon!
> If you want to test the implementation or collaborate, please DM me.

# System overview


## Diagram

<img width="640" height="480" alt="parol6_teleop_pipeline" src="https://github.com/user-attachments/assets/3a31e53f-733f-4b57-82d1-6ae9e572d907" />


## Hardware

| Component | Role |
|-----------|------|
| Leader arm (XL330 ×7) | Teleoperation input |
| OpenRB-150 | Dynamixel bus interface |
| PAROL6 6-DOF arm | Follower robot |
| SSG-48 gripper | End effector |

## Joint Mapping

| Leader | PAROL6 | Standby (°) | Range (°) |
|--------|--------|-------------|-----------|
| J1 | Base | 0 | ±123 |
| J2 | Shoulder | -90 | -145 → -3.4 |
| J3 | Elbow | 180 | 107.9 → 287.9 |
| J4 | Forearm | 0 | ±105 |
| J5 | Wrist pitch | 0 | ±90 |
| J6 | Wrist roll | 180 | 0 → 360 |
| J7 | Gripper | — | 3200 → 7000 steps |

## Running

### 1. Building pipeline - Named pipe

```bash
# Terminal 1 - laying a pipe
mkfifo /tmp/leader_pipe
cat /tmp/leader_pipe

# Terminal 2 - making a serial port
 python teleopration-dynamixel.py

# Terminal 3 - bridging between leader and follwer
python bridge-leader2follwer-dynamixel.py

```

### 2. Starting up Parol6 (client)
```bash
# Terminal 4
python API/headless_commander.py
```

### 3. Starting up leader arm (server)
```bash
# Terminal 5
python /leader-dynamixel/test-leader-follwer.py
```

> [!CAUTION]
> If you mix around the activation order, that might cause unwanted rapid moves of the follwer arm, resulting damage on some mechanical components on each joint.

## Safety
- NC limit switches (hardware E-stop)
- Software joint limit clamping
- Deadman pause toggle
- JOINT_STREAM 100 ms timeout → holds position

# Hardware

## Parol6 - follower arm
- opensource 6-axies robot arm
- open-loop control with STM32 based board
- Jetson Orin Nano Super 8GB
- IMX219-75 wrist camera
- IMX219-160 global camera
  
## MiniParol6 - leader arm v1

<img width="640" height="480" alt="leader arm" src="https://github.com/user-attachments/assets/3f519480-fd99-49a0-b0fc-28dce08ab7f5" />

- identical URDF is used for compatibility with Parol6
- 0.7 : 1 scale
- Servo motor: Feetech STS3215 
- Board: URT-2
- Comm protocal : TTL, UART
- Gripper : rotational

## MiniParol6 - leader arm v2

<img width="640" height="480" alt="leader arm - v2" src="https://github.com/user-attachments/assets/506ce509-c0bd-4a35-88d3-28181a749286" />

- identical URDF is used for compatibility with Parol6
- 0.5 : 1 scale
- Servo motor: Dynamixel XL330-M288 and -M071
- Board : OpenRB-150
- Comm protocal : TTL
- Gripper : linear 
  
# Software
- Dynamixel
- Parol6 commander software[https://github.com/PCrnjak/PAROL-commander-software]
- python API(headless_commander)[https://github.com/PCrnjak/PAROL6-python-API-Alvar.git] written by AlvarEhr
- lerobot library : ACT, pi-zero
- Global system GUI : [TODO]


## Attribution
1.The follower arm design uses 3D models and the controller board(including STM32F446RE and PCB) from
Source Robotics[https://source-robotics.com],
licensed under [GNU GENERAL PUBLIC LICENSE Version 3].
Modifications made for PAROL6 application by H.Song.

2.This leader arm design uses servo and board models from 
ROBOTIS OMX-AI[https://github.com/ROBOTIS-GIT/open_manipulator], 
licensed under [ Apache License Version 2.0]. 
Modifications made for PAROL6 teleoperation by H.Song.
