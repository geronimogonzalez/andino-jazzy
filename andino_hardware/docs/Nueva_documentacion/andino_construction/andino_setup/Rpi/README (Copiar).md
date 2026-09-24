# Raspberry 5

En este documento se tratara toda la instalacion de la RPI 5, desde el OS, el ROS y los diferentes paquetes, softwares y librerias para el correcto funcionamineto del robot

## Instalacion OS

Instalar Ubuntu en Raspberry Pi

### Qué necesitás

#### Hardware

- Raspberry Pi
- MicroSD (mínimo 16 GB recomendado). En nuestro caso 256 GB.
- Fuente buena (MUY importante). Usamos al misma del PCB que creamos.
- Internet (importante la estabilidad de red para que no se corte el SSH. Nosotros usamos un router intermedio).
- Teclado/monitor (o SSH).

------

### 1) Instalar Raspberry Pi Imager

En Ubuntu:

```bash
 sudo snap install rpi-imager
```

Luego abrir:

```bash
 rpi-imager
```

------

### 2) Elegir sistema operativo

En:

```bash
 Choose OS
```

Elegir:

```bash
 Ubuntu
```

### Elegir versión correcta

#### Proyectos / liviano / servidor

```bash
 Ubuntu Server
```

Recomendación:

```bash
 Ubuntu Server 64-bit
```

y luego instalar entorno gráfico solo si realmente hace falta.

### Arquitectura correcta

#### Raspberry Pi 4 / 5

En nuestro caso usamos la **Pi 5**

Usar:

```bash
 64-bit
```

#### Raspberry Pi 3 vieja

A veces conviene:

```bash
 32-bit
```

### 3) Elegir la microSD

En:

```bash
 Choose Storage
```

⚠️ Revisar MUY bien no seleccionar otro disco.

### 4) Configuración avanzada (MUY útil)

Antes de grabar:

```bash
CTRL + SHIFT + X
```

o usar el engranaje ⚙️

### Configurar antes de instalar

#### Activar SSH

```bash
Enable SSH
```

#### Colocar Hostname

Por defecto **rpi-image** coloca **ubuntu**. Nosotros utilizamos ese. En caso de tener varias **RPI** numeramos cada una, para no tener problemas con el SSH y redes.

#### Configurar Wi-Fi

- SSID
- contraseña
- país

#### Crear usuario y contraseña

### Robot Andino

#### Sistema Operativo

Ubuntu server 24.04.4 LTS (64 bits)

#### Usuario

andino

(Si va a tener varios, numerarlos para identificar)

#### Contraseña

andino

### 5) Grabar la SD

```bash
Write
```

Esperar a que termine.

### 6) Arrancar la Raspberry

Insertar:

- microSD
- alimentación

y esperar.

⚠️ El primer arranque puede tardar varios minutos.

### Conectarse por SSH

Desde otra PC:

```bash
ssh usuario@ip_de_la_raspberry
```

En nuestro caso:

```bash
ssh andino@192.168.1.30
```

También hay otra manera que se detalla mas abajo.

### Cómo encontrar la IP

En la Raspberry:

```bash
ip a
```

o desde el router.

También se puede probar:

```bash
ping raspberrypi.local
```

### Después de instalar

Actualizar sistema:

```bash
sudo apt update
sudo apt upgrade
```

### MUY IMPORTANTE

Muchísimos problemas en Raspberry vienen de:

```bash
fuentes de alimentación malas o insuficientes
```

Usar una buena fuente evita problemas raros.

### Recomendación final

Para estudio/electrónica/proyectos:

```bash
Ubuntu Server 64-bit
SSH activado
sin entorno gráfico al principio
```

Ventajas:

- menos consumo
- más rápido
- menos problemas
- más estable

### Para ssh:

#### Para poder conectarse sin ip instalar:

```bash
sudo apt install avahi-daemon
```

#### Luego ejecutar:

