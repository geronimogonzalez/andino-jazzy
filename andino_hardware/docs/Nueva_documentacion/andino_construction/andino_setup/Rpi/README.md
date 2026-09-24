# Raspberry Pi 5

Este documento describe la preparación de una Raspberry Pi 5 para utilizarla con el robot Andino, incluyendo la instalación del sistema operativo, ROS 2 Jazzy, dependencias, configuración de red, dispositivos USB y cámara.

> **Estado del documento:** en revisión. La configuración de la cámara y la organización de sus espacios de trabajo todavía deben revisarse antes de considerar este procedimiento definitivo.

------

## 1. Instalación del sistema operativo

### Hardware necesario

- Raspberry Pi 5.
- MicroSD. Mínimo recomendado: 16 GB. En nuestro caso utilizamos 256 GB.
- Fuente de alimentación adecuada. La alimentación es especialmente importante en la Raspberry Pi 5.
- Conexión a Internet estable.
- Teclado y monitor, o acceso por SSH.

### Raspberry Pi Imager

En Ubuntu instalar Raspberry Pi Imager:

```bash
sudo snap install rpi-imager
```

Abrir:

```bash
rpi-imager
```

### Selección del sistema operativo

En **Choose OS** seleccionar:

```text
Ubuntu
```

Para el robot Andino utilizamos:

```text
Ubuntu Server 24.04.4 LTS
64-bit
```

La arquitectura de 64 bits es la utilizada en Raspberry Pi 5.

> Para proyectos que necesiten entorno gráfico se puede instalar posteriormente. Para una instalación orientada a servidor, ROS y acceso SSH, Ubuntu Server permite comenzar con un sistema más reducido.

### Selección de la microSD

En **Choose Storage** seleccionar la microSD correspondiente.

> **IMPORTANTE:** comprobar cuidadosamente que no se haya seleccionado otro disco antes de utilizar `Write`.

### Configuración avanzada

Antes de grabar la tarjeta, abrir la configuración avanzada mediante:

```text
CTRL + SHIFT + X
```

o mediante el icono de configuración.

Configurar:

- SSH.
- Hostname.
- Wi-Fi.
- Usuario.
- Contraseña.
- País/región correspondiente.

### Configuración utilizada en Andino

Actualmente se utiliza:

```text
Sistema operativo: Ubuntu Server 24.04.4 LTS
Arquitectura:      64-bit
Usuario:           andino
```

Los hostnames utilizados para las distintas Raspberry son:

```text
andino
andino1
andino2
```

Esto permite diferenciarlas cuando se utilizan simultáneamente en la red.

### Grabar y arrancar

Seleccionar:

```text
Write
```

Esperar a que Raspberry Pi Imager termine.

Después:

1. Insertar la microSD en la Raspberry.
2. Conectar la alimentación.
3. Esperar el primer arranque.

El primer arranque puede tardar varios minutos.

### Primer acceso por SSH

Desde otra computadora:

```bash
ssh usuario@ip_de_la_raspberry
```

Por ejemplo:

```bash
ssh andino@192.168.1.30
```

La dirección IP puede consultarse desde la Raspberry mediante:

```bash
ip a
```

También puede consultarse desde el router.

### Acceso mediante hostname

Para utilizar nombres `.local` instalar Avahi:

```bash
sudo apt install avahi-daemon
```

Activarlo:

```bash
sudo systemctl enable avahi-daemon
sudo systemctl start avahi-daemon
```

Después se puede acceder utilizando el hostname correspondiente, por ejemplo:

```bash
ssh andino@andino.local
```

Para varios robots es importante que cada Raspberry tenga un hostname diferente.

### Primera actualización

Después de instalar el sistema:

```bash
sudo apt update
sudo apt upgrade
```

### Consideración sobre la alimentación

Muchos problemas aparentemente relacionados con software pueden estar provocados por una alimentación insuficiente o inestable.

Utilizar una fuente adecuada para la Raspberry Pi 5 y para los periféricos conectados.

------

## 2. Instalación y configuración de ROS 2 Jazzy

La instalación utilizada por Andino corresponde a:

```text
Ubuntu 24.04 LTS (Noble)
ROS 2 Jazzy
```

### Actualizar el sistema

```bash
sudo apt update
sudo apt upgrade
```

### Configurar el locale

Comprobar:

```bash
locale
```

Si fuera necesario:

```bash
sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```

Comprobar nuevamente:

