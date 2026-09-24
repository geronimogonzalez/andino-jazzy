### 1. Imagen con ruta local → **MODIFICAR**

Esto:

```
[image](file:///home/gero_f/...)
```

es claramente dependiente de la PC donde se escribió.

En Git, otra persona no va a tener esa ruta.

Si la imagen está realmente dentro del repositorio, debería utilizarse una ruta relativa, por ejemplo:

```
docs/robot_rviz.png
```

o la sintaxis correspondiente de Markdown.

Esto probablemente aparezca también en otros README, así que podemos hacer una **limpieza global de rutas `file:///home/...`** cuando terminemos el relevamiento.

------

### 2. "physical properties" → **Mantener, pero revisar qué parámetros tenemos**

Esta parte es importante para nuestro objetivo:

> change the physical properties of some of the components...

Acá seguramente haya parámetros como:

- dimensiones;
- radio de ruedas;
- separación entre ruedas;
- masas;
- inercias;
- posiciones de sensores;
- etc.

**No cambiaría el README todavía**, pero sí anotaría:

> Revisar `config/andino` y los Xacro para comprobar que representan el robot físico actual.

Especialmente porque queremos que los esquemáticos y documentación coincidan con **lo que realmente quedó construido**.

------

### 3. `yaml_config_dir` → probablemente mantener

Esto parece ser una característica del paquete y no algo específico de la instalación vieja.

Si sigue funcionando, no hay razón para eliminarlo.

------

### 4. Los launch files → mantener

Estos dos:

```
ros2 launch andino_description andino_description.launch.py
```

y

```
ros2 launch andino_description view_andino.launch.py
```

parecen perfectamente razonables.

Lo único que verificaría después es que sigan siendo exactamente esos nombres y que sus argumentos no hayan cambiado.

------

### 5. Hay algo que **no agregaría acá**

No metería en este README:

- qué Raspberry usamos;
- qué LiDAR;
- qué Arduino;
- cómo conectar el motor;
- qué puerto USB;
- etc.

Aunque esos elementos aparezcan representados en el URDF.

`andino_description` responde:

> **¿Cómo está representado el robot para ROS?**

No:

> **¿Cómo construyo físicamente el robot?**