```bash
sudo systemctl enable avahi-daemon
sudo systemctl start avahi-daemon
```

#### Finalmente conectar:

```bash
ssh andino@ubuntu.local
```

En este caso **ubuntu** es el Hostname de la RPI, por eso es importante diferenciar si hay varias.

------

## Instalacion de ROS y sus dependencias

### Instalación de ROS 2 Jazzy

Estas instrucciones describen la instalación del entorno ROS 2 Jazzy utilizado por Andino sobre Ubuntu 24.04 LTS (Noble).

### 1. Actualizar el sistema

```bash
sudo apt update
sudo apt upgrade
```

### 2. Configurar el locale

Verificar que el sistema utilice UTF-8:

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

Verificar:

```bash
locale
```

### 3. Agregar el repositorio de ROS 2

Instalar las herramientas necesarias:

```bash
sudo apt install software-properties-common curl
```

Agregar el repositorio oficial de ROS 2:

```bash
sudo add-apt-repository universe
```

Agregar la clave del repositorio:

```bash
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  -o /usr/share/keyrings/ros-archive-keyring.gpg
```

Agregar el repositorio:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

Actualizar los repositorios:

```bash
sudo apt update
```

### 4. Instalar ROS 2 Jazzy

Instalar la versión Desktop:

```bash
sudo apt install ros-jazzy-desktop
```

Comprobar que ROS 2 esté disponible:

```bash
source /opt/ros/jazzy/setup.bash
```

Para cargar ROS 2 automáticamente en cada terminal:

```bash
vim ~/.bashrc
```

> **Nota:** para editar archivos de configuración durante la instalación se puede utilizar el editor de texto que prefiera el usuario. Por ejemplo:
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

Agregar al final:

```bash
source /opt/ros/jazzy/setup.bash
```

Aplicar el cambio sin cerrar la terminal:

```bash
source ~/.bashrc
```

### 5. Instalar herramientas de desarrollo

Instalar `colcon` y sus extensiones:

```bash
sudo apt install python3-colcon-common-extensions
```

### 6. Instalar rosdep

```bash
sudo apt install python3-rosdep
```

Inicializar `rosdep`:

```bash
sudo rosdep init
```

Actualizar la base de datos:

```bash
rosdep update
```

> `rosdep init` normalmente se ejecuta una sola vez por instalación del sistema. En las Raspberry posteriores, si ya estaba inicializado, solamente fue necesario ejecutar `rosdep update`.

### 7. Crear el workspace

Crear el directorio de trabajo y la carpeta `src`:

```bash
mkdir -p ~/robot_ws/src
cd ~/robot_ws/src
```

Clonar el repositorio de Andino dentro de `src`:

```bash
git clone <URL_DEL_REPOSITORIO>
```

La estructura resultante será:

```bash
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

Volver al directorio raíz del workspace:

```bash
cd ~/robot_ws
```

### 8. Instalar las dependencias del proyecto

Desde el workspace:

```bash
cd ~/robot_ws
```

Ejecutar:

```bash
rosdep install --from-paths Andino --ignore-src -r -y
```

Este comando analiza los paquetes del repositorio y descarga mediante `apt` las dependencias declaradas que no forman parte del propio código fuente.

### 9. Instalar ROS 2 Control

El proyecto utiliza `ros2_control` y los controladores correspondientes:

```bash
sudo apt install ros-jazzy-ros2-control ros-jazzy-ros2-controllers
```

### 10. Compilar el workspace

Antes de compilar, cargar el entorno de ROS 2:

```bash
source /opt/ros/jazzy/setup.bash
```

Desde el workspace:

```bash
cd ~/robot_ws
```

Compilar:

```bash
colcon build --symlink-install
```

Después de una compilación exitosa, cargar también el workspace de Andino:

```bash
source ~/robot_ws/install/setup.bash
```

Para hacerlo automáticamente en nuevas terminales, agregar al `~/.bashrc`:

```bash
vim ~/.bashrc
```

y agregar:

```bash
source ~/robot_ws/install/setup.bash
```

Aplicar:

```bash
source ~/.bashrc
```

### 11. Verificar la instalación

Comprobar que ROS 2 esté disponible:

```bash
ros2 --help
```

Comprobar que los paquetes de Andino sean reconocidos:

```bash
ros2 pkg list | grep andino
```

También se puede comprobar que el entorno de ROS 2 esté correctamente cargado:

```bash
printenv | grep ROS
```

### 12. Reconstrucción del workspace

Si es necesario realizar una compilación limpia:

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

### Resumen

El flujo utilizado para preparar una Raspberry Pi nueva es:

```bash
sudo apt update
sudo apt upgrade