```bash
locale
```

### Agregar el repositorio de ROS 2

Instalar las herramientas necesarias:

```bash
sudo apt install software-properties-common curl
```

Agregar `universe`:

```bash
sudo add-apt-repository universe
```

Agregar la clave:

```bash
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
    -o /usr/share/keyrings/ros-archive-keyring.gpg
```

Agregar el repositorio:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

Actualizar:

```bash
sudo apt update
```

### Instalar ROS 2 Jazzy

```bash
sudo apt install ros-jazzy-desktop
```

Cargar ROS 2:

```bash
source /opt/ros/jazzy/setup.bash
```

Para cargarlo automáticamente en nuevas terminales:

```bash
vim ~/.bashrc
```

> **Nota:** Para editar archivos de configuración durante la instalación se puede utilizar el editor de texto que prefiera el usuario. En este documento se utiliza `vim` como ejemplo, pero también puede utilizarse `nano`.
>
> Por ejemplo:
>
> ```bash
> vim ~/.bashrc
> ```
>
> o:
>
> ```bash
> nano ~/.bashrc
> ```

Agregar:

```bash
source /opt/ros/jazzy/setup.bash
```

Aplicar el cambio:

```bash
source ~/.bashrc
```

### Herramientas de desarrollo

Instalar `colcon`:

```bash
sudo apt install python3-colcon-common-extensions
```

Instalar `rosdep`:

```bash
sudo apt install python3-rosdep
```

Inicializar:

```bash
sudo rosdep init
```

Actualizar:

```bash
rosdep update
```

> `rosdep init` normalmente se ejecuta una sola vez por instalación del sistema. En instalaciones posteriores, si ya fue inicializado, solamente es necesario ejecutar `rosdep update`.

### Workspace de Andino

La estructura utilizada es la estructura convencional de ROS 2:

```text
~/robot_ws/
└── src/
    └── Andino/
        ├── andino_base/
        ├── andino_bringup/
        ├── andino_camera/
        ├── andino_control/
        ├── andino_description/
        ├── andino_firmware/
        ├── andino_hardware/
        ├── andino_navigation/
        ├── andino_slam/
        └── ...
```

Crear el workspace:

```bash
mkdir -p ~/robot_ws/src
cd ~/robot_ws/src
```

Clonar el repositorio:

```bash
git clone <URL_DEL_REPOSITORIO>
```

Volver al directorio raíz:

```bash
cd ~/robot_ws
```

### Instalar dependencias del proyecto

Desde el workspace:

```bash
cd ~/robot_ws
```

Ejecutar:

```bash
rosdep install --from-paths src --ignore-src -r -y
```

Este comando analiza los paquetes del workspace y descarga las dependencias declaradas que no forman parte del código fuente.

### Instalar ros2_control

El proyecto utiliza `ros2_control` y los controladores correspondientes:

```bash
sudo apt install ros-jazzy-ros2-control ros-jazzy-ros2-controllers
```

### Compilar el workspace

Cargar ROS 2:

```bash
source /opt/ros/jazzy/setup.bash
```

Compilar:

```bash
cd ~/robot_ws
colcon build --symlink-install
```

Después de una compilación exitosa:

```bash
source ~/robot_ws/install/setup.bash
```

Para cargar automáticamente el workspace:

```bash
vim ~/.bashrc
```

Agregar:

```bash
source ~/robot_ws/install/setup.bash
```

Aplicar:

```bash
source ~/.bashrc
```

### Verificar la instalación

Comprobar ROS 2:

```bash
ros2 --help
```

Comprobar los paquetes de Andino:

```bash
ros2 pkg list | grep andino
```

Comprobar el entorno:

```bash
printenv | grep ROS
```

### Reconstrucción limpia

Si es necesario eliminar la compilación anterior:

```bash
cd ~/robot_ws
rm -rf build install log
colcon build --symlink-install
```

Después:

```bash
source ~/robot_ws/install/setup.bash
```

------

## 3. Configuración de dispositivos USB

Andino utiliza reglas `udev` para asignar nombres persistentes a los dispositivos USB utilizados por ROS.

Los nombres utilizados por el sistema son:

```text
/dev/ttyUSB_ARDUINO
/dev/ttyUSB_LIDAR
```

Esto permite que la configuración de ROS no dependa de si el sistema asigna temporalmente el dispositivo como `/dev/ttyUSB0`, `/dev/ttyUSB1`, etc.

### Identificar los dispositivos

