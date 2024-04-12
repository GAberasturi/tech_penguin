# TECH PENGUIN - KATALLER DOCKERS





## Practica 3 - PostgreSQL



El objetivo de la prácticaes arrancar un contenedor de BBDD PostgreSQL e inicializarlo y ver como conectarnos al mismo desde un contenedor PGAdmin para poder gestionarlo.

Para realizar una gestión mas sencilla del entorno se creara un fichero `docker-compose.yml` con el cual poder arrancar tanto la BBDD PostgreSQL como el PGAdmin para poder gestionarla.



> **NOTA:** se ha utilizado como referencia para la practica la guía [Load PostgreSQL Sample Database](https://www.postgresqltutorial.com/postgresql-getting-started/load-postgresql-sample-database/) 



### 01. Revisión del proyecto

La estructura del proyecto debe ser la siguiente

```
\TECH_PENGUIN\PRACTICAS\POSTGRESQL
\---backups
```



El contenido de las carpetas es el siguiente

- **backups**

  Backup de ejemplo.



### 02. Crear un docker-compose P

Se crea un docker-compose-yml para realizar una gestión mas sencilla del entorno y la BBDD postgres

docker-compose.yml

```
#################################################################################
#
# docker-compose para una gestión mas sencilla del entorno y la BBDD postgresql.
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
  
##########
## BBDD ##
##########
  tech-penguin-postgres-pgadmin:
    image: dpage/pgadmin4
    container_name: tech-penguin-postgres-pgadmin-1
    environment:
      PGADMIN_DEFAULT_EMAIL: "tec@penguin.com"
      PGADMIN_DEFAULT_PASSWORD: "tec@penguin.com"
    networks:
      - tech-penguin-net
    ports:
      - 8080:80
```









### 02. Arrancar una BBDD Postgresql docker

Se arranca un contenedor postgresql

```
docker run --name tech-penguin-postgres-1 -e POSTGRES_PASSWORD=Tech.Peguin.Pssword -d postgres
```







### Incidencias Practica 3 - Postgresql

Sección con incidencias que nos podemos encontrar.





### Ejemplos de salidas de comandos






## Referencias



**Postgresql**

- https://hub.docker.com/_/postgres
- 
- https://www.postgresqltutorial.com/postgresql-getting-started/load-postgresql-sample-database/



**PGAdmin**

- https://www.pgadmin.org/download/pgadmin-4-container/
- https://hub.docker.com/r/dpage/pgadmin4/
- https://www.pgadmin.org/docs/pgadmin4/latest/container_deployment.html