sudo apt install ros-jazzy-desktop
sudo apt install python3-colcon-common-extensions
sudo apt install python3-rosdep

sudo rosdep init
rosdep update

cd ~/robot_ws
rosdep install --from-paths Andino --ignore-src -r -y

sudo apt install ros-jazzy-ros2-control ros-jazzy-ros2-controllers

colcon build --symlink-install
```

Finalmente:

```bash
source /opt/ros/jazzy/setup.bash
source ~/robot_ws/install/setup.bash
```

------

## Configulacion de reglas de los puertos

### Puertos USB

```bash
sudo dmesg | grep ttyUSB
[sudo] password for andino:
[   11.058922] ch341-uart ttyUSB0: break control not supported, using simulated break
[   11.060803] usb 4-2: ch341-uart converter now attached to ttyUSB0
[   11.065288] usb 2-2: cp210x converter now attached to ttyUSB1
```

### Arduino

```bash
ATTRS{idProduct}=="7523"
ATTRS{idVendor}=="1a86"
```

### LiDAR

```bash
ATTRS{idProduct}=="ea60"
ATTRS{idVendor}=="10c4"
```

------

## Configuracion de redes y SSH

Para las redes, lo que hicimos (ya que la idea era manejar varios andinos en simultaneo) fue a cada uno reservarle una ip con la **reserva por DHCP** del router.

En nuestro caso utilizamos un Tp-link. Entramos por el navegador a su configuracion, colocando la ip del gateway, que puede ser:

```bash
192.168.0.1
192.168.1.1
```

Una vez dentro nos pide usuario y clave, que suele ser siempre **admin** en ambos.

Dentro vamos a DHCP > Addres Reservation > Add New...

Ahora lo que debemos hacer es entrar en la rpi (ya sea por SSH o teclado-monitor) y colocar el siguiente comando en la terminal:

```bash
ip link show wlan0
```

Con esto veremos la MAC de la rpi, y podremos colocarla en la página del router.

Asignamos la ip correspondiente que queremos (es importante que este por fuera del rango DHCP, para que no se "pise" con otras IPs que asigne el router). Nosotros lo hicimos en 192.168.1.XXX, donde XXX es debe ser < 100, quedandonos definidas la 30, 31 y 32.

------

## Configuracion de software para compatibilidad con la camara

### Bitácora — Raspberry Pi 5 + IMX219 + Ubuntu 24.04 + ROS 2 Jazzy

### Objetivo

Configurar una cámara Raspberry Pi IMX219 conectada por MIPI/CSI a una Raspberry Pi 5 con:

- Ubuntu 24.04 Noble 64-bit
- ROS 2 Jazzy
- Raspberry Pi 5
- Cámara IMX219
- libcamera
- `camera_ros`

La configuración final utiliza:

```bash
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

No se utiliza `v4l2_camera` para esta cámara.

------

### 1. Comprobar la configuración de la cámara

Archivo:

```bash
/boot/firmware/config.txt
```

La configuración que terminó funcionando tenía:

```bash
camera_auto_detect=1
display_auto_detect=1
```

No fue necesario agregar manualmente:

```bash
dtoverlay=imx219
```

La cámara fue detectada automáticamente.

