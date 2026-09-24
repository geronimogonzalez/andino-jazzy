### 1. Enlaces a documentación de Humble → **MODIFICAR**

Aparece varias veces:

```
control.ros.org/humble/...
```

Nosotros estamos usando **ROS 2 Jazzy**, así que esto debería actualizarse.

Más importante todavía: si los enlaces de `control.ros.org` no necesitan especificar distro, probablemente sea mejor enlazar a la documentación correspondiente a Jazzy o a la documentación general actual.

Esto es una cuestión de documentación, no necesariamente de funcionamiento.

------

### 2. El diagrama `svg` → **REVISAR**

Acá veo algo interesante:

```
The following diagram...
```

y después:

```
svg
```

Si realmente el README renderiza un SVG correctamente en Git, hay que comprobarlo. Si literalmente quedó `svg`, claramente está roto.

Pero **no haría todavía un diagrama nuevo**. Primero tenemos que terminar de establecer cuál es la arquitectura final del robot.

Después sí podemos hacer un diagrama simple que represente **nuestro sistema real**, no necesariamente el original.

------

### 3. Interfaces de ruedas → **VERIFICAR**

La documentación dice:

```
left wheel velocity
right wheel velocity
left wheel position
right wheel position
```

y eso probablemente sigue siendo correcto.

Pero acá quiero que cuando lleguemos al código/configuración verifiquemos los nombres reales de las joints:

```
left_wheel_name
right_wheel_name
```

Porque vos justamente querés que las referencias `L/R` queden consistentes.

No cambiaría nombres por gusto. **El código manda.**

------

### 4. `diff_drive_controller`

La explicación está bien y no hace falta convertirla en una clase de ROS 2 Control.

Lo que sí sería bueno revisar después es **qué parámetros concretos tiene actualmente `andino_controllers.yaml`**.

Por ejemplo:

- radio de rueda
- separación entre ruedas
- límites de velocidad
- frame de odometría
- frame del robot
- publicación de odometría
- etc.

No necesariamente hay que documentarlos todos en este README. Pero si alguno es fundamental para reproducir el robot, debería aparecer en algún sitio.

------

### 5. Algo que probablemente falta

Yo agregaría una sección muy corta de **"Configuración"**, apuntando al YAML:

```
## Configuration

The controller parameters are defined in:

`config/andino_controllers.yaml`
```

Y si ese archivo contiene valores específicos del robot físico, quizás ahí mismo poner una nota:

> Estos parámetros deben corresponder a las dimensiones y características del robot utilizado.

Pero nuevamente: primero revisemos el YAML.