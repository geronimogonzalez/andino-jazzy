# Cámara en Raspberry Pi 5 - Diagnóstico y configuración

## Problema típico

La Raspberry Pi 5 usa un conector CSI distinto al de las Raspberry Pi 3/4.

La cámara vieja puede necesitar:

- cable distinto
- adaptador
- orientación específica

---

# Lo más probable

## El flex está:

- invertido
- mal insertado
- mal trabado

y eso alcanza para que la cámara no aparezca.

---

# IMPORTANTE

La Raspberry Pi 5 usa:

```text
conector MIPI CSI más pequeño
```

Por eso muchos módulos necesitan:

```text
cable adaptador específico para Pi 5
```

---

# MUY IMPORTANTE: orientación del flex

En Raspberry:

```text
la orientación de los contactos metálicos importa muchísimo
```

Si el cable está invertido:

```text
la cámara no aparece directamente
```

---

# Recomendación inicial

## Apagar completamente la Raspberry

Nunca conectar/desconectar cámara con la Pi encendida.

---

# Verificar conexión

## Revisar:

- que el flex esté totalmente insertado
- que la traba esté cerrada
- orientación correcta de contactos
- probar el otro puerto CSI de la Pi 5

La Raspberry Pi 5 tiene:

```text
2 puertos CSI
```

---

# Instalar herramientas necesarias

Ubuntu moderno usa:

```text
libcamera
```

No usar:

```text
raspistill
```

porque pertenece al stack viejo.

---

# Instalar soporte cámara

```bash
sudo apt update
sudo apt install libcamera-apps
```

---

# Probar cámara

```bash
libcamera-hello
```

---

# Resultado esperado

Si funciona:

- aparece preview
- detecta la cámara

---

# Si falla

Puede aparecer:

```text
no cameras available
```

Eso normalmente indica:

- problema de hardware
- flex invertido
- adaptador incorrecto
- conexión floja

---

# Verificar dispositivos

```bash
ls /dev/video*
```

---

# IMPORTANTE sobre adaptadores

Muchos adaptadores baratos:

```text
son físicamente compatibles
pero eléctricamente incorrectos
```

---

# Checklist rápido

```text
1. apagar Raspberry
2. reconectar flex
3. revisar orientación
4. trabar correctamente
5. probar otro puerto CSI
6. instalar libcamera-apps
7. ejecutar libcamera-hello
```

---

# Sospecha más probable

Por el síntoma:

```text
la cámara no aparece
```

lo más probable es:

- flex invertido
- adaptador incorrecto
- mala conexión

más que problema de software.