La sección relevante quedó:

```bash
# Autoload overlays for any recognized cameras or displays
camera_auto_detect=1
display_auto_detect=1
```

IMPORTANTE:

No modificar `camera_auto_detect` a `0` ni agregar manualmente `dtoverlay=imx219,cam0`
si la detección automática ya funciona.

------

### 2. Comprobar que el kernel detecta la cámara

Ejecutar:

```bash
sudo dmesg | grep -Ei "imx219|camera|csi|rp1|pisp"
```

La salida correcta debe contener algo equivalente a:

```bash
found subdevice ... imx219@10
Using sensor imx219 10-0010 for capture
```

y:

```bash
Registered [rp1-cfe-csi2_ch0] ... as /dev/video0
```

En nuestro caso apareció:

```bash
rp1-cfe 1f00110000.csi: found subdevice /axi/pcie@120000/rp1/i2c@88000/imx219@10

rp1-cfe 1f00110000.csi: Using sensor imx219 10-0010 for capture

rp1-cfe 1f00110000.csi: Registered [rp1-cfe-csi2_ch0] node id 0 successfully as /dev/video0
```

Esto confirmó que:

- el RP1 funciona
- el CSI funciona
- la IMX219 responde por I2C
- el kernel reconoce el sensor
- el dispositivo de captura fue registrado

Los mensajes:

```bash
Fixed dependency cycle(s)
```

no impidieron el funcionamiento y no fueron el problema.

------

### 3. Comprobar permisos del usuario

La cámara utiliza dispositivos pertenecientes al grupo `video`.

Comprobar:

```bash
groups
```

Si el usuario no pertenece a `video`:

```bash
sudo usermod -a -G video $USER
```

Después cerrar sesión/reiniciar para que el cambio tenga efecto.

También se puede comprobar:

```bash
ls -l /dev/v4l-subdev*
```

En nuestro caso:

```bash
crw-rw---- 1 root video ...
```

por lo que los dispositivos estaban correctamente asociados al grupo `video`.

------

### 4. Problema inicial con libcamera

Inicialmente Ubuntu tenía:

```bash
libcamera 0.2.0
```

Comprobación:

```bash
apt policy libcamera-tools
```

Resultado inicial:

```bash
Installed: 0.2.0-3fakesync1build6
```

y:

```bash
dpkg -l | grep libcamera
```

mostraba:

```bash
libcamera0.2
libcamera-tools
libcamera-ipa
gstreamer1.0-libcamera
```

Esto no era suficiente para la configuración que necesitábamos.

La guía indicaba utilizar el PPA:

```bash
ppa:marco-sonic/rasppios
```

------

### 5. Agregar el PPA de Marco Sonic

Ejecutar:

```bash
sudo add-apt-repository ppa:marco-sonic/rasppios
```

Aceptar con ENTER.

Después:

```bash
sudo apt update
```

Comprobar:

```bash
apt policy libcamera-tools
```

Debe aparecer una versión del PPA similar a:

```bash
0.6.0+rpt20251202-1ubuntu1~marco1
```

También:

```bash
apt search libcamera | grep 0.
```

debe mostrar:

```bash
libcamera0.6
libcamera-tools
libcamera-dev
libcamera-ipa
libcamera-v4l2
python3-libcamera
rpicam-apps
rpicam-apps-lite
```

entre otros.

------

### 6. Actualizar el sistema

En nuestro caso hubo una actualización grande:

```bash
sudo apt update
sudo apt full-upgrade
```

Durante la actualización apareció:

```bash
Pending kernel upgrade!

Running kernel version:
  6.8.0-1057-raspi

The currently running kernel version is not the expected kernel version
6.8.0-1060-raspi.
```

Esto era normal.

La actualización había instalado un kernel nuevo, pero el sistema todavía estaba ejecutando el anterior.

Se debía reiniciar:

```bash
sudo reboot
```

IMPORTANTE:

