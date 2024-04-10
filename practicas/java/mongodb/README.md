# TECH PENGUIN - KATALLER DOCKERS





## Practica 1 - Mongo



El objetivo de la practica es conectar desde una aplicación java arrancada en un servidor de aplicaciones java Open Liberty a una BBDD mongo.

Para realizar una gestión mas sencilla del entorno se creara un fichero `docker-compose.yml` con el cual poder arrancar tanto la BBDD mongo con la aplicación en modo "desarrollador" e ir modificándola para ver como funciona.



> **NOTA:** se ha utilizado como referencia para la practica la guía de open liberty [mongodb-intro.html](https://openliberty.io/guides/mongodb-intro.html) 



### 01. Revisión del proyecto

La estructura del proyecto debe ser la siguiente

```
TECH_PENGUIN\PRACTICAS\JAVA\MONGODB
+---assets
+---finish
|   \---src
|       +---main
|       |   +---java
|       |   |   \---io
|       |   |       \---openliberty
|       |   |           \---guides
|       |   |               +---application
|       |   |               \---mongo
|       |   +---liberty
|       |   |   \---config
|       |   |       \---resources
|       |   |           \---security
|       |   \---webapp
|       |       +---css
|       |       +---fonts
|       |       +---img
|       |       +---js
|       |       +---META-INF
|       |       \---WEB-INF
|       \---test
|           \---java
|               \---it
|                   \---io
|                       \---openliberty
|                           \---guides
|                               \---application
+---scripts
\---start
    \---src
        +---main
        |   +---java
        |   |   \---io
        |   |       \---openliberty
        |   |           \---guides
        |   |               +---application
        |   |               \---mongo
        |   +---liberty
        |   |   \---config
        |   |       \---resources
        |   |           \---security
        |   \---webapp
        |       +---css
        |       +---fonts
        |       +---img
        |       +---js
        |       +---META-INF
        |       \---WEB-INF
        \---test
            \---java
                \---it
                    \---io
                        \---openliberty
                            \---guides
                                \---application

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



### 02. Preparar la imagen de mongo

Lo primero que se debe hacer es preparar la imagen de mongo. Para ello se debe tener docker arrancado en el equipo local.



Desde un terminal hay que posicionarse en el directorio `practicas\java\mongodb\`



Mediante los comando `docker ps` o `docker images` se puede comprobar si el daemon esta ejecutándose. De ser así no mostrara los contenedores ejecutándose y las imágenes de contenedores disponibles en el sistema respectivamente. Ejemplo en [Ejemplos de salidas de comandos - contenedores en ejecucion](#contenedores-en-ejecución).





Se ejecutan los siguientes comandos, el primero para compilar la imagen y el segundo para arrancar el contenedor.

> **NOTA:** La primera vez tardara porque debe descargarse las imágenes base.

```
docker build -t tech-penguin-mongodb -f assets/Dockerfile .
docker run --name tech-penguin-mongodb-1 -p 27017:27017 -d tech-penguin-mongodb
```

Ejemplos en 

[Ejemplos de salidas de comandos - Compilar imagen mongo](#compilar-imagen-mongo)

[Ejemplos de salidas de comandos - Imágenes de contenedores después de compilar](#imágenes-de-contenedores-después-de-compilar).

[Ejemplos de salidas de comandos -Contenedor de mongo ejecutándose](#contenedor-de-mongo-ejecutándose).



Revisamos el log de ejecución del mongo para ello se entra dentro del contenedor arrancado.

```
docker exec -ti tech-penguin-mongodb-1 bash
```

```
\tech_penguin\java\mongoDB\guide-mongodb-intro\tmp_libs>docker exec -ti tech-penguin-mongodb-1 bash
root@c23769513e8b:/#
```



Una vez dentro se revisa el comienzo del fichero de log

```
head -50 /home/mongodb/logs/mongodb.log
```

[Ejemplos de salidas de comandos -Log de arranque de mongo](#log-de-arranque-de-mongo).



Se debe obtener el certificado TLS generado (`truststore.p12`) para poder conectarse desde la aplicación.

Desde un terminal hay que posicionarse en el directorio `practicas\java\mongodb\`

```
docker cp tech-penguin-mongodb-1:/home/mongodb/certs/truststore.p12 start/src/main/liberty/config/resources/security
docker cp tech-penguin-mongodb-1:/home/mongodb/certs/truststore.p12 finish/src/main/liberty/config/resources/security
docker cp tech-penguin-mongodb-1:/home/mongodb/certs/truststore.p12 certs/
```

[Ejemplos de salidas de comandos -Extracción de certificado TLS](#extracción-de-certificado-tls).





### Incidencias Practica 1 - Mongo

Sección con incidencias que nos podemos encontrar.





### Ejemplos de salidas de comandos



#### Contenedores en ejecución

```
\tech_penguin\practicas\java\mongodb>docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

\tech_penguin\practicas\java\mongodb>
```



#### Compilar imagen mongo

```
\tech_penguin\practicas\java\mongodb>docker build -t tech-penguin-mongodb -f assets/Dockerfile .
[+] Building 4.1s (20/20) FINISHED                                                                                                                                                                                          docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                                                  0.1s
 => => transferring dockerfile: 1.90kB                                                                                                                                                                                                0.0s
 => [internal] load metadata for docker.io/library/mongo:latest                                                                                                                                                                       1.3s
 => [internal] load metadata for icr.io/appcafe/ibm-semeru-runtimes:open-11-jdk-ubi                                                                                                                                                   0.8s
 => [internal] load .dockerignore                                                                                                                                                                                                     0.3s
 => => transferring context: 2B                                                                                                                                                                                                       0.0s
 => [stage-1  1/10] FROM docker.io/library/mongo:latest@sha256:5505a38a8c6044ace154376dfe4c94ac85f01c22585547efdd9483c4922dfa37                                                                                                       0.0s
 => [internal] load build context                                                                                                                                                                                                     0.1s
 => => transferring context: 675B                                                                                                                                                                                                     0.0s
 => [staging 1/4] FROM icr.io/appcafe/ibm-semeru-runtimes:open-11-jdk-ubi@sha256:6d3e1e34bd57ae000fa905918284055ba0fb2fdd41cf2ec4468ee388aa8c5725                                                                                     0.0s
 => CACHED [stage-1  2/10] RUN mkdir /home/mongodb                                                                                                                                                                                    0.0s
 => CACHED [stage-1  3/10] RUN mkdir /home/mongodb/data                                                                                                                                                                               0.0s
 => CACHED [stage-1  4/10] RUN mkdir /home/mongodb/certs                                                                                                                                                                              0.0s
 => CACHED [stage-1  5/10] RUN mkdir /home/mongodb/logs                                                                                                                                                                               0.0s
 => CACHED [stage-1  6/10] RUN chown -R mongodb:mongodb /home/mongodb                                                                                                                                                                 0.0s
 => CACHED [staging 2/4] RUN openssl req -x509 -newkey rsa:2048 -nodes -days 365     -out /tmp/cert.pem     -keyout /tmp/private.key     -subj "/C=CA/ST=Ontario/L=Markham/O=IBM/OU=OpenLiberty/CN=localhost"                         0.0s
 => CACHED [staging 3/4] RUN cat /tmp/private.key /tmp/cert.pem > /tmp/mongodb_tls.pem                                                                                                                                                0.0s
 => CACHED [staging 4/4] RUN keytool -import -trustcacerts -keystore /tmp/truststore.p12     -storepass mongodb -storetype PKCS12 -alias mongo -file /tmp/cert.pem -noprompt                                                          0.0s
 => CACHED [stage-1  7/10] COPY --from=staging /tmp /home/mongodb/certs                                                                                                                                                               0.0s
 => CACHED [stage-1  8/10] COPY assets/mongodb.conf /home/mongodb                                                                                                                                                                     0.0s
 => CACHED [stage-1  9/10] COPY assets/index.js /home/mongodb                                                                                                                                                                         0.0s
 => CACHED [stage-1 10/10] RUN mongod         --fork         --config /home/mongodb/mongodb.conf     && mongosh         testdb         -tls         --tlsCAFile /home/mongodb/certs/cert.pem         --tlsCertificateKeyFile: /home/  0.0s
 => exporting to image                                                                                                                                                                                                                0.0s
 => => exporting layers                                                                                                                                                                                                               0.0s
 => => writing image sha256:530b0042757040a4aa0a29981367330cb9fcf7d6334edd979d2ac20c3705ee6d                                                                                                                                          0.0s
 => => naming to docker.io/library/tech-penguin-mongodb                                                                                                                                                                               0.0s

View build details: docker-desktop://dashboard/build/default/default/1k5qp3e1eissqlhrslmgqrx43

What's Next?
  View a summary of image vulnerabilities and recommendations → docker scout quickview

\tech_penguin\practicas\java\mongodb>
```



#### Imágenes de contenedores después de compilar

```
\tech_penguin\practicas\java\mongodb>docker images
REPOSITORY                                TAG                         IMAGE ID       CREATED         SIZE
tech-penguin-mongodb                      latest                      530b00427570   32 hours ago    1.09GB
docker/desktop-vpnkit-controller          v2.0                        8c2c38aa676e   2 years ago     21MB
docker/desktop-storage-provisioner        v2.0                        99f89471f470   2 years ago     41.9MB
eclipse/che-ip                            latest                      ba0c46d33f01   5 years ago     4.82MB

\tech_penguin\practicas\java\mongodb>
```



#### Contenedor de mongo ejecutándose

```  
\tech_penguin\practicas\java\mongodb>docker run --name tech-penguin-mongodb-1 -p 27017:27017 tech-penguin-mongodb
{"t":{"$date":"2024-04-07T19:52:32.990Z"},"s":"I",  "c":"CONTROL",  "id":20697,   "ctx":"main","msg":"Renamed existing log file","attr":{"oldLogPath":"/home/mongodb/logs/mongodb.log","newLogPath":"/home/mongodb/logs/mongodb.log.2024-04-07T19-52-32"}}
```



#### Log de arranque de mongo

```
root@c23769513e8b:/# head -100 /home/mongodb/logs/mongodb.log
{"t":{"$date":"2024-04-07T19:52:32.991+00:00"},"s":"I",  "c":"NETWORK",  "id":4915701, "ctx":"main","msg":"Initialized wire specification","attr":{"spec":{"incomingExternalClient":{"minWireVersion":0,"maxWireVersion":21},"incomingInternalClient":{"minWireVersion":0,"maxWireVersion":21},"outgoing":{"minWireVersion":6,"maxWireVersion":21},"isInternalClient":true}}}
{"t":{"$date":"2024-04-07T19:52:32.997+00:00"},"s":"W",  "c":"NETWORK",  "id":551190,  "ctx":"main","msg":"Server certificate has no compatible Subject Alternative Name. This may prevent TLS clients from connecting","tags":["startupWarnings"]}
{"t":{"$date":"2024-04-07T19:52:32.998+00:00"},"s":"I",  "c":"NETWORK",  "id":4913010, "ctx":"main","msg":"Certificate information","attr":{"subject":"CN=localhost,OU=OpenLiberty,O=IBM,L=Markham,ST=Ontario,C=CA","issuer":"CN=localhost,OU=OpenLiberty,O=IBM,L=Markham,ST=Ontario,C=CA","thumbprint":"70752ED825B4953FEBFEB5DB60355A0C2BB8F835","notValidBefore":{"$date":"2024-04-06T11:21:47.000Z"},"notValidAfter":{"$date":"2025-04-06T11:21:47.000Z"},"keyFile":"/home/mongodb/certs/mongodb_tls.pem","type":"Server"}}
{"t":{"$date":"2024-04-07T19:52:32.998+00:00"},"s":"I",  "c":"CONTROL",  "id":23285,   "ctx":"main","msg":"Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'"}
{"t":{"$date":"2024-04-07T19:52:33.000+00:00"},"s":"I",  "c":"NETWORK",  "id":4648601, "ctx":"main","msg":"Implicit TCP FastOpen unavailable. If TCP FastOpen is required, set tcpFastOpenServer, tcpFastOpenClient, and tcpFastOpenQueueSize."}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"REPL",     "id":5123008, "ctx":"main","msg":"Successfully registered PrimaryOnlyService","attr":{"service":"TenantMigrationDonorService","namespace":"config.tenantMigrationDonors"}}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"REPL",     "id":5123008, "ctx":"main","msg":"Successfully registered PrimaryOnlyService","attr":{"service":"TenantMigrationRecipientService","namespace":"config.tenantMigrationRecipients"}}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"CONTROL",  "id":5945603, "ctx":"main","msg":"Multi threading initialized"}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"TENANT_M", "id":7091600, "ctx":"main","msg":"Starting TenantMigrationAccessBlockerRegistry"}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"CONTROL",  "id":4615611, "ctx":"initandlisten","msg":"MongoDB starting","attr":{"pid":1,"port":27017,"dbPath":"/home/mongodb/data","architecture":"64-bit","host":"c23769513e8b"}}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"CONTROL",  "id":23403,   "ctx":"initandlisten","msg":"Build Info","attr":{"buildInfo":{"version":"7.0.8","gitVersion":"c5d33e55ba38d98e2f48765ec4e55338d67a4a64","openSSLVersion":"OpenSSL 3.0.2 15 Mar 2022","modules":[],"allocator":"tcmalloc","environment":{"distmod":"ubuntu2204","distarch":"x86_64","target_arch":"x86_64"}}}}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"CONTROL",  "id":51765,   "ctx":"initandlisten","msg":"Operating System","attr":{"os":{"name":"Ubuntu","version":"22.04"}}}
{"t":{"$date":"2024-04-07T19:52:33.008+00:00"},"s":"I",  "c":"CONTROL",  "id":21951,   "ctx":"initandlisten","msg":"Options set by command line","attr":{"options":{"config":"/home/mongodb/mongodb.conf","net":{"bindIp":"*","tls":{"CAFile":"/home/mongodb/certs/cert.pem","allowConnectionsWithoutCertificates":true,"certificateKeyFile":"/home/mongodb/certs/mongodb_tls.pem","mode":"requireTLS"}},"storage":{"dbPath":"/home/mongodb/data"},"systemLog":{"destination":"file","path":"/home/mongodb/logs/mongodb.log"}}}}
{"t":{"$date":"2024-04-07T19:52:33.014+00:00"},"s":"I",  "c":"STORAGE",  "id":22270,   "ctx":"initandlisten","msg":"Storage engine to use detected by data files","attr":{"dbpath":"/home/mongodb/data","storageEngine":"wiredTiger"}}
{"t":{"$date":"2024-04-07T19:52:33.014+00:00"},"s":"I",  "c":"STORAGE",  "id":22315,   "ctx":"initandlisten","msg":"Opening WiredTiger","attr":{"config":"create,cache_size=7450M,session_max=33000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,remove=true,path=journal,compressor=snappy),builtin_extension_config=(zstd=(compression_level=6)),file_manager=(close_idle_time=600,close_scan_interval=10,close_handle_minimum=2000),statistics_log=(wait=0),json_output=(error,message),verbose=[recovery_progress:1,checkpoint_progress:1,compact_progress:1,backup:0,checkpoint:0,compact:0,evict:0,history_store:0,recovery:0,rts:0,salvage:0,tiered:0,timestamp:0,transaction:0,verify:0,log:0],"}}
{"t":{"$date":"2024-04-07T19:52:33.869+00:00"},"s":"I",  "c":"STORAGE",  "id":4795906, "ctx":"initandlisten","msg":"WiredTiger opened","attr":{"durationMillis":855}}
{"t":{"$date":"2024-04-07T19:52:33.869+00:00"},"s":"I",  "c":"RECOVERY", "id":23987,   "ctx":"initandlisten","msg":"WiredTiger recoveryTimestamp","attr":{"recoveryTimestamp":{"$timestamp":{"t":0,"i":0}}}}
{"t":{"$date":"2024-04-07T19:52:33.888+00:00"},"s":"W",  "c":"CONTROL",  "id":22120,   "ctx":"initandlisten","msg":"Access control is not enabled for the database. Read and write access to data and configuration is unrestricted","tags":["startupWarnings"]}
{"t":{"$date":"2024-04-07T19:52:33.888+00:00"},"s":"W",  "c":"CONTROL",  "id":22178,   "ctx":"initandlisten","msg":"/sys/kernel/mm/transparent_hugepage/enabled is 'always'. We suggest setting it to 'never' in this binary version","tags":["startupWarnings"]}
{"t":{"$date":"2024-04-07T19:52:33.888+00:00"},"s":"W",  "c":"CONTROL",  "id":5123300, "ctx":"initandlisten","msg":"vm.max_map_count is too low","attr":{"currentValue":262144,"recommendedMinimum":1677720,"maxConns":838860},"tags":["startupWarnings"]}
{"t":{"$date":"2024-04-07T19:52:33.898+00:00"},"s":"I",  "c":"NETWORK",  "id":4915702, "ctx":"initandlisten","msg":"Updated wire specification","attr":{"oldSpec":{"incomingExternalClient":{"minWireVersion":0,"maxWireVersion":21},"incomingInternalClient":{"minWireVersion":0,"maxWireVersion":21},"outgoing":{"minWireVersion":6,"maxWireVersion":21},"isInternalClient":true},"newSpec":{"incomingExternalClient":{"minWireVersion":0,"maxWireVersion":21},"incomingInternalClient":{"minWireVersion":21,"maxWireVersion":21},"outgoing":{"minWireVersion":21,"maxWireVersion":21},"isInternalClient":true}}}
{"t":{"$date":"2024-04-07T19:52:33.898+00:00"},"s":"I",  "c":"REPL",     "id":5853300, "ctx":"initandlisten","msg":"current featureCompatibilityVersion value","attr":{"featureCompatibilityVersion":"7.0","context":"startup"}}
{"t":{"$date":"2024-04-07T19:52:33.898+00:00"},"s":"I",  "c":"STORAGE",  "id":5071100, "ctx":"initandlisten","msg":"Clearing temp directory"}
{"t":{"$date":"2024-04-07T19:52:33.901+00:00"},"s":"I",  "c":"CONTROL",  "id":6608200, "ctx":"initandlisten","msg":"Initializing cluster server parameters from disk"}
{"t":{"$date":"2024-04-07T19:52:33.901+00:00"},"s":"I",  "c":"CONTROL",  "id":20536,   "ctx":"initandlisten","msg":"Flow Control is enabled on this deployment"}
{"t":{"$date":"2024-04-07T19:52:33.901+00:00"},"s":"I",  "c":"FTDC",     "id":20625,   "ctx":"initandlisten","msg":"Initializing full-time diagnostic data capture","attr":{"dataDirectory":"/home/mongodb/data/diagnostic.data"}}
{"t":{"$date":"2024-04-07T19:52:33.911+00:00"},"s":"I",  "c":"REPL",     "id":6015317, "ctx":"initandlisten","msg":"Setting new configuration state","attr":{"newState":"ConfigReplicationDisabled","oldState":"ConfigPreStart"}}
{"t":{"$date":"2024-04-07T19:52:33.911+00:00"},"s":"I",  "c":"STORAGE",  "id":22262,   "ctx":"initandlisten","msg":"Timestamp monitor starting"}
{"t":{"$date":"2024-04-07T19:52:33.913+00:00"},"s":"I",  "c":"NETWORK",  "id":23015,   "ctx":"listener","msg":"Listening on","attr":{"address":"/tmp/mongodb-27017.sock"}}
{"t":{"$date":"2024-04-07T19:52:33.913+00:00"},"s":"I",  "c":"NETWORK",  "id":23015,   "ctx":"listener","msg":"Listening on","attr":{"address":"0.0.0.0"}}
{"t":{"$date":"2024-04-07T19:52:33.913+00:00"},"s":"I",  "c":"NETWORK",  "id":23016,   "ctx":"listener","msg":"Waiting for connections","attr":{"port":27017,"ssl":"on"}}
{"t":{"$date":"2024-04-07T19:52:33.913+00:00"},"s":"I",  "c":"CONTROL",  "id":8423403, "ctx":"initandlisten","msg":"mongod startup complete","attr":{"Summary of time elapsed":{"Startup from clean shutdown?":true,"Statistics":{"Transport layer setup":"0 ms","Run initial syncer crash recovery":"0 ms","Create storage engine lock file in the data directory":"0 ms","Get metadata describing storage engine":"0 ms","Validate options in metadata against current startup options":"0 ms","Create storage engine":"866 ms","Write current PID to file":"0 ms","Initialize FCV before rebuilding indexes":"10 ms","Drop abandoned idents and get back indexes that need to be rebuilt or builds that need to be restarted":"0 ms","Rebuild indexes for collections":"0 ms","Load cluster parameters from disk for a standalone":"0 ms","Build user and roles graph":"0 ms","Verify indexes for admin.system.users collection":"0 ms","Set up the background thread pool responsible for waiting for opTimes to be majority committed":"0 ms","Initialize information needed to make a mongod instance shard aware":"0 ms","Start up the replication coordinator":"1 ms","Start transport layer":"0 ms","_initAndListen total elapsed time":"905 ms"}}}}
```



#### Extracción de certificado TLS

```
\tech_penguin\practicas\java\mongodb>docker cp tech-penguin-mongodb-1:/home/mongodb/certs/truststore.p12 certs/
Successfully copied 3.07kB to \tech_penguin\practicas\java\mongodb\certs\

\tech_penguin\practicas\java\mongodb>
```






## Referencias



**Java Mongo**

- https://openliberty.io/guides/liberty-deep-dive.html
- https://openliberty.io/docs/latest/relational-database-connections-JDBC.html
- https://openliberty.io/guides/mongodb-intro.html
- https://openliberty.io/guides/



**Mongo compass**

- https://downloads.mongodb.com/compass/mongodb-compass-1.42.3-win32-x64.exe

  

**Mongo Express**

- https://hub.docker.com/_/mongo-express


