# Pasos ros2 andino

```bash
ssh andino@ubuntu.local
cd ~/robot_ws
source install/setup.bash
ros2 launch andino_bringup andino_robot.launch.py

```



# source para ros2 en pc

```bash
source /opt/ros/jazzy/setup.bash
```



ros2 launch andino_bringup teleop_keyboard.launch.py

ros2 launch andino_slam slam_toolbox_online_async.launch.py





ros2 run nav2_map_server map_saver_cli -f mapa_robotica

ros2 launch andino_navigation bringup.launch.py map:=mapa_robotica.yaml
