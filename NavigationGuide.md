# KUKA Navigation Guide

## 1. Overview

This section describes how to launch the **KUKA navigation system** using ROS, including:

* Robot bring-up
* Navigation stack
* Map creation and usage

---------------------------------------------------

## 2. Launch KUKA Navigation System

### 2.1 Start ROS Master

Open a terminal:

```bash
source devel/setup.bash
roscore
```

### 2.2 Bring Up the Robot

Open a **new terminal**:

```bash
source devel/setup.bash
roslaunch kmriiwa_bringup kmriiwa_bringup.launch
```
This step initializes the robot hardware and communication.
* After bringup, you need to go to the Controller Application, and then hold the buttons on KUKA SmartPad to activate the robot.

### 2.3 Launch Navigation Stack

Open another **new terminal**:

```bash
source devel/setup.bash
roslaunch kmriiwa_navigation map_navigation.launch rviz:=true map_file:=/home/yfl/CAIS_WS/src/kmriiwa_moving/scripts/260310/my_map.yaml
```

### Parameter Explanation

* `rviz:=true` → Launches RViz for visualization
* `map_file:=...` → Path to the map file

Example:

```text
/home/yfl/CAIS_WS/src/kmriiwa_moving/scripts/260310/my_map.yaml
```
This path should be replaced with your **actual map file location**

---------------------------------------------------

## 3. Map Navigation

### 3.1 Create a Map (SLAM)

To build a map using SLAM:

```bash
roslaunch kmriiwa_navigation gmapping.launch
```
* Use keyboard or teleoperation to move the robot
* The map will be built in real time

### 3.2 Save the Map

After mapping is complete **(do NOT close the gmappng.launch yet!)**:

```bash
rosrun map_server map_saver -f <map_name>
```
*(you can kill the gmapping program after saving the map)*

Example:

```bash
rosrun map_server map_saver -f my_map
```

This will generate:

* `my_map.pgm`
* `my_map.yaml`

### 3.3 Use an Existing Map

To run navigation with a known map:

```bash
roslaunch kmriiwa_navigation map_navigation.launch rviz:=true map_file:=<map_path>
```

Example:

```bash
roslaunch kmriiwa_navigation map_navigation.launch rviz:=true map_file:=/home/yfl/CAIS_WS/src/kmriiwa_moving/scripts/260310/my_map.yaml
```
---------------------------------------------------

## 4. RViz Usage Guide (2D Nav Goal)

After launching the navigation system (`kmriiwa_bringup` + `map_navigation`), an **RViz window** will open automatically.

### 4.1 Set Initial Pose (Localization)

Before sending navigation goals, you must initialize the robot’s position:

* Click the **“2D Pose Estimate”** tool (green arrow in RViz)
* Click on the map where the robot is located
* Drag to set the robot’s orientation

### 4.2 Send Navigation Goal

To command the robot to move:

* Click the **“2D Nav Goal”** tool (purple arrow in RViz)
* Click on the target location on the map
* Drag to set the desired final orientation

### 4.3 Activate Robot (T1 Mode)

If the robot is in **T1 (manual) mode**:

* You must **press and hold the button on the back of the KUKA robot**
* This action **activates the robot motion**

### 4.4 Expected Behavior

* After setting the goal, the robot should begin moving toward the target
* The path will be planned and displayed in RViz
* You should be able to see:

  * The robot position updating in real time
  * The planned path (usually a colored line)

---------------------------------------------------

## 5. Timestamp Synchronization (TF / Costmap Warning Fix)

### 5.1 Problem Description

After launching RViz:

* Unable to place **2D Nav Goal**
* Terminal shows **TF** or **costmap warnings** (yellow messages)

This is usually caused by a **timestamp mismatch** between:

* KUKA controller (Smartpad / Windows system)
* Your laptop (ROS system)

### 5.2 Solution Overview

You need to **synchronize the system clocks** between KUKA and your computer.

### 5.3 Access KUKA System (Smartpad)

1. Connect a **keyboard and mouse** to the KUKA Smartpad (top-left port)
2. Press:

Ctrl + Alt + Delete

3. Enter the Windows interface
4. Open **Command Prompt (CMD)** as **Administrator**

### 5.4 Verify Network Connection

Make sure both devices can communicate:

#### On Laptop:

```bash
ping 172.31.1.77
```

#### On KUKA (CMD):

```cmd
ping 172.31.1.100
```

* If both succeed → network is OK
* If not → check WiFi connection (must be connected to KUKA network)

### 5.5 Start Time Service

On KUKA (CMD as Administrator):

```cmd
net start w32time
```
### 5.6 Force Time Synchronization

```cmd
w32tm /resync
```
### 5.7 Fix Error 1058 (If Service Fails)

If you see:

```text
Error 1058: The service cannot be started
```

Follow these steps:

1. Press:

```text
Win + R
```

2. Type:

```text
services.msc
```

3. Find:

* **Windows Time**

4. Change:

* Startup type → **Automatic**

5. Start the service manually

6. Run again:

```cmd
w32tm /resync
```
### 5.8 Important Notice (Keyboard Layout)

* KUKA uses a **German keyboard layout**
* Some keys (e.g., `/`, `-`, `=`) may be in different positions
* It is recommended to **search key mappings online if needed**
