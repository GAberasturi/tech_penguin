# TECH PENGUIN - KATALLER DOCKERS





## Practica 2 - Kafka



El objetivo de la practica es conectar desde una aplicación java arrancada en un servidor de aplicaciones java Open Liberty a una BBDD mongo.

Para realizar una gestión mas sencilla del entorno se creara un fichero `docker-compose.yml` con el cual poder arrancar tanto la BBDD mongo con la aplicación en modo "desarrollador" e ir modificándola para ver como funciona.



> **NOTA:** se ha utilizado como referencia para la practica la guía de open liberty [guide-microprofile-reactive-messaging.html](https://openliberty.io/guides/microprofile-reactive-messaging.html) 



### 01. Revisión del proyecto

La estructura del proyecto debe ser la siguiente

```
TECH_PENGUIN\PRACTICAS\JAVA\Kafka
+---assets
+---finish
|   +---inventory
|   |   \---src
|   |       +---main
|   |       |   +---java
|   |       |   |   \---io
|   |       |   |       \---openliberty
|   |       |   |           \---guides
|   |       |   |               \---inventory
|   |       |   |                   \---health
|   |       |   +---liberty
|   |       |   |   \---config
|   |       |   +---resources
|   |       |   |   \---META-INF
|   |       |   \---webapp
|   |       |       \---WEB-INF
|   |       \---test
|   |           \---java
|   |               \---it
|   |                   \---io
|   |                       \---openliberty
|   |                           \---guides
|   |                               \---inventory
|   +---models
|   |   \---src
|   |       \---main
|   |           \---java
|   |               \---io
|   |                   \---openliberty
|   |                       \---guides
|   |                           \---models
|   +---scripts
|   \---system
|       \---src
|           +---main
|           |   +---java
|           |   |   \---io
|           |   |       \---openliberty
|           |   |           \---guides
|           |   |               \---system
|           |   |                   \---health
|           |   +---liberty
|           |   |   \---config
|           |   +---resources
|           |   |   \---META-INF
|           |   \---webapp
|           |       \---WEB-INF
|           \---test
|               \---java
|                   \---it
|                       \---io
|                           \---openliberty
|                               \---guides
|                                   \---system
+---scripts
\---start
    +---inventory
    |   \---src
    |       +---main
    |       |   +---java
    |       |   |   \---io
    |       |   |       \---openliberty
    |       |   |           \---guides
    |       |   |               \---inventory
    |       |   |                   \---health
    |       |   +---liberty
    |       |   |   \---config
    |       |   +---resources
    |       |   |   \---META-INF
    |       |   \---webapp
    |       |       \---WEB-INF
    |       \---test
    |           \---java
    |               \---it
    |                   \---io
    |                       \---openliberty
    |                           \---guides
    |                               \---inventory
    +---models
    |   \---src
    |       \---main
    |           \---java
    |               \---io
    |                   \---openliberty
    |                       \---guides
    |                           \---models
    +---scripts
    \---system
        \---src
            +---main
            |   +---java
            |   |   \---io
            |   |       \---openliberty
            |   |           \---guides
            |   |               \---system
            |   |                   \---health
            |   +---liberty
            |   |   \---config
            |   +---resources
            |   |   \---META-INF
            |   \---webapp
            |       \---WEB-INF
            \---test
                \---java
                    \---it
                        \---io
                            \---openliberty
                                \---guides
                                    \---system

\tech_penguin\practicas\java>
```



El contenido de las carpetas es el siguiente

- **assest**

  Ficheros de configuración necesarios para inicializar la BBDD Mongo

- **finish**

  Ejemplo original resuelto.

- **scripts**

  Scripts del ejemplo original.

- **start**

  Ficheros sobre los que trabajaremos.

- **test**

  Tests del ejemplo original.



### 02. Creacion de productor

Se crea un servicio que actuara como productor creando un stream de mensajes en kafka. Cada 15 segundos el microservicio  `system` publica un evento con la media de la carga del sistema del ultimo minuto.



Se copia el fichero `finish/system/src/main/java/io/openliberty/guides/system/SystemService.java` en `start/system/src/main/java/io/openliberty/guides/system/SystemService.java`





### 03. Creacion de un consumidor

El micrservicio `inventory` guarda en su inventario la informacion de la media de la carga del sistema que recibe de diferentes instancias del microservicio `system`.



Se copia el fichero `finish/inventory/src/main/java/io/openliberty/guides/inventory/InventoryResource.java` en `start/inventory/src/main/java/io/openliberty/guides/inventory/InventoryResource.java`



### 04. Configurando los conectors a kafka

Se configuran los ficheros de propiedades para conectarse a kafka de los microservicios `system` e  `inventory` .



Se copia los ficheros

 `finish/system/src/main/resources/META-INF/microprofile-config.properties` en `start/system/src/main/resources/META-INF/microprofile-config.properties`

`finish/inventory/src/main/resources/META-INF/microprofile-config.properties` en `start/inventory/src/main/resources/META-INF/microprofile-config.properties`





### 



### Incidencias Practica 2 - Kafka

Sección con incidencias que nos podemos encontrar.





### Ejemplos de salidas de comandos






## Referencias







**Java Kafka**

- https://openliberty.io/guides/microprofile-reactive-messaging.html



**AKHQ**

- https://akhq.io