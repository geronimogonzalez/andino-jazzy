Sí. **Este es uno de los que yo marcaría como "no tocar todavía"**, justamente por lo que decís. Acá no alcanza con comparar el README con el código: tenemos que reconstruir qué quedó físicamente conectado y qué corresponde al firmware actual.

Y además hay una cosa importante: si hubo **dos cambios sucesivos en los pines de encoder**, el estado final del archivo puede no coincidir con tu recuerdo de cuál de los dos quedó aplicado. Así que no asumiría nada.

### `andino_firmware/README`

#### Lo que parece seguir correcto

- Firmware para el Arduino.
- Arduino IDE + `Adafruit BNO055`.
- PlatformIO como alternativa.
- Comunicación serie a **57600**.
- Comandos `e`, `r`, `m`, `o`, `u`, etc.
- La explicación general de open-loop / closed-loop.

Eso probablemente se pueda conservar.

------

## Pero hay 3 puntos que tenemos que revisar

### 1. **Pines de encoder — CRÍTICO**

El README dice:

> Check `encoder_driver.h` and `motor_driver.h` files to check the expected pins...

Eso es insuficiente para nuestra documentación final si precisamente **el diagrama original de la empresa estaba equivocado**.

Pero tampoco pondría los pines en el README todavía.

Primero necesitamos determinar:

```
Arduino
 ├── Encoder izquierdo → ¿pines?
 ├── Encoder derecho   → ¿pines?
 ├── Motor izquierdo   → ¿pines?
 └── Motor derecho     → ¿pines?
```

Y comparar **tres cosas**:

1. Lo que está físicamente cableado.
2. Lo que dice el esquema/documentación de la empresa.
3. Lo que espera el firmware actual.

El objetivo final es que **1 y 3 coincidan**. El 2 nos sirve únicamente para detectar qué estaba mal originalmente.

Y justamente no confiaría en `encoder_driver.h` como única fuente de verdad hasta hacer esa comparación.

------

### 2. `left` / `right` — **también verificar**

Acá aparece repetidamente:

```
<left> <right>
```

Eso puede parecer trivial, pero después de los cambios de cableado tenemos que asegurarnos de que:

```
LEFT
```

signifique consistentemente:

> motor/encoder físico izquierdo

y no que en algún punto haya quedado invertido.

Esto lo podemos comprobar cuando revisemos:

- pines;
- `encoder_driver`;
- `motor_driver`;
- configuración de `andino_base`;
- nombres de joints;
- sentido de giro.

**No corregiría ningún `left/right` todavía.**

------

### 3. Calibración `ticks per revolution`

También la marcaría para revisar.

El README pone el ejemplo de `700`, pero **ese número no debería quedar como si fuera el valor real del robot**.

Más adelante podemos determinar:

> ¿Cuál es el valor que efectivamente usa nuestro firmware/controlador?

Y si el valor depende de la configuración concreta del encoder, dejarlo explícito.

------

## Hay otra cosa interesante: este README mezcla dos niveles

Tiene:

**Instalación del firmware**

y después:

**Uso/testeo del firmware**

Eso me parece bien, pero para la versión final probablemente quedaría mejor algo como:

```
# andino_firmware

## Descripción

## Hardware y conexiones
   → referencia al esquema

## Instalación
   Arduino IDE
   PlatformIO

## Comunicación serie

## Pruebas
   Encoder
   Motores
   IMU

## Comandos
```

Y en **Hardware y conexiones** podríamos poner un esquema realmente nuestro, una vez que lo hayamos verificado.

------

### Sobre el cambio que hiciste

Yo incluso dejaría anotado internamente durante esta revisión:

> **PENDIENTE: verificar pines finales de encoders. Hubo corrección respecto al esquema original y posteriormente una segunda modificación. Determinar estado final físico + firmware.**

No hace falta que eso termine en la documentación final. Es una nota de trabajo para **nosotros**, para no olvidarnos.

Y este es justamente el tipo de cosa por la que me parece acertado que estés haciendo primero el relevamiento y después la redacción.

**Este archivo queda "pendiente de verificación de hardware", no lo damos por bueno todavía.**

Cuando terminemos todos los README, ahí podemos volver a este y comparar directamente código ↔ conexiones ↔ esquemas.







## IMU

La comunicación con la IMU fue verificada mediante un sketch
independiente de prueba. Este programa permite comprobar la comunicación
I²C y obtener los datos proporcionados por la IMU, pero no forma parte
del firmware actual del robot.

### Estado

- ✓ Comunicación I²C verificada.
- ✓ Lectura de datos de la IMU verificada.
- ⏳ Integración de la IMU en el firmware principal.
- ⏳ Adaptación de los datos al formato requerido por ROS 2.
- ⏳ Integración con los nodos correspondientes de ROS 2.

# IMU

Con estos codigos funcioina la **IMU** de manera independiente, resta hacerlo compatible con el firmware de andino

## Liberia

MPU6050_light



## Código

```c
\#include <Wire.h>

\#include <MPU6050_light.h>

MPU6050 mpu(Wire);

void setup() {

  Serial.begin(115200);

  Wire.begin();

  Serial.println("Inicializando...");

  byte status = mpu.begin();

  Serial.print("Status: ");

  Serial.println(status);

  if (status != 0) {

​    Serial.println("Error MPU6050");

​    while (1);

  }

  Serial.println("Calibrando...");

  mpu.calcOffsets(true, true);

  Serial.println("Listo");

}

void loop() {

  mpu.update();

  Serial.print("Acc X: ");

  Serial.print(mpu.getAccX());

  Serial.print(" Y: ");

  Serial.print(mpu.getAccY());

  Serial.print(" Z: ");

  Serial.println(mpu.getAccZ());

  delay(200);

}
```









## Código con giroscopio



```c
#include <Wire.h>
#include <MPU6050_light.h>

MPU6050 mpu(Wire);

unsigned long timer = 0;

void setup() {
  Serial.begin(115200);
  Wire.begin();

  Serial.println("Inicializando MPU6050...");

  byte status = mpu.begin();
  if (status != 0) {
    Serial.println("Error de conexión MPU6050");
    while (1);
  }

  Serial.println("Calibrando... no muevas el sensor");
  delay(1000);
  mpu.calcOffsets(true, true); // accel + gyro
  Serial.println("Listo.");
}

void loop() {
  mpu.update();

  if (millis() - timer > 200) {
    timer = millis();

    // Aceleración (g)
    Serial.print("Acc [g] X: ");
    Serial.print(mpu.getAccX());
    Serial.print(" Y: ");
    Serial.print(mpu.getAccY());
    Serial.print(" Z: ");
    Serial.println(mpu.getAccZ());

    // Giróscopo (°/s)
    Serial.print("Gyro [°/s] X: ");
    Serial.print(mpu.getGyroX());
    Serial.print(" Y: ");
    Serial.print(mpu.getGyroY());
    Serial.print(" Z: ");
    Serial.println(mpu.getGyroZ());

    // Ángulos estimados (roll, pitch)
    Serial.print("Angle X (roll): ");
    Serial.print(mpu.getAngleX());
    Serial.print("  Y (pitch): ");
    Serial.println(mpu.getAngleY());

    Serial.println("----------------------");
  }
}
```