No ejecutar otra actualización encima si `dpkg` informa:

```bash
dpkg was interrupted
```

En ese caso primero:

```bash
sudo dpkg --configure -a
```

y después continuar con:

```bash
sudo apt update
sudo apt full-upgrade
```

------

### 7. Instalar libcamera 0.6

Después de agregar el PPA:

```bash
sudo apt install libcamera-dev
```

Esto instaló automáticamente:

```bash
libcamera-ipa
libcamera0.6
libcamera-dev
```

En nuestro caso:

```bash
libcamera0.6
0.6.0+rpt20251202-1ubuntu1~marco1
```

Comprobar:

```bash
ldconfig -p | grep libcamera
```

Resultado correcto:

```bash
libcamera.so.0.6 => /lib/aarch64-linux-gnu/libcamera.so.0.6
libcamera.so => /lib/aarch64-linux-gnu/libcamera.so
libcamera-base.so.0.6 => /lib/aarch64-linux-gnu/libcamera-base.so.0.6
libcamera-base.so => /lib/aarch64-linux-gnu/libcamera-base.so
```

------

### 8. Comprobar headers de libcamera

Ejecutar:

```bash
ls /usr/include/libcamera
```

Debe existir el directorio.

En nuestro caso:

```bash
/usr/include/libcamera/libcamera/
```

contenía archivos como:

```bash
camera.h
camera_manager.h
controls.h
formats.h
framebuffer.h
libcamera.h
stream.h
version.h
```

------

### 9. Comprobar pkg-config

Ejecutar:

```bash
find /usr -name 'libcamera.pc' -o -name 'LibcameraConfig.cmake' -o -name 'libcamera-config.cmake' 2>/dev/null
```

Encontramos:

```bash
/usr/lib/aarch64-linux-gnu/pkgconfig/libcamera.pc
```

Aunque inicialmente:

```bash
pkg-config --modversion libcamera
```

no encontraba el paquete.

Esto no terminó siendo un problema porque CMake encontró directamente la biblioteca instalada.

------

### 10. Comprobar las aplicaciones de cámara

La guía menciona `rpicam` como sucesor de los antiguos comandos `libcamera-*`.

Después de instalar los paquetes adecuados, la prueba importante fue:

```bash
rpicam-still -o test.jpg
```

Esto abrió la cámara y mostró correctamente la imagen en el monitor de la Raspberry Pi.

RESULTADO:

```bash
IMX219 funcionando correctamente con libcamera/rpicam.
```

Esto fue una prueba fundamental.

Si:

```bash
rpicam-still -o test.jpg
```

muestra imagen, entonces:

- el cable MIPI está funcionando
- el sensor está funcionando
- el kernel detecta la cámara
- libcamera puede acceder a ella
- PiSP puede procesarla

------

### 11. `cam` y `qcam`

Inicialmente se intentó:

```bash
cam -l
```

y posteriormente apareció:

```bash
Command 'cam' not found
```

aunque el paquete correspondiente era `libcamera-tools`.

También se probó:

```bash
qcam
```

pero apareció:

```bash
qt.qpa.xcb: could not connect to display
```

Esto ocurría porque `qcam` intentaba utilizar el display gráfico de Qt y no tenía acceso al display desde esa sesión.

No fue necesario resolver esto.

La prueba:

```bash
rpicam-still -o test.jpg
```

fue suficiente y funcionó.

------

### 12. Problema con ROS 2

El paquete originalmente utilizado por el proyecto era:

```bash
v4l2_camera
```

El launch original utilizaba:

```bash
package='v4l2_camera'
executable='v4l2_camera_node'
```

Pero para una cámara MIPI de Raspberry Pi 5 la ruta correcta es:

```bash
libcamera → camera_ros
```

Por lo tanto, se decidió utilizar:

```bash
camera_ros
```

------

### 13. Comprobar paquetes ROS relacionados

Ejecutar:

```bash
ros2 pkg list | grep camera
```