Conectar el Arduino y el LiDAR y comprobar qué puertos fueron detectados:

```bash
sudo dmesg | grep ttyUSB
```

Ejemplo:

```text
[   11.058922] ch341-uart ttyUSB0: break control not supported, using simulated break
[   11.060803] usb 4-2: ch341-uart converter now attached to ttyUSB0
[   11.065288] usb 2-2: cp210x converter now attached to ttyUSB1
```

El Arduino utiliza un conversor CH340/CH341:

```text
idVendor:  1a86
idProduct: 7523
```

El LiDAR utiliza un conversor CP210x:

```text
idVendor:  10c4
idProduct: ea60
```

### Crear las reglas udev

Crear el archivo de reglas:

```bash
sudo vim /etc/udev/rules.d/99-andino-usb.rules
```

Agregar:

```text
SUBSYSTEM=="tty", ATTRS{idProduct}=="7523", ATTRS{idVendor}=="1a86", SYMLINK+="ttyUSB_ARDUINO"
SUBSYSTEM=="tty", ATTRS{idProduct}=="ea60", ATTRS{idVendor}=="10c4", SYMLINK+="ttyUSB_LIDAR"
```

Guardar el archivo.

### Recargar las reglas

Después de crear o modificar las reglas:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Si los dispositivos ya estaban conectados, puede ser necesario desconectarlos y volver a conectarlos.

### Verificar los nombres

Comprobar:

```bash
ls -l /dev/ttyUSB*
```

El resultado esperado es similar a:

```text
/dev/ttyUSB0
/dev/ttyUSB1
/dev/ttyUSB_ARDUINO -> ttyUSB0
/dev/ttyUSB_LIDAR -> ttyUSB1
```

Los números `ttyUSB0` y `ttyUSB1` pueden cambiar entre conexiones. Lo importante es que los enlaces simbólicos mantengan sus nombres:

```text
/dev/ttyUSB_ARDUINO
/dev/ttyUSB_LIDAR
```

### Uso desde ROS

Los paquetes de Andino utilizan estos nombres persistentes.

Arduino:

```text
/dev/ttyUSB_ARDUINO
```

LiDAR:

```text
/dev/ttyUSB_LIDAR
```

Por ejemplo, la configuración del LiDAR utiliza:

```text
serial_port: '/dev/ttyUSB_LIDAR'
```

### Limitación de las reglas actuales

Estas reglas identifican los dispositivos mediante VID/PID.

Esto funciona correctamente cuando hay un Arduino y un LiDAR conectados, porque sus conversores USB son diferentes.

Sin embargo, dos Arduinos idénticos con el mismo CH340/CH341 tendrán el mismo VID/PID y estas reglas no permitirán distinguirlos individualmente.

Si en el futuro se utilizan varios Arduinos idénticos simultáneamente, será necesario crear reglas basadas en atributos adicionales, como la ruta física USB.




## 4. Configuración de red y SSH

Para utilizar varios robots Andino simultáneamente se configuró una reserva DHCP para cada Raspberry en el router.

### Red utilizada

La red utilizada actualmente es:

```text
Gateway: 192.168.1.1
```

Las Raspberry están configuradas mediante reservas DHCP:

```text
andino  → 192.168.1.30
andino1 → 192.168.1.31
andino2 → 192.168.1.32
```

La PC utilizada para la administración del robot utiliza:

```text
192.168.1.102
```

(o la que el router le otorgue).



### Reserva DHCP

El procedimiento general es:

1. Entrar a la configuración del router.
2. Localizar la sección DHCP.
3. Buscar la opción de reserva de direcciones.
4. Identificar la MAC de la Raspberry.
5. Asociar la MAC con la dirección IP deseada.
6. Guardar la configuración.

La MAC puede consultarse desde la Raspberry.

> **Pendiente de revisión:** verificar el procedimiento exacto del router TP-Link utilizado y documentar correctamente la relación entre rango DHCP y reservas. No asumir que una reserva necesariamente debe encontrarse fuera del rango DHCP.

### Hostnames y acceso `.local`

Con Avahi instalado, cada Raspberry puede ser identificada mediante su hostname:

```text
andino.local
andino1.local
andino2.local
```

Por ejemplo:

```bash
ssh andino@andino.local
```

Esto permite conectarse al robot sin conocer previamente su dirección IP.

------

## 5. Configuración de la cámara

### Arquitectura utilizada

