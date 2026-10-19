# Arduino

En este documento se explica cómo compilar y cargar el firmware en el Arduino, así como las pruebas disponibles mediante el puerto serie para verificar el funcionamiento de los periféricos que controla.

> **Importante:** La instalación y el uso de Arduino IDE se realizan desde una **PC de desarrollo** conectada al Arduino. No es necesario instalar Arduino IDE en las Raspberry Pi del robot.

## 1. Entorno de desarrollo

Para trabajar con el firmware desde Linux se utiliza **Arduino IDE 2.x**, distribuido como AppImage.

Durante las pruebas se utilizó Arduino IDE **2.3.10**.

> **Importante:** La versión de Arduino IDE disponible desde la tienda de aplicaciones de Ubuntu presentó problemas durante las pruebas. Por este motivo se recomienda utilizar la versión AppImage de Arduino IDE 2.x.

### Instalación de Arduino IDE

Descargar Arduino IDE 2.x en formato AppImage desde la página oficial de Arduino.

Una vez descargado, otorgar permisos de ejecución al archivo y ejecutarlo.

### Librerías

El firmware utiliza librerías adicionales que deben estar instaladas en Arduino IDE.

Las librerías requeridas se pueden instalar desde:

**Tools → Manage Libraries...**

Las librerías concretas utilizadas por el firmware se detallan más adelante según las dependencias del código.

## 2. Abrir el firmware

Descargar o clonar el repositorio y abrir el proyecto correspondiente desde Arduino IDE.

El archivo principal del firmware debe abrirse desde Arduino IDE para cargar el proyecto.

## 3. Selección de placa y puerto

Conectar el Arduino a la PC mediante USB.

En Arduino IDE seleccionar:

- **Placa:** la correspondiente al Arduino utilizado.
- **Puerto:** el dispositivo USB correspondiente al Arduino.

El puerto puede verificarse desde una terminal con:

```bash
ls -l /dev/ttyUSB*
```

En el sistema configurado para Andino, el Arduino utiliza el alias:

```text
/dev/ttyUSB_ARDUINO
```

## 4. Compilar y cargar el firmware

Antes de cargar el firmware, verificar que la placa y el puerto seleccionados sean los correctos.

Para comprobar que el código puede compilarse, utilizar:

**Sketch → Verify/Compile**

Si la compilación finaliza correctamente, cargar el firmware mediante:

**Sketch → Upload**

Una vez finalizada la carga, el Arduino reiniciará y comenzará a ejecutar el nuevo firmware.

## 5. Comunicación mediante puerto serie

El firmware permite comunicarse con el Arduino mediante el puerto serie para consultar información y realizar pruebas sobre los periféricos que controla.

La comunicación se realiza a:

```text
57600 baud
```

Por ejemplo:

```bash
screen /dev/ttyUSB_ARDUINO 57600
```

Los comandos disponibles y su función se detallan en la tabla correspondiente.

## 6. Pruebas

Las pruebas mediante puerto serie permiten verificar individualmente el funcionamiento del firmware y de los periféricos conectados al Arduino.

Se incluyen pruebas para:

- encoders;
- motores;
- control PID;
- reinicio de los contadores de los encoders.

Los comandos disponibles se documentan a continuación.

## 7. IMU

La comunicación con la IMU fue verificada mediante un **sketch independiente de prueba**.

Este programa permite comprobar la comunicación I²C y obtener los datos proporcionados por la IMU, pero **no forma parte actualmente del firmware principal del robot**.

Estado actual:

- ✓ Comunicación I²C verificada.
- ✓ Lectura de datos de la IMU verificada.
- ⏳ Integración de la IMU en el firmware principal.
- ⏳ Adaptación de los datos al formato requerido por ROS 2.
- ⏳ Integración con los nodos correspondientes de ROS 2.