Inicialmente apareció:

```bash
camera_calibration_parsers
camera_info_manager
v4l2_camera
```

Los otros paquetes de la guía no estaban instalados inicialmente.

------

### 14. Instalar `camera_ros`

La guía indicaba:

```bash
sudo apt install ros-jazzy-camera-ros
```

El paquete disponible era:

```bash
ros-jazzy-camera-ros
```

Sin embargo, había un problema importante con `ros-jazzy-libcamera`.

Inicialmente:

```bash
ros-jazzy-libcamera
```

estaba instalado y requería:

```bash
libcamera.so.0.7
```

mientras el sistema tenía:

```bash
libcamera.so.0.6
```

Esto produjo:

```bash
Could not load library dlopen error:
libcamera.so.0.7: cannot open shared object file
```

Por lo tanto, se decidió NO utilizar la versión ROS de libcamera que esperaba 0.7.

------

### 15. Solución: utilizar `camera_ros` compilado contra libcamera 0.6

Se utilizó el repositorio:

```bash
https://github.com/christianrauch/camera_ros
```

Workspace:

```bash
~/camera_ws
```

El repositorio estaba limpio y actualizado:

```bash
cd ~/camera_ws/src/camera_ros

git remote -v
git status
git branch --show-current
git log -1 --oneline
```

Resultado:

```bash
origin https://github.com/christianrauch/camera_ros.git

On branch main
Your branch is up to date with 'origin/main'.
```

------

### 16. Eliminar el paquete ROS incompatible

Se intentó:

```bash
sudo apt remove ros-jazzy-libcamera
```

Si aparece:

```bash
Package 'ros-jazzy-libcamera' is not installed
```

no hay problema.

Comprobar:

```bash
dpkg -l | grep libcamera
```

La situación deseada para este procedimiento es que no aparezca:

```bash
ros-jazzy-libcamera
```

------

### 17. Instalar las dependencias de desarrollo

Instalar:

```bash
sudo apt install libcamera-dev
```

Esto proporciona:

```bash
libcamera0.6
libcamera-ipa
libcamera-dev
```

------

### 18. Compilar `camera_ros`

Entrar al workspace:

```bash
cd ~/camera_ws
```

Comprobar:

```bash
ls
```

Debe existir:

```bash
build
install
log
src
```

Y:

```bash
ls src
```

debe mostrar:

```bash
camera_ros
```

Compilar:

```bash
colcon build --packages-select camera_ros --symlink-install
```

Resultado correcto:

```bash
Finished <<< camera_ros
```

y:

```bash
Summary: 1 package finished
```

Después:

```bash
source ~/camera_ws/install/setup.bash
```

Comprobar:

```bash
ros2 pkg prefix camera_ros
```

Resultado:

```bash
/home/andino/camera_ws/install/camera_ros
```

------

### 19. Comprobar que el componente usa libcamera 0.6

El ejecutable principal no mostraba directamente la dependencia mediante `ldd`, porque la biblioteca está en el componente.

Encontrar:

```bash
find ~/camera_ws/install/camera_ros -type f | grep -E 'libcamera|\.so'
```

Resultado:

```bash
~/camera_ws/install/camera_ros/lib/libcamera_component.so
```

Comprobar:

```bash
ldd ~/camera_ws/install/camera_ros/lib/libcamera_component.so | grep -E 'libcamera|not found'
```

Resultado correcto:

```bash
libcamera.so.0.6 => /lib/aarch64-linux-gnu/libcamera.so.0.6
libcamera-base.so.0.6 => /lib/aarch64-linux-gnu/libcamera-base.so.0.6
```

También aparecieron correctamente:

```bash
libcamera_info_manager.so
libcamera_calibration_parsers.so
```

y NO apareció:

```bash
not found
```

Esto confirma que `camera_ros` está enlazado correctamente contra libcamera 0.6.

------

### 20. Lanzar `camera_ros`

