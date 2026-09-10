# Registro diario de Andino



## §006 2026-09-10 Jueves

### Planchado y atacado de PCB :white_check_mark:

Resta hacer los agujeros, planchar la serigrafia y barnizar. 



## §005 2026-09-09 Miércoles

### Prueba del segundo Lidar en rpi roja:

* Funciona en modo estandard

* modifique el lauch del lidar para que funcione en ese modo

* El otro lidar tambien funciona en ese modo, sin errores hasta ahora

* hay que subir el cambio al git

* reemplace la linea 47 del launch (esta comentada):

  ```python
   #scan_mode = LaunchConfiguration('scan_mode', default='Sensitivity')
   scan_mode = LaunchConfiguration('scan_mode', default='Standard')
  ```

* con el segundo lidar, funciona directamente conectado a la rpi, sin el "puente de tension con USB"

|              | Primer LiDAR                       | Segundo LiDAR                      |
| ------------ | ---------------------------------- | ---------------------------------- |
| S/N          | `8CF899F6C9E59AD4C5E59CF7702A3414` | `31FCE7F2C1E29BF2C0E39EF6C02F4937` |
| Firmware     | **1.29**                           | **1.15**                           |
| Hardware Rev | **7**                              | **0**                              |
| Modo         | **Sensitivity**                    | **Standard**                       |
| Alcance      | 12 m                               | 16 m                               |
| Puntos       | 7.9K                               | 2.1K                               |
| Resultado    | Arranca                            | No puede establecer `Sensitivity`  |

* con el modo estandar hay menos puntos, menor resolucion, hay que ver si afecta a la navegacion, de ser asi se podria implementar algo para detectar que lidar es y ponerlo en el modo mas compatible.

### Motores:

En la placa vieja, estaban al reves los encoders, los cambiamos de firmware. En realidad me confundi al rutear ya que la placa esta en espejo, por lo que deberian quedar como los de ekumen. De igual manera, debo invertir por firmware el cable violeta y azul del encoder del motor x.  

* cambie el nombre de la serigrafia de la placa

* intercambiar A y B del nuevo motor L (original ekumen

### Cables:

* [fichas c](https://www.mercadolibre.com.ar/5-ficha-conector-usb-tipo-c-macho-para-armar-cable/up/MLAU3842649796?pdp_filters=item_id:MLA1697912819#is_advertising=true&searchVariation=MLAU3842649796&backend_model=search-backend&be_origin=backend&position=2&search_layout=grid&type=pad&tracking_id=27f82bf6-186d-4439-8f2a-f80a34a613f3&ad_domain=VQCATCORE_LST&ad_position=2&ad_click_id=ZGMxOTk1NjktZmMwMi00M2Y2LTg3YzctNWNkYTQyZmZiNzA2)

* [fichas A](https://www.mercadolibre.com.ar/conector-usb-tipo-a-macho-para-cable-x10-para-armar/up/MLAU3518218001#polycard_client=search-desktop&float_highlight=last_units&be_origin=backend&overlay_label=not_apply&search_layout=grid&position=22&type=product&tracking_id=1b87023a-da6d-437a-a172-52ad288bf332&wid=MLA2506621750&sid=search)

  

## §004 2026-09-08 Martes

PCB:

* sw ✅, falta re medir

* stepdown ✅

* tornillos ✅

* mover motor ✅

* mover ficha ✅ 

Pcb listo, resta verificar y hacer.



## §003 2026-09-07 Lunes

### RPI roja, falla nodo lidar y camara. 

* Lidar desconexion de motor

* camara: no esta disponible libcamera 0.6.0. pude extraer una version vieja de andino2, pero faltan 2 programas mas que no aparecen en el caché. Queda ver que solucion se puede hayar, y dejarla de manera robusta para que no se pise

### sigo con el pcb.

* medir y rehacer
  * sw
  * stepdown
  * tornillos
  * mover motor
  * mover ficha
  * no cortar cables




## §002 2026-09-01 Martes

* Finalizó la copilación en roja y azul.

  * roja, obtuve esta salida:

    ```bash
    4 packages had stderr output:
    andino_base
    nav2_costmap_2d
    nav2_mppi_controller
    nav2_regulated_pure_pursuit_controller
    ```

  * azul, obtuve esta salida:

    ```bash
    3 packages had stderr output:
    nav2_costmap_2d
    nav2_mppi_controller
    nav2_regulated_pure_pursuit_controller
    ```

* problema con la comunicación con la **pc4** (la que tiene la gráfica y el ros2) y los nodos, se soluciono con update/upgrade y:

  ```bash
  ros2 daemon stop
  ros2 daemon start
  ros2 node list
  ```

* acomode el ws, estaba mal los nombres y las rutas

* Cargué el mapa del "Boliche de robotica" en el **WS** nuevo de la **RPI roja** y en el **WS** de la **azul**. El archivo es "mapa_robotica.yaml". Esto lo hice ya que no funcionaba la navegación, porque no estaba el mapa.

* La cámara dejo de andar, seguramente debido a las rutas, luego de mover la carpeta al git, y crear "andino_camera". Resta ver de nuevo en el **WS** viejo como estaba hecho y adaptarlo al nuevo, (con el git **GCA**). 

* **hacer mañana:**

  * Copilación en **RPI roja** (andino, ubuntu):

  ```bash
  cd ~/robot_ws1
  rm -rf build install log
  colcon build --symlink-install
  ```

  * Probar bringup en este **WS**:

  ```bash
  source ~/robot_ws1/install/setup.bash
  ros2 launch andino_bringup andino_robot.launch.py
  ```

  * En **RPI azul** (andino1, ubuntu1), la carpeta del git ("andino") estaba en minuscula, la cambie a mayuscula para que quede la ruta igual a la que teniamos en la primer RPI (la roja) (ya que linux distingue de mayusculas y minusculas en las rutas). Por lo que supongo que también deberíamos **Re-copilar** esta RPI.

  

## §001 2026-08-31 Lunes

* Asignación de **IPs** por reserva **DHCP** en el **router**.
* Creación de nuevo **WS** en roja.
* Actualización de **git** en **WS** nuevo **roja**, y en **blanca**.
* Comienzo de **copilación** del **git** nuevo en la **roja** y **azul**.
* Falta:
  * **Imprimir** etiqueta para router y andinos.
  * Probar las **IMUs** con el segundo arduino.
  * Probar robot con todas las **RPIs**, (con la roja el WS nuevo).
  * Probar segundo **Lidar**.
  * Flashear los dos arduinios.
  * Imprimir faltantes para **blanco** (modificar medidas).
  * Hacer **PCBs**.