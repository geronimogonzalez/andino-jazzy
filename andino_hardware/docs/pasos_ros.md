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





## Bajo consumo RPI:



Modo normal:
echo ondemand | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
o
echo schedutil | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
Modo eco:
echo powersave | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

Verificar modo:
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor



## Explorador por ssh

otras ubicaciones

sftp://andino2@ubuntu2.local/



