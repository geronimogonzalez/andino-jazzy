# andino_bringup

## Descripción

Este paquete contiene principalmente archivos de lanzamiento necesarios para ejecutar todos los drivers y nodos relacionados con el robot real. También se incluyen algunos archivos de configuración para configurar los nodos ejecutados, ubicados en la carpeta `config`.

## Archivos de lanzamiento

### Archivo principal

El archivo principal de lanzamiento es `andino_robot.launch.py`. Este archivo ejecuta todos los nodos necesarios para que el robot Andino funcione.

Incluye:

- `andino_description.launch.py`: archivo encargado de cargar el modelo 3D del robot a partir del URDF.
- `andino_control.launch.py`: archivo encargado de los nodos y drivers de `ros2_control`.
- `camera.launch.py`: archivo encargado de ejecutar los drivers de la cámara mediante el paquete `v4l2_camera`. Si no es necesario utilizar la cámara, puede deshabilitarse mediante el parámetro `include_camera`.
- `rplidar.launch.py`: archivo encargado de ejecutar los drivers del RP 2D LiDAR conectado al robot. Si no es necesario utilizarlo, puede deshabilitarse mediante el parámetro `include_rplidar`. (para una mejor compatibilidad con los diversos modelos, hemos cambiado un parametro del launch)

### Otros archivos de lanzamiento incluidos

Estos archivos de lanzamiento se incluyen para poder controlar el robot de diferentes maneras o visualizar la información:

- `rosbag_record.launch.py`: ejecuta el comando `ros2 bag record` para almacenar un archivo `.bag` con los tópicos especificados. Si no se especifican tópicos, se grabarán todos.
- `rviz.launch.py`: ejecuta la herramienta de visualización RViz utilizando por defecto la configuración `andino.rviz`, permitiendo visualizar el modelo 3D del robot y la información proveniente de los tópicos.
- `teleop_joystick.launch.py`: permite controlar el robot mediante un controlador con joystick, utilizando los paquetes `teleop_twist_joy` y `joy_linux`.
- `teleop_keyboard.launch.py`: permite controlar el robot mediante el teclado, utilizando el paquete `teleop_twist_keyboard`.