La cámara utilizada es una Raspberry Pi IMX219 conectada mediante MIPI/CSI a una Raspberry Pi 5.

La cadena que finalmente funcionó fue:

```text
IMX219
    ↓
RP1 CSI / PiSP
    ↓
libcamera 0.6
    ↓
camera_ros
    ↓
ROS 2 topics
```

No se utiliza `v4l2_camera` para esta cámara MIPI cuando `camera_ros` funciona correctamente.

> **Pendiente de revisión:** la organización de `camera_ros` en un workspace separado (`~/camera_ws`) todavía debe resolverse antes de considerar esta instalación definitiva.

### Detección automática de la cámara

Archivo:

```text
/boot/firmware/config.txt
```

La configuración que funcionó fue:

```text
camera_auto_detect=1
display_auto_detect=1
```

No fue necesario agregar:

```text
dtoverlay=imx219
```

La cámara fue detectada automáticamente.

### Comprobar el kernel

Ejecutar:

```bash
sudo dmesg | grep -Ei "imx219|camera|csi|rp1|pisp"
```

La salida debe indicar que la IMX219 fue detectada y que el dispositivo de captura fue registrado.

En nuestro caso aparecieron mensajes equivalentes a:

```text
Using sensor imx219 10-0010 for capture
```

y:

```text
Registered [rp1-cfe-csi2_ch0] ... as /dev/video0
```

Esto confirmó:

- detección de la IMX219;
- comunicación por I2C;
- funcionamiento del CSI;
- detección del RP1;
- registro del dispositivo de captura.

Los mensajes:

```text
Fixed dependency cycle(s)
```

no impidieron el funcionamiento de la cámara.

### Permisos

Comprobar los grupos del usuario:

```bash
groups
```

Si el usuario no pertenece a `video`:

```bash
sudo usermod -a -G video $USER
```

Después cerrar sesión o reiniciar.

Comprobar también:

```bash
ls -l /dev/v4l-subdev*
```

Los dispositivos deben pertenecer al grupo `video`.

------

### Configuración de libcamera

Inicialmente el sistema disponía de una versión anterior de libcamera.

Comprobar:

```bash
apt policy libcamera-tools
```

También:

```bash
dpkg -l | grep libcamera
```

La configuración que finalmente funcionó utilizó `libcamera 0.6`.

### PPA utilizado

Se agregó:

```bash
sudo add-apt-repository ppa:marco-sonic/rasppios
```

Después:

```bash
sudo apt update
```

Comprobar:

```bash
apt policy libcamera-tools
```

En nuestra instalación apareció una versión equivalente a:

```text
0.6.0+rpt20251202-1ubuntu1~marco1
```

### Actualización del sistema

Durante la actualización apareció un mensaje indicando que había un nuevo kernel instalado pero todavía no estaba ejecutándose.

En ese caso reiniciar:

```bash
sudo reboot
```

Si `dpkg` informa que una operación fue interrumpida:

```bash
sudo dpkg --configure -a
```

y después:

```bash
sudo apt update
sudo apt full-upgrade
```

### Instalar libcamera

```bash
sudo apt install libcamera-dev
```

Comprobar:

```bash
ldconfig -p | grep libcamera
```

En nuestra instalación aparecieron:

```text
libcamera.so.0.6
libcamera-base.so.0.6
```

También se verificaron los headers:

```bash
ls /usr/include/libcamera
```

y el archivo de `pkg-config`:

```bash
find /usr -name 'libcamera.pc' -o -name 'LibcameraConfig.cmake' -o -name 'libcamera-config.cmake' 2>/dev/null
```

### Probar la cámara independientemente de ROS

La prueba principal fue:

```bash
rpicam-still -o test.jpg
```

Si la cámara produce correctamente una imagen, se confirma el funcionamiento de la cadena de captura independientemente de ROS.

También puede comprobarse qué cámaras detecta:

```bash
rpicam-still --list-cameras
```

------

## 6. Integración de la cámara con ROS 2

### Problema con `v4l2_camera`

El proyecto originalmente utilizaba:

```text
v4l2_camera
```

con:

```text
v4l2_camera_node
```

Para la IMX219 conectada mediante CSI en la Raspberry Pi 5, la solución que funcionó fue utilizar:

```text
camera_ros
```

La arquitectura utilizada quedó:

```text
libcamera
    ↓
camera_ros
    ↓
ROS 2
```

### Problema de versiones de libcamera

