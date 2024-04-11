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





### 05. Configurando Open Liberty

Para que el microservicio `system`funcione correctamente se deben de configurar las caracteristicas del servidor Open Liberty



`finish/system/src/main/liberty/config/server.xml` en `start/system/src/main/liberty/config/server.xml`



### 06. Compilando la aplicacion

Se compilan los microservicios `system` e `inventory`usando maven para ellos se crea un fichero pom.xml.



Copiar  el fichero `finish/system/pom.xml` en `start/system/pom.xml`



Como no se dispone de maven en el equipo local, se arranca un contenedor para poder compilar y empaquetar la aplicacion.

> **NOTA:** Ajustar el path del directorio local `start`



```
docker run -ti -v \practicas\java\kafka\start\:/start/ maven:3.9.6-ibm-semeru-11-focal bash
```

[Ejemplos de salidas de comandos - Contenedor maven para compilar y empaquetar](#contenedor-maven-para-compilar-y-empaquetar).



Desde dentro del contenedor nos ubicamos en `/start/` y se lanzan los comandos

```
mvn -pl models install
mvn package
```

[Ejemplos de salidas de comandos - mvn -pl models install](#mvn--pl-models-install).

[Ejemplos de salidas de comandos - mvn package](#mvn-package).



Una vez finalizados se sale del contenedor y en las carpetas `start/system/`  y  `start/inventory/`  habra un directorio `target` y dentro de el las aplicaciones `system.war` y `inventory.war` respectivamente.

Se compilar los contenedores de los microservicios `system`e `inventory` desde el directorio `start`ejecutar

```
docker build -t system:1.0-SNAPSHOT system/.
docker build -t inventory:1.0-SNAPSHOT inventory/.
```

[Ejemplos de salidas de comandos - Docker build system](#docker-build-system).

[Ejemplos de salidas de comandos - Docker build inventory](#docker-build-inventory).





### 07. Creando docker-compose



**docker-compose.yml**

```
#################################################################################
#
# docker-compose para probar como conectar una aplicacion java a kafka.
#
#
# v0.1
# Latest updated: 2024-04-12
#
#################################################################################

version: '3'

networks:
  tech-penguin-net:
    driver: bridge

services:  
  
###########
## KAFKA ##
###########
  kafka:
    build:
      context: .
      dockerfile: assets/Dockerfile
    image: bitnami/kafka:latest
    hostname: kafka
    container_name: tech-penguin-kafka-1
    environment:
      ALLOW_PLAINTEXT_LISTENER: "yes"
      KAFKA_CFG_ADVERTISED_LISTENERS: "PLAINTEXT://kafka:9092"
      KAFKA_CFG_NODE_ID: 0
      KAFKA_CFG_PROCESS_ROLES: controller,broker
      KAFKA_CFG_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_CFG_CONTROLLER_QUORUM_VOTERS: 0@kafka:9093
      KAFKA_CFG_CONTROLLER_LISTENER_NAMES: CONTROLLER
    networks:
      - tech-penguin-net

  akhq:
    image: tchiotludo/akhq
    environment:
      AKHQ_CONFIGURATION: |
        akhq:
          connections:
            docker-kafka-server:
              properties:
                bootstrap.servers: "kafka:9092"
    networks:
      - tech-penguin-net
    ports:
      - "8080:8080"
    depends_on:
      - kafka

###########
## APP ##
###########
  
  tech-penguin-system-app:
    build:
      context: .
      dockerfile: system/Dockerfile
    image: system:1.0-SNAPSHOT
    container_name: tech-penguin-system-app-1
    environment:
      MP_MESSAGING_CONNECTOR_LIBERTY_KAFKA_BOOTSTRAP_SERVERS: kafka:9092
      WLP_LOGGING_CONSOLE_LOGLEVEL: info
    networks:
      - tech-penguin-net
    ports:
      - 9083:9083

  tech-penguin-inventory-app:
    build:
      context: .
      dockerfile: inventory/Dockerfile
    image: inventory:1.0-SNAPSHOT
    container_name: tech-penguin-inventory-appp-1
    environment:
      MP_MESSAGING_CONNECTOR_LIBERTY_KAFKA_BOOTSTRAP_SERVERS: kafka:9092
      WLP_LOGGING_CONSOLE_LOGLEVEL: info
    networks:
      - tech-penguin-net
    ports:
      - 9085:9085
```



se guarda el fichero `docker-compose.yml` en la carpeta `start`



Se arranca el entorno

```
docker compose up
```

[Ejemplos de salidas de comandos - Entorno arrancado](#entorno-arrancado).



Una vez arrancado el entorno se puede comprobar accediendo a las URLS



- HealthCheck Microservicio Inventory: http://localhost:9085/health
- http://localhost:9085/inventory/systems
- Consola AKHQ de Kafka: http://localhost:8080









### Incidencias Practica 2 - Kafka

Sección con incidencias que nos podemos encontrar.





### Ejemplos de salidas de comandos



#### Contenedor maven para compilar y empaquetar

```
\practicas\java>docker run -ti -v \practicas\java\kafka\start/:/start/ maven:3.9.6-ibm-semeru-11-focal bash
root@492032fc46bf:/#
```



#### mvn -pl models install

```
. . .
Downloaded from central: https://repo.maven.apache.org/maven2/org/codehaus/plexus/plexus-utils/3.4.2/plexus-utils-3.4.2.jar (267 kB at 3.1 MB/s)
Downloaded from central: https://repo.maven.apache.org/maven2/org/apache/commons/commons-compress/1.21/commons-compress-1.21.jar (1.0 MB at 9.7 MB/s)
[INFO] Building jar: /start/models/target/models-1.0-SNAPSHOT.jar
[INFO]
[INFO] --- install:3.1.1:install (default-install) @ models ---
[INFO] Installing /start/models/pom.xml to /root/.m2/repository/io/openliberty/guides/models/1.0-SNAPSHOT/models-1.0-SNAPSHOT.pom
[INFO] Installing /start/models/target/models-1.0-SNAPSHOT.jar to /root/.m2/repository/io/openliberty/guides/models/1.0-SNAPSHOT/models-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  10.485 s
[INFO] Finished at: 2024-04-10T23:52:40Z
[INFO] ------------------------------------------------------------------------
```



#### mvn package

```
[INFO]
[INFO] --- resources:3.3.1:resources (default-resources) @ inventory ---
[INFO] Copying 2 resources from src/main/resources to target/classes
[INFO]
[INFO] --- compiler:3.11.0:compile (default-compile) @ inventory ---
[INFO] Changes detected - recompiling the module! :source
[INFO] Compiling 5 source files with javac [debug target 11] to target/classes
[INFO]
[INFO] --- resources:3.3.1:testResources (default-testResources) @ inventory ---
[INFO] skip non existing resourceDirectory /start/inventory/src/test/resources
[INFO]
[INFO] --- compiler:3.11.0:testCompile (default-testCompile) @ inventory ---
[INFO] Changes detected - recompiling the module! :dependency
[INFO] Compiling 2 source files with javac [debug target 11] to target/test-classes
[INFO] /start/inventory/src/test/java/it/io/openliberty/guides/inventory/InventoryServiceIT.java: /start/inventory/src/test/java/it/io/openliberty/guides/inventory/InventoryServiceIT.java uses unchecked or unsafe operations.
[INFO] /start/inventory/src/test/java/it/io/openliberty/guides/inventory/InventoryServiceIT.java: Recompile with -Xlint:unchecked for details.
[INFO]
[INFO] --- surefire:3.2.5:test (default-test) @ inventory ---
[INFO]
[INFO] --- war:3.4.0:war (default-war) @ inventory ---
[INFO] Packaging webapp
[INFO] Assembling webapp [inventory] in [/start/inventory/target/inventory]
[INFO] Processing war project
[INFO] Copying webapp resources [/start/inventory/src/main/webapp]
[INFO] Building war: /start/inventory/target/inventory.war
[INFO]
[INFO] ----< io.openliberty.guides:guide-microprofile-reactive-messaging >-----
[INFO] Building guide-microprofile-reactive-messaging 1.0-SNAPSHOT        [4/4]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for guide-microprofile-reactive-messaging 1.0-SNAPSHOT:
[INFO]
[INFO] models ............................................. SUCCESS [  1.575 s]
[INFO] system ............................................. SUCCESS [ 13.400 s]
[INFO] inventory .......................................... SUCCESS [  8.880 s]
[INFO] guide-microprofile-reactive-messaging .............. SUCCESS [  0.001 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  24.011 s
[INFO] Finished at: 2024-04-10T23:53:20Z
[INFO] ------------------------------------------------------------------------
root@492032fc46bf:/start#
```



#### Docker build system

```
\practicas\java\kafka\start>docker build -t system:1.0-SNAPSHOT system/.
[+] Building 23.2s (9/9) FINISHED                                                                                                                                                                                           docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                                  0.0s
 => => transferring dockerfile: 1.01kB                                                                                                                                                                                                0.0s
 => [internal] load metadata for icr.io/appcafe/open-liberty:kernel-slim-java11-openj9-ubi                                                                                                                                            2.0s
 => [internal] load .dockerignore                                                                                                                                                                                                     0.0s
 => => transferring context: 2B                                                                                                                                                                                                       0.0s
 => [1/4] FROM icr.io/appcafe/open-liberty:kernel-slim-java11-openj9-ubi@sha256:fe42abaed3648d415f4115a2d04a037bfcd281cff111cfc25cbda510ceae43aa                                                                                      2.7s
 => => resolve icr.io/appcafe/open-liberty:kernel-slim-java11-openj9-ubi@sha256:fe42abaed3648d415f4115a2d04a037bfcd281cff111cfc25cbda510ceae43aa                                                                                      0.0s
 => => sha256:fe42abaed3648d415f4115a2d04a037bfcd281cff111cfc25cbda510ceae43aa 1.39kB / 1.39kB                                                                                                                                        0.0s
 => => sha256:f737b8d6e8e2f9945b3b35b8955d05df9ecdd441695e4f91738de6bf1c598f5b 242B / 242B                                                                                                                                            0.6s
 => => sha256:3e76f103e977aaf895e2da0765ccb44354c9d2c00d041d9f518e7cc859ecd0c1 3.88kB / 3.88kB                                                                                                                                        0.0s
 => => sha256:ab9a1df523a1e6e3eb562e78a5c1b83c21d53eebe3f0695f9121319f0faabebc 27.92kB / 27.92kB                                                                                                                                      0.0s
 => => sha256:e89240f4864c9572e7bfcd03e1afa2c1f8d58b6d536f15c5d6117f357e6733ba 9.40kB / 9.40kB                                                                                                                                        0.8s
 => => sha256:db89a2f3db6daa0b7e9792dd24ebbd44ec0e139b786603de59ba7a213eaa1193 1.05kB / 1.05kB                                                                                                                                        0.9s
 => => sha256:896d1c26d2ac2618a7140ce243372c1d379778286d1fe5e7629c2d08cb4e8a93 31.75kB / 31.75kB                                                                                                                                      0.9s
 => => sha256:69bfa8e296eecab336cd3daf3540cea649bc9c0f7aa06145a917e583a3961042 759B / 759B                                                                                                                                            1.2s
 => => extracting sha256:db89a2f3db6daa0b7e9792dd24ebbd44ec0e139b786603de59ba7a213eaa1193                                                                                                                                             0.0s
 => => extracting sha256:e89240f4864c9572e7bfcd03e1afa2c1f8d58b6d536f15c5d6117f357e6733ba                                                                                                                                             0.0s
 => => sha256:2b438c2caddd33d7c2711192c3a75e19bf02dd8884e6288cadb078e57ff89b6c 5.27kB / 5.27kB                                                                                                                                        1.3s
 => => sha256:1197f3665d899d3da3f6192a5c7740f6b3fa8c88b56f78778e1558fd079b1cb0 13.96MB / 13.96MB                                                                                                                                      1.9s
 => => extracting sha256:f737b8d6e8e2f9945b3b35b8955d05df9ecdd441695e4f91738de6bf1c598f5b                                                                                                                                             0.0s
 => => extracting sha256:896d1c26d2ac2618a7140ce243372c1d379778286d1fe5e7629c2d08cb4e8a93                                                                                                                                             0.0s
 => => extracting sha256:69bfa8e296eecab336cd3daf3540cea649bc9c0f7aa06145a917e583a3961042                                                                                                                                             0.0s
 => => sha256:650777ddf2c4ec590d74bc61662bd1de0b4adf610ed57b618091c9b8519a415c 551.73kB / 551.73kB                                                                                                                                    1.7s
 => => sha256:e25e245f04597c4207f00d252faf7ca4b0a11506e2219fa6eb78097d975bc455 817B / 817B                                                                                                                                            1.7s
 => => sha256:f022e6db85a1fe7daaf7fac66967209da99c6638a056fd1a99f817d7a2e939b4 2.91MB / 2.91MB                                                                                                                                        2.3s
 => => sha256:f7035131774b9b17c7c26ef5b4feac8c4c0c00d9842e125ac60b93ecfad98583 13.30kB / 13.30kB                                                                                                                                      2.1s
 => => extracting sha256:1197f3665d899d3da3f6192a5c7740f6b3fa8c88b56f78778e1558fd079b1cb0                                                                                                                                             0.2s
 => => extracting sha256:2b438c2caddd33d7c2711192c3a75e19bf02dd8884e6288cadb078e57ff89b6c                                                                                                                                             0.0s
 => => extracting sha256:650777ddf2c4ec590d74bc61662bd1de0b4adf610ed57b618091c9b8519a415c                                                                                                                                             0.1s
 => => extracting sha256:e25e245f04597c4207f00d252faf7ca4b0a11506e2219fa6eb78097d975bc455                                                                                                                                             0.0s
 => => extracting sha256:f7035131774b9b17c7c26ef5b4feac8c4c0c00d9842e125ac60b93ecfad98583                                                                                                                                             0.0s
 => => extracting sha256:f022e6db85a1fe7daaf7fac66967209da99c6638a056fd1a99f817d7a2e939b4                                                                                                                                             0.2s
 => [internal] load build context                                                                                                                                                                                                     0.8s
 => => transferring context: 19.32MB                                                                                                                                                                                                  0.8s
 => [2/4] COPY --chown=1001:0 src/main/liberty/config /config/                                                                                                                                                                        0.1s
 => [3/4] RUN features.sh                                                                                                                                                                                                            18.0s
 => [4/4] COPY --chown=1001:0 target/system.war /config/apps                                                                                                                                                                          0.1s
 => exporting to image                                                                                                                                                                                                                0.2s
 => => exporting layers                                                                                                                                                                                                               0.2s
 => => writing image sha256:e0342414d8fbf1539a73b01342fa3c5833351fdc0f91c6d5c88cc0f37a10edc4                                                                                                                                          0.0s
 => => naming to docker.io/library/system:1.0-SNAPSHOT                                                                                                                                                                                0.0s

View build details: docker-desktop://dashboard/build/default/default/xna6b9roq7neoat5ug0fuxi7l

What's Next?
  View a summary of image vulnerabilities and recommendations → docker scout quickview

\practicas\java\kafka\start>
```







#### Docker build inventory

```
\practicas\java\kafka\start>docker build -t inventory:1.0-SNAPSHOT inventory/.
[+] Building 50.5s (9/9) FINISHED                                                                                                                                                                                           docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                                  0.0s
 => => transferring dockerfile: 1.03kB                                                                                                                                                                                                0.0s
 => [internal] load metadata for icr.io/appcafe/open-liberty:kernel-slim-java11-openj9-ubi                                                                                                                                            0.2s
 => [internal] load .dockerignore                                                                                                                                                                                                     0.0s
 => => transferring context: 2B                                                                                                                                                                                                       0.0s
 => CACHED [1/4] FROM icr.io/appcafe/open-liberty:kernel-slim-java11-openj9-ubi@sha256:fe42abaed3648d415f4115a2d04a037bfcd281cff111cfc25cbda510ceae43aa                                                                               0.0s
 => [internal] load build context                                                                                                                                                                                                     0.7s
 => => transferring context: 17.01MB                                                                                                                                                                                                  0.7s
 => [2/4] COPY --chown=1001:0 src/main/liberty/config /config/                                                                                                                                                                        0.8s
 => [3/4] RUN features.sh                                                                                                                                                                                                            36.4s
 => [4/4] COPY --chown=1001:0 target/inventory.war /config/apps                                                                                                                                                                      10.6s
 => exporting to image                                                                                                                                                                                                                0.4s
 => => exporting layers                                                                                                                                                                                                               0.4s
 => => writing image sha256:eb4b9e268b8a8995290fee6fe03fb4c17542f661ef8de1a57e5692e6ca86d30b                                                                                                                                          0.0s
 => => naming to docker.io/library/inventory:1.0-SNAPSHOT                                                                                                                                                                             0.0s

View build details: docker-desktop://dashboard/build/default/default/j6jqm64vzpgpbhz09eygapewl

What's Next?
  View a summary of image vulnerabilities and recommendations → docker scout quickview

\practicas\java\kafka\start>
```



#### Entorno arrancado

```
ator.group.GroupCoordinator)
tech-penguin-inventory-appp-1  | [INFO    ] RESTEASY002225: Deploying jakarta.ws.rs.core.Application: class io.openliberty.guides.inventory.InventoryApplication$Proxy$_$$_WeldClientProxy
tech-penguin-inventory-appp-1  | [INFO    ] SRVE0242I: [inventory] [/] [io.openliberty.guides.inventory.InventoryApplication]: Initialization successful.
tech-penguin-kafka-1           | [2024-04-11 15:18:48,530] INFO [GroupCoordinator 0]: Stabilized group system-load-status generation 3 (__consumer_offsets-21) with 1 members (kafka.coordinator.group.GroupCoordinator)
tech-penguin-inventory-appp-1  | [err] [Default Executor-thread-10] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-system-load-status-1, groupId=system-load-status] Successfully joined group with generation Generation{generationId=3, memberId='consumer-system-load-status-1-f1abfc24-f3a5-4f85-90f6-90113876df41', protocol='range'}
tech-penguin-inventory-appp-1  | [err] [Default Executor-thread-10] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-system-load-status-1, groupId=system-load-status] Finished assignment for group at generation 3: {consumer-system-load-status-1-f1abfc24-f3a5-4f85-90f6-90113876df41=Assignment(partitions=[system.load-0])}
tech-penguin-kafka-1           | [2024-04-11 15:18:48,582] INFO [GroupCoordinator 0]: Assignment received from leader consumer-system-load-status-1-f1abfc24-f3a5-4f85-90f6-90113876df41 for group system-load-status for generation 3. The group has 1 members, 0 of which are static. (kafka.coordinator.group.GroupCoordinator)
tech-penguin-inventory-appp-1  | [err] [Default Executor-thread-10] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-system-load-status-1, groupId=system-load-status] Successfully synced group in generation Generation{generationId=3, memberId='consumer-system-load-status-1-f1abfc24-f3a5-4f85-90f6-90113876df41', protocol='range'}
tech-penguin-inventory-appp-1  | [err] [Default Executor-thread-10] INFO org.apache.kafka.clients.consumer.internals.ConsumerCoordinator - [Consumer clientId=consumer-system-load-status-1, groupId=system-load-status] Notifying assignor about the new Assignment(partitions=[system.load-0])
tech-penguin-inventory-appp-1  | [err] [Default Executor-thread-10] INFO org.apache.kafka.clients.consumer.internals.ConsumerRebalanceListenerInvoker - [Consumer clientId=consumer-system-load-status-1, groupId=system-load-status] Adding newly assigned partitions: system.load-0
tech-penguin-inventory-appp-1  | [err] [Default Executor-thread-10] INFO org.apache.kafka.clients.consumer.internals.ConsumerUtils - Setting offset for partition system.load-0 to the committed offset FetchPosition{offset=22, offsetEpoch=Optional[0], currentLeader=LeaderAndEpoch{leader=Optional[kafka:9092 (id: 0 rack: null)], epoch=2}}
tech-penguin-inventory-appp-1  | [INFO    ] Host 554d98843544 was added: CpuLoadAverage: {"hostname":"554d98843544","loadAverage":1.71}
tech-penguin-inventory-appp-1  | [INFO    ] Host 554d98843544 was updated: CpuLoadAverage: {"hostname":"554d98843544","loadAverage":1.33}
```










## Referencias







**Java Kafka**

- https://openliberty.io/guides/microprofile-reactive-messaging.html



**AKHQ**

- https://akhq.io