En cada terminal:

```bash
source /opt/ros/jazzy/setup.bash
source ~/camera_ws/install/setup.bash
```

Luego:

```bash
ros2 run camera_ros camera_node
```

Resultado correcto:

```bash
libcamera v0.6.0+rpt20251202
```

Luego:

```bash
cameras:
   0: imx219 (...)
```

Esto confirma que ROS 2 ve la cámara.

También apareció:

```bash
Adding camera '...imx219@10'
```

y:

```bash
Registered camera ... to CFE device /dev/media0 and ISP device /dev/media1
```

------

### 21. Configuración automática

Sin parámetros, `camera_ros` avisó:

```bash
no camera selected, using default
```

y seleccionó:

```bash
imx219
```

También seleccionó automáticamente:

```bash
XRGB8888
```

y:

```bash
800x600
```

Estos warnings no son errores.

Para evitar los warnings se pueden pasar parámetros explícitos.

------

### 22. Configuración que funciona

Para una primera prueba:

```bash
ros2 run camera_ros camera_node \
    --ros-args \
    -p camera:=0 \
    -p width:=640 \
    -p height:=480
```

Esto utiliza:

```bash
cámara 0
640x480
```

------

### 23. Integración en el launch de Andino

El launch original utilizaba:

```bash
v4l2_camera
```

y tenía:

```bash
'image_size': [640, 480]
'camera_frame_id': 'camera_link'
'camera_info_url': ...
```

La versión nueva utiliza `camera_ros`.

Ejemplo mínimo funcional:

```bash
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

IMPORTANTE:

Por ahora no se debe intentar resolver la calibración dentro de este paso.

Primero comprobar que la imagen llega a ROS.

------

### 24. Resultado final

La ejecución correcta produjo:

```bash
libcamera v0.6.0+rpt20251202

cameras:
   0: imx219

configuring streams:
   800x600-XRGB8888/sRGB
```

Y luego:

```bash
camera ... configured with 800x600-XRGB8888/sRGB stream
```

Esto significa que la cadena completa funciona:

```bash
IMX219
   ↓
MIPI CSI
   ↓
RP1 CFE
   ↓
PiSP
   ↓
libcamera 0.6
   ↓
camera_ros
   ↓
ROS 2
```

------

### 25. Calibración

El único mensaje pendiente fue:

```bash
Unable to open camera calibration file
```

con una ruta similar a:

```bash
/home/andino/.ros/camera_info/imx219__base_axi_pcie_120000_rp1_i2c_88000_imx219_10_800x600.yaml
```

Esto NO significa que la cámara esté fallando.

Significa simplemente que todavía no existe el archivo YAML de calibración.

La calibración se hará posteriormente.

------

### 26. Verificar los topics

Con `camera_node` ejecutándose, abrir otra terminal:

```bash
source /opt/ros/jazzy/setup.bash
source ~/camera_ws/install/setup.bash
```

Ejecutar:

```bash
ros2 topic list
```

Deberían aparecer:

```bash
/camera/camera_info
/camera/image_raw
```

También:

```bash
ros2 topic hz /camera/image_raw
```

Esto permite comprobar que están llegando imágenes.

------

### 27. Estado final de la instalación

La configuración que funciona es:

```bash
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

```bash
ros-jazzy-libcamera
```

si intenta cargar:

```bash
libcamera.so.0.7
```

No utilizar:

```bash
v4l2_camera
```

para esta cámara MIPI si `camera_ros` funciona correctamente.

------

### 28. Checklist para repetir en otro robot

#### Sistema

```bash
[ ] Ubuntu 24.04 64-bit
[ ] ROS 2 Jazzy instalado
[ ] Cámara conectada correctamente
[ ] Usuario pertenece al grupo video
```

Comprobar:

```bash
groups
```

------

#### Cámara / kernel

```bash
[ ] camera_auto_detect=1
```

Comprobar:

```bash
sudo dmesg | grep -Ei "imx219|camera|csi|rp1|pisp"
```

Debe aparecer:

```bash
Using sensor imx219
```

y:

```bash
Registered ... /dev/video0
```

------

#### PPA

```bash
[ ] PPA marco-sonic agregado
```

Comando:

```bash
sudo add-apt-repository ppa:marco-sonic/rasppios
sudo apt update
```

Comprobar:

```bash
apt policy libcamera-tools
```

Debe ofrecer:

```bash
0.6.0+rpt20251202-1ubuntu1~marco1
```

------

#### libcamera

```bash
sudo apt install libcamera-dev
```

Comprobar:

```bash
ldconfig -p | grep libcamera
```

Debe aparecer:

```bash
libcamera.so.0.6
libcamera-base.so.0.6
```

------

#### Cámara independiente de ROS

Probar:

```bash
rpicam-still -o test.jpg
```

Si muestra imagen:

```bash
[OK] cámara funcionando
```

------

#### camera_ros

Workspace:

```bash
~/camera_ws
```

Repositorio:

```bash
https://github.com/christianrauch/camera_ros
```

Compilar:

```bash
cd ~/camera_ws
colcon build --packages-select camera_ros --symlink-install
```

Activar:

```bash
source /opt/ros/jazzy/setup.bash
source ~/camera_ws/install/setup.bash
```

------

#### Dependencias

Comprobar:

```bash
ldd ~/camera_ws/install/camera_ros/lib/libcamera_component.so | grep -E 'libcamera|not found'
```

Debe aparecer:

```bash
libcamera.so.0.6
libcamera-base.so.0.6
```

Y NO:

```bash
not found
```

------

#### Prueba ROS

```bash
ros2 run camera_ros camera_node
```

Debe detectar:

```bash
0: imx219
```

------

#### Prueba de topics

En otra terminal:

```bash
source /opt/ros/jazzy/setup.bash
source ~/camera_ws/install/setup.bash

ros2 topic list
```

Y:

```bash
ros2 topic hz /camera/image_raw
```

------

### 29. Comandos de diagnóstico rápidos

#### ¿El kernel ve la cámara?

```bash
sudo dmesg | grep -Ei "imx219|camera|csi|rp1|pisp"
```

#### ¿libcamera instalado?

```bash
ldconfig -p | grep libcamera
```

#### ¿Qué versión ofrece el PPA?

```bash
apt policy libcamera-tools
```

#### ¿Qué cámaras ve libcamera?

```bash
rpicam-still --list-cameras
```

#### ¿La cámara produce imagen?

```bash
rpicam-still -o test.jpg
```

#### ¿Está el usuario en video?

```bash
groups
```

#### ¿Qué versión de camera_ros está instalada?

```bash
ros2 pkg prefix camera_ros
```

#### ¿Está compilado?

```bash
ldd ~/camera_ws/install/camera_ros/lib/libcamera_component.so | grep -E 'libcamera|not found'
```

#### ¿ROS ve la cámara?

```bash
ros2 run camera_ros camera_node
```

#### ¿ROS publica imágenes?

```bash
ros2 topic list
ros2 topic hz /camera/image_raw
```

------

### 30. Regla importante para futuras instalaciones

No cambiar varias cosas simultáneamente.

El orden recomendado es:

```bash
1. Kernel detecta IMX219
2. rpicam funciona
3. libcamera 0.6 instalado
4. camera_ros compilado contra 0.6
5. camera_ros detecta IMX219
6. ROS publica /camera/image_raw
7. Integrar en andino_bringup
8. Calibrar cámara
9. Optimizar formato/resolución
```

Si un paso falla, detenerse ahí.

No avanzar modificando otros componentes hasta determinar qué capa está fallando.

------

## Consideraciones importantes

## Futuros cambios/mejoras

Ahora con la camara tuvimos inconvenientes con el espacio de trabajo, queda rever como acomodarlo.