La versión instalada del sistema fue:

```text
libcamera 0.6
```

Sin embargo, el paquete ROS `ros-jazzy-libcamera` esperaba:

```text
libcamera.so.0.7
```

Esto produjo errores del tipo:

```text
libcamera.so.0.7: cannot open shared object file
```

Por este motivo, la configuración utilizada fue compilar `camera_ros` contra la versión 0.6 instalada en el sistema.

### `camera_ros`

Se utilizó el repositorio:

```text
https://github.com/christianrauch/camera_ros
```

En la instalación original se utilizó un workspace separado:

```text
~/camera_ws
```

con:

```text
~/camera_ws/
└── src/
    └── camera_ros/
```

> **Pendiente:** revisar si esta separación sigue siendo necesaria o si `camera_ros` debe incorporarse al workspace principal de Andino.

### Eliminar el paquete incompatible

Comprobar:

```bash
dpkg -l | grep libcamera
```

Si corresponde a la instalación problemática, eliminar:

```bash
sudo apt remove ros-jazzy-libcamera
```

Si aparece:

```text
Package 'ros-jazzy-libcamera' is not installed
```

no es un problema.

### Compilar `camera_ros`

Desde el workspace utilizado:

```bash
cd ~/camera_ws
```

Compilar:

```bash
colcon build --packages-select camera_ros --symlink-install
```

Después:

```bash
source ~/camera_ws/install/setup.bash
```

Comprobar:

```bash
ros2 pkg prefix camera_ros
```

### Comprobar la biblioteca utilizada

Buscar el componente:

```bash
find ~/camera_ws/install/camera_ros -type f | grep -E 'libcamera|\.so'
```

Comprobar sus dependencias:

```bash
ldd ~/camera_ws/install/camera_ros/lib/libcamera_component.so | grep -E 'libcamera|not found'
```

El resultado correcto debe mostrar:

```text
libcamera.so.0.6
libcamera-base.so.0.6
```

y no debe aparecer:

```text
not found
```

### Ejecutar `camera_ros`

En una terminal:

```bash
source /opt/ros/jazzy/setup.bash
source ~/camera_ws/install/setup.bash
```

Ejecutar:

```bash
ros2 run camera_ros camera_node
```

La cámara debe aparecer como:

```text
0: imx219
```

En nuestra prueba también aparecieron mensajes indicando que la cámara había sido registrada en los dispositivos CFE/ISP.

### Configuración de la cámara

Para una prueba explícita:

```bash
ros2 run camera_ros camera_node \
    --ros-args \
    -p camera:=0 \
    -p width:=640 \
    -p height:=480
```

La configuración automática también funcionó y seleccionó la cámara 0.

### Integración con Andino

El launch original utilizaba `v4l2_camera`.

La integración nueva utiliza `camera_ros`, por ejemplo:

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='camera_ros',
            executable='camera_node',
            name='camera',
            output='screen',
            parameters=[{
                'camera': 0,
                'width': 640,
                'height': 480,
            }],
        )
    ])
