# Revisar





### `plugdev`

Esta parte:

```
sudo usermod -a -G plugdev $USER
```

la marcaría para **eliminar o verificar**.

`dialout` sí es relevante para acceder al puerto serie. `plugdev` puede haber quedado de una instalación/configuración anterior y no quiero que nuestra documentación nueva indique agregar grupos innecesarios.

Esto además encaja con lo que queremos: **no copiar comandos históricos si no son necesarios para el estado final**.





### Algo que falta

Para nuestro robot actual, este README probablemente debería mencionar que la comunicación serie depende del **Arduino + firmware `andino_firmware`**, y enlazar a ese paquete.

Algo del estilo:

```
andino_base
    ↓
serial
    ↓
andino_firmware (Arduino)
    ↓
drivers de motores + encoders
```

No necesariamente como diagrama formal; incluso dos líneas de explicación podrían bastar.