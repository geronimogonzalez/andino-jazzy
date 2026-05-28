# Instalar Ubuntu en Raspberry Pi

## Qué necesitás

### Hardware

- Raspberry Pi
- microSD (mínimo 16 GB recomendado)
- fuente buena (MUY importante)
- internet
- teclado/monitor (o SSH)

---

# 1) Instalar Raspberry Pi Imager

En Ubuntu:

```bash
sudo snap install rpi-imager
```

Luego abrir:

```bash
rpi-imager
```

---

# 2) Elegir sistema operativo

En:

```text
Choose OS
```

Elegir:

```text
Ubuntu
```

---

# Elegir versión correcta

## Proyectos / liviano / servidor

```text
Ubuntu Server
```

Recomendación:

```text
Ubuntu Server 64-bit
```

y luego instalar entorno gráfico solo si realmente hace falta.

---

# Arquitectura correcta

## Raspberry Pi 4 / 5

Usar:

```text
64-bit
```

## Raspberry Pi 3 vieja

A veces conviene:

```text
32-bit
```

---

# 3) Elegir la microSD

En:

```text
Choose Storage
```

⚠️ Revisar MUY bien no seleccionar otro disco.

---

# 4) Configuración avanzada (MUY útil)

Antes de grabar:

```text
CTRL + SHIFT + X
```

o usar el engranaje ⚙️

---

# Configurar antes de instalar

## Activar SSH

```text
Enable SSH
```

---

## Configurar Wi-Fi

- SSID
- contraseña
- país

---

## Crear usuario y contraseña

# Robot Andino

## 	Sistema Operativo

​				Ubuntu server 24.04.4 LTS (64 bits)

### 		Usuario

​			andino

### 		Contraseña

​			andino

---

# 5) Grabar la SD

```text
Write
```

Esperar a que termine.

---

# 6) Arrancar la Raspberry

Insertar:

- microSD
- alimentación

y esperar.

⚠️ El primer arranque puede tardar varios minutos.

---

# Conectarse por SSH

Desde otra PC:

```bash
ssh usuario@ip_de_la_raspberry
```

Ejemplo:

```bash
ssh gero@192.168.0.25
```

---

# Cómo encontrar la IP

En la Raspberry:

```bash
ip a
```

o desde el router.

También se puede probar:

```bash
ping raspberrypi.local
```

---

# Después de instalar

Actualizar sistema:

```bash
sudo apt update
sudo apt upgrade
```

---

# MUY IMPORTANTE

Muchísimos problemas en Raspberry vienen de:

```text
fuentes de alimentación malas o insuficientes
```

Usar una buena fuente evita problemas raros.

---

# Recomendación final

Para estudio/electrónica/proyectos:

```text
Ubuntu Server 64-bit
SSH activado
sin entorno gráfico al principio
```

Ventajas:

- menos consumo
- más rápido
- menos problemas
- más estable