```

Primero debe comprobarse que la cámara publica correctamente en ROS antes de intentar resolver la calibración.

### Calibración

Durante la ejecución apareció un mensaje similar a:

```text
Unable to open camera calibration file
```

con una ruta dentro de:

```text
~/.ros/camera_info/
```

Esto no significa que la cámara esté fallando.

Significa que todavía no existe el archivo YAML de calibración correspondiente.

La calibración se realizará posteriormente.

### Verificar los topics

Con `camera_node` ejecutándose:

```bash
source /opt/ros/jazzy/setup.bash
source ~/camera_ws/install/setup.bash
```

Comprobar:

```bash
ros2 topic list
```

Deberían aparecer:

```text
/camera/camera_info
/camera/image_raw
```

Comprobar la frecuencia:

```bash
ros2 topic hz /camera/image_raw
```

------

## 7. Estado de la configuración de cámara

La configuración que funcionó durante las pruebas fue:

```text
Ubuntu 24.04 Noble
ROS 2 Jazzy
Raspberry Pi 5
IMX219
camera_auto_detect=1
libcamera 0.6
libcamera-dev 0.6
libcamera-ipa 0.6
PPA marco-sonic/rasppios
camera_ros compilado desde GitHub
camera_ros enlazado contra libcamera.so.0.6
```

No utilizar:

```text
ros-jazzy-libcamera
```

si la versión disponible requiere:

```text
libcamera.so.0.7
```

No utilizar:

```text
v4l2_camera
```

para esta cámara MIPI si `camera_ros` es la solución adoptada.

------

## 8. Diagnóstico rápido de la cámara

### ¿El kernel detecta la cámara?

```bash
sudo dmesg | grep -Ei "imx219|camera|csi|rp1|pisp"
```

### ¿Está instalado libcamera?

```bash
ldconfig -p | grep libcamera
```

### ¿Qué versión ofrece el PPA?

```bash
apt policy libcamera-tools
```

### ¿Qué cámaras detecta libcamera?

```bash
rpicam-still --list-cameras
```

### ¿La cámara produce una imagen?

```bash
rpicam-still -o test.jpg
```

### ¿El usuario pertenece a `video`?

```bash
groups
```

### ¿Está disponible `camera_ros`?

```bash
ros2 pkg prefix camera_ros
```

### ¿Está enlazado correctamente?

```bash
ldd ~/camera_ws/install/camera_ros/lib/libcamera_component.so | grep -E 'libcamera|not found'
```

### ¿ROS detecta la cámara?

```bash
ros2 run camera_ros camera_node
```

### ¿ROS publica imágenes?

```bash
ros2 topic list
ros2 topic hz /camera/image_raw
```

------

## 9. Checklist para instalar otra Raspberry

### Sistema

```text
[ ] Ubuntu 24.04 64-bit
[ ] Raspberry Pi 5
[ ] ROS 2 Jazzy instalado
[ ] Usuario configurado
[ ] SSH configurado
[ ] Hostname configurado
```

### Red

```text
[ ] Reserva DHCP configurada
[ ] IP correspondiente asignada
[ ] Hostname único
[ ] Avahi funcionando
[ ] Acceso SSH comprobado
```

### USB

```text
[ ] Arduino detectado
[ ] LiDAR detectado
[ ] Reglas udev instaladas
[ ] /dev/ttyUSB_ARDUINO comprobado
[ ] /dev/ttyUSB_LIDAR comprobado
```

### Cámara

```text
[ ] IMX219 conectada
[ ] camera_auto_detect=1
[ ] Usuario pertenece a video
[ ] Kernel detecta IMX219
[ ] libcamera funcionando
[ ] rpicam-still produce imagen
[ ] camera_ros compilado
[ ] camera_ros detecta IMX219
[ ] /camera/image_raw publicado
[ ] Calibración pendiente/realizada
```

### ROS

```text
[ ] rosdep configurado
[ ] Dependencias instaladas
[ ] Workspace compilado
[ ] Andino aparece en ros2 pkg list
[ ] Workspace cargado en .bashrc
```

------

## 10. Regla de diagnóstico

Al configurar una Raspberry o la cámara, no cambiar varias capas simultáneamente.

Para la cámara seguir este orden:

```text
1. Kernel detecta IMX219
        ↓
2. rpicam funciona
        ↓
3. libcamera funciona
        ↓
4. camera_ros compila
        ↓
5. camera_ros detecta IMX219
        ↓
6. ROS publica /camera/image_raw
        ↓
7. Integrar en andino_bringup
        ↓
8. Calibrar cámara
        ↓
9. Ajustar formato y resolución
```

Si un paso falla, detenerse en esa capa y determinar primero la causa.

------

## 11. Consideraciones importantes

- La Raspberry Pi 5 requiere una alimentación adecuada.
- Las direcciones IP de los robots deben ser diferentes.
- Los hostnames de las Raspberry deben ser únicos.
- El acceso mediante `.local` depende de Avahi.
- Las reglas udev basadas únicamente en VID/PID no distinguen dispositivos idénticos.
- El workspace principal de Andino utiliza la estructura convencional `~/robot_ws/src/Andino`.
- La organización definitiva de `camera_ros` todavía debe revisarse.

------

## 12. Futuros cambios

- Revisar y definir la organización definitiva de `camera_ros` y `camera_ws`.
- Decidir si `camera_ros` debe incorporarse al workspace principal de Andino.
- Actualizar `andino_bringup` para utilizar la solución definitiva de cámara.
- Documentar las reglas udev definitivas.
- Revisar la configuración de red y DHCP.
- Realizar la calibración de la IMX219.
- Separar del procedimiento definitivo los registros históricos de troubleshooting que correspondan a `guias-instrucciones`.
- Revisar las versiones y dependencias antes de utilizar este documento como procedimiento para una Raspberry nueva.
