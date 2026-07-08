# Raspberry Pi Camera IMX219 en Ubuntu 24.04 (Raspberry Pi 5)

Guía basada en una instalación que quedó funcionando correctamente.





Fecha: 08/07/2026

Hardware:
- Raspberry Pi 5
- Ubuntu 24.04 LTS
- Kernel 6.8.0-1060-raspi
- Cámara IMX219 oficial

Solución:
Instalar PPA marco-sonic/rasppios
Actualizar a libcamera 0.6
Instalar rpicam-apps-lite





---

# 1. Comprobar que el usuario pertenece al grupo video

```bash
groups
```

Debe aparecer:

```
video
```

Si no aparece:

```bash
sudo usermod -aG video $USER
```

Cerrar sesión o reiniciar.

---

# 2. Configurar el archivo /boot/firmware/config.txt

Editar:

```bash
sudo nano /boot/firmware/config.txt
```

Mantener:

```ini
camera_auto_detect=1
display_auto_detect=1
dtoverlay=vc4-kms-v3d
```

No fue necesario agregar manualmente:

```
dtoverlay=imx219
```

ya que Ubuntu detectó correctamente la cámara.

---

# 3. Verificar que el kernel detecta la cámara

```bash
sudo dmesg | grep -Ei "imx219|camera|csi|rp1|pisp"
```

Debe aparecer algo similar a:

```
Using sensor imx219
Registered /dev/video0
Registered /dev/video1
...
```

Si aparecen esas líneas, el hardware está funcionando correctamente.

---

# 4. Verificar los dispositivos V4L2

```bash
for i in {0..7}; do
    echo "========== /dev/video$i =========="
    v4l2-ctl -D -d /dev/video$i
done
```

Los dispositivos deben existir.

---

# 5. Problema encontrado

Con los paquetes originales de Ubuntu:

```
libcamera 0.2
```

ocurría lo siguiente:

```
cam -l
```

mostraba:

```
Available cameras:
```

sin listar ninguna cámara.

Además:

```
ffmpeg
```

no podía abrir `/dev/video0`.

Esto NO era un problema del hardware.

---

# 6. Agregar el PPA de Marco Sonic

```bash
sudo add-apt-repository ppa:marco-sonic/rasppios
```

Actualizar índices:

```bash
sudo apt update
```

---

# 7. Reparar dpkg (si fuese necesario)

En esta instalación apareció:

```
dpkg was interrupted
```

Se solucionó con:

```bash
sudo dpkg --configure -a
```

---

# 8. Actualizar completamente

```bash
sudo apt full-upgrade
```

Durante la actualización se instalaron:

- libcamera 0.6
- librerías Raspberry Pi
- kernel actualizado

---

# 9. Instalar aplicaciones Raspberry Pi

```bash
sudo apt install rpicam-apps-lite python3-picamera2
```

---

# 10. Reiniciar

```bash
sudo reboot
```

---

# 11. Probar la cámara

Capturar una imagen:

```bash
rpicam-still -o test.jpg
```

Resultado esperado:

- Se abre la vista previa.
- Se visualiza correctamente la imagen.
- Se genera:

```
test.jpg
```

Esto confirma que:

- Sensor IMX219 funcionando.
- CSI funcionando.
- RP1 funcionando.
- libcamera funcionando correctamente.

---

# Estado final

## Funciona

✔ IMX219 detectada

✔ rpicam-still

✔ Captura de imágenes

✔ Kernel Ubuntu 24.04

✔ Raspberry Pi 5

✔ PPA Marco Sonic

---

## Pendiente

Queda configurar ROS2 (`camera_ros`) para utilizar la nueva instalación de libcamera.

La cámara ya está completamente operativa; el problema restante pertenece únicamente a la integración con ROS2.