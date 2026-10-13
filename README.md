# KUKA KMR iiwa + Prusa Printer: Lab Operating Manual

![ROS Noetic](https://img.shields.io/badge/ROS-Noetic-22314E?logo=ros)
![KUKA](https://img.shields.io/badge/robot-KUKA%20KMR%20iiwa-FF6600)
![Prusa](https://img.shields.io/badge/printer-Prusa-FA6831?logo=prusa3d&logoColor=white)

Step-by-step operating guides for the mobile additive-manufacturing setup: a **KUKA KMR iiwa** mobile manipulator carrying a **Prusa 3D printer**, both driven from a laptop over ROS 1.
They are written for new lab members: power-on to a moving robot and a running print, plus fixes for the problems that come up most often.

## Guides

| Guide | Covers |
|---|---|
| 📍 [**Navigation Guide**](NavigationGuide.md) | Robot bring-up, SLAM mapping with `gmapping`, saving maps, map-based navigation, RViz *2D Pose Estimate* / *2D Nav Goal*, T1 mode, and **fixing TF / costmap timestamp errors by syncing the KUKA clock** |
| 🖨️ [**Printer Usage Guide**](PrinterUsageGuide.md) | Network setup, `roscore`, starting the printer node and test client, optional motion control, slicing in PrusaSlicer, serial-port troubleshooting |

## Quick reference

**Navigation** (three terminals, each after `source devel/setup.bash`):

```bash
roscore
roslaunch kmriiwa_bringup kmriiwa_bringup.launch          # then enable the robot on the SmartPad
roslaunch kmriiwa_navigation map_navigation.launch rviz:=true map_file:=<path/to/map.yaml>
```

Build a new map instead: `roslaunch kmriiwa_navigation gmapping.launch`, drive around, then save it **before** closing gmapping with `rosrun map_server map_saver -f <map_name>`.

**Printing** (laptop on the KUKA Wi‑Fi, printer on USB): start `roscore`, then the printer node and its test client from the [print-control repo](https://github.com/greenaandblue/3dprinter_control_PRINTRUN). In the current version (`May18/final_version/`) these are:

| Guide name | File in the print-control repo |
|---|---|
| printer node | `gcode_sender_node.py` |
| printer test script | `test_gcode_sender.py` |
| motion control (optional) | `motion_based_control.py` |

## Troubleshooting at a glance

| Symptom | Likely cause | Fix |
|---|---|---|
| Can't place a *2D Nav Goal*; yellow TF / costmap warnings | KUKA controller clock and laptop clock are out of sync | On the SmartPad (Windows, admin CMD): `net start w32time` then `w32tm /resync`. See the [Navigation Guide §5](NavigationGuide.md) |
| `w32tm` fails with **Error 1058** | Windows Time service is disabled | `services.msc` → *Windows Time* → Startup type **Automatic** → Start |
| Robot doesn't move after a goal is sent | T1 (manual) mode | Hold the enabling switch on the robot / SmartPad |
| Printer script reports a connection failure | Serial port changed after re-plugging USB | `ls /dev/ttyACM*`, then update the port in `printer_config.ini` |
| Keys type the wrong symbols on the SmartPad | German keyboard layout | Look up the German layout for `/`, `-`, `=` |

## Related repositories

| Repo | Role |
|---|---|
| [3dprinter_control_PRINTRUN](https://github.com/greenaandblue/3dprinter_control_PRINTRUN) | Printer controller used by these guides (ROS + Printrun, motion-based auto pause) |
| [ROS_octoprint](https://github.com/greenaandblue/ROS_octoprint) | Earlier OctoPrint-based version |
| [Setup-ROS-on-macOS-Apple-Silicon](https://github.com/greenaandblue/Setup-ROS-on-macOS-Apple-Silicon) | Running the KMR iiwa ROS stack in Docker on a Mac |

## Author

**Huihang Liu** · Computer Engineering, Penn State · [@greenaandblue](https://github.com/greenaandblue)
