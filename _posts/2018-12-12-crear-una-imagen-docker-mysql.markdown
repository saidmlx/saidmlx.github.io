---
layout: post
title: Crear una imagen customizada basada en Mysql
date: 2018-12-12 12:00:00 
categories: Devops
tags: Devops Mysql Docker
---

Las imágenes son la base de Docker, y crear una imagen customizada es el punto de partida para todo desarrollador ya que nos permite compartirlas con el equipo de trabajo o con el mundo entero.

En esta sección vamos a construir una imagen customizada con una base de datos propia en base en Mysql y la subiremos a Docker-Hub.

Imaginemos que ya tenemos una base de datos y la queremos pasar al nuevo integrante del equipo que está desarrollando una API REST con Java, pues lo único que hacemos es indicar el repositorio donde obtenerla vía Docker-Hub, el corre el proceso Docker fácilmente en una línea de código y tiene un ambiente de base de datos, sin tener que tramitar permisos de firewall a un ambiente de desarrollo (como se hacía tradicionalmente) o instalar el ambiente y hacer Dump Database.

## Preparamos los scripts

Para empezar creamos los scripts SQL y los dejamos en una carpeta __scripts__.

Para fines demostrativos solo vamos a crear solo una tabla así que se crea la carpeta __scripts__ y un archivo __create.table.sql__

```terminal
$ mkdir scripts
$ touch create.table.sql

```

```sql
CREATE TABLE `products` (
  `id` int(11) NOT NULL,
  `name` varchar(45) DEFAULT NULL,
  `description` varchar(45) DEFAULT NULL,
  `active` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `id_UNIQUE` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

## Crear el archivo Dockerfile

El archivo __Dockerfile__ es el archivo de configuración principal donde definimos la creación de una imagen.

* __FROM mysql:__ Recordemos que Docker funciona en capas entonces tomamos como base la imagen de __*Mysql*__ 
* __COPY:__ copiamos los scripts generados para que cuando se cargue el proceso estos sean ejecutados en el contenedor

```docker
# Base image
FROM mysql

# Add all scripts 
COPY ./scripts/ /docker-entrypoint-initdb.d/

```

Nuestros recursos están listos con la siguiente estructura:

```terminal
$ tree
├───Dockerfile
└───scripts     
    └───create.table.sql
```

## Crear la imagen 

Dónde:
* __-t saidmlx__: es el tag que le vamos a asignar a nuestra imagen
* __./__: el contexto de intercambio de archivos.
```terminal
$ docker build -t saidmlx ./
Sending build context to Docker daemon  3.584kB
Step 1/2 : FROM mysql
 ---> f991c20cb508
Step 2/2 : COPY ./scripts/ /docker-entrypoint-initdb.d/
 ---> 7c7f96bfc950
Successfully built 7c7f96bfc950
Successfully tagged mysql:saidmlx
```

De esta forma al listar las imágenes disponibles descubrimos que existen dos __mysql__ como repositorio pero con un *tag* diferente __latest__ que es la imagen del repositorio oficial y __saidmlx__ que es la que acabamos de crear, la primera creada hace tres semanas y la segunda creada hace tres minutos.

```terminal
$ docker image ls
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
mysql                      saidmlx             7c7f96bfc950        3 minutes ago       486MB
mysql                      latest              f991c20cb508        3 weeks ago         486MB
```

## Ejecutar un contenedor con una imagen customizada

Ejecutamos el contenedor con los parámetros:

* __-d__: Deatached Mode es la forma en que indicamos que corra en background.
* __-p__: puerto, el contenedor corre en el puerto 3306 pero hacemos un bind para que lo escuchemos en Host el puerto 33061.
* __--name__: para no tener que hacer referencia al hash le asignamos un nombre.
* __-e MYSQL_ROOT_PASSWORD__: variable de entorno donde le asignamos la contraseña al usuario __*root*__.
* __-e MYSQL_DATABASE=demo__: variable de entorno donde le asignamos el nombre de la base de datos que queremos que cree.

```terminal
$ docker run -d -p 3306:3306 --name mysql-saidmlx -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=demo mysql:saidmlx
```
# Verificar el contenido 

Entramos en modo interactivo

Donde:
* __exec__: indicamos que vamos a pasar un comando.
* __-it__ Modo interactivo.
* __mysql -p__: es el comando para entrar a la consola de mysql con el usuario root(si has trabajado con mysql en consola es lo mismo).

```terminal
λ docker exec -it mysql-saidmlx mysql -p                                      
Enter password:                                                               
Welcome to the MySQL monitor.  Commands end with ; or \g.                     
Your MySQL connection id is 9                                                 
Server version: 8.0.13 MySQL Community Server - GPL                           
                                                                              
Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.  
                                                                              
Oracle is a registered trademark of Oracle Corporation and/or its             
affiliates. Other names may be trademarks of their respective                 
owners.                                                                       
                                                                              
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> show databases;                                                        
```

Mostramos las bases de datos creadas y vemos a __demo__ que es valor que le pasamos al proceso en la variable de entorno __MYSQL_DATABASE__

```terminal                                                                              
mysql> show databases;                                                        
+--------------------+                                                        
| Database           |                                                        
+--------------------+                                                        
| demo               |                                                        
| information_schema |                                                        
| mysql              |                                                        
| performance_schema |                                                        
| sys                |                                                        
+--------------------+                                                        
5 rows in set (0.00 sec)                                                      
```

Desplegamos las tablas y vemos la tabla __products__ creada y que se encontraba en la ruta __./scripts/create.table.sql__.

```terminal
mysql> use demo;                                                  
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A    
                                                                  
Database changed                                                  
mysql> show tables;                                               
+----------------+                                                
| Tables_in_demo |                                                
+----------------+                                                
| products       |                                                
+----------------+                                                
1 row in set (0.00 sec)                                           
```

Ya con esto tenemos una imagen que a la hora de ejecutar el proceso Docker precarga la información, para este ejemplo solo es una tabla, ahora imagina si cargas todo un script de base de datos

## Subir nuestra imagen a docker-hub

[Docker-Hub](https://hub.docker.com/) es un Github para contenedores y para subir la imagen que creamos es necesario crear una cuenta.

Una vez creada una cuenta hay que hacer un par de cambios.

Cambiamos el repository apuntando a nuestro repositorio, como mi cuenta es saidmlx, escribimos lo siguiente.
Donde: 
*__mysql:saidmlx__ es la imagen actual
*__saidmlx/mysql:withdatabase__ es el repositorio con el tag que queremos asignar
```terminal
$ docker tag mysql:saidmlx saidmlx/mysql:withdatabase
```

Y al listar las imágenes vemos las tres la original como __mysql:latest__, la imagen que creamos __mysql:saidmlx__ y la que renombramos como __saidmlx/mysql:withdatabase__
```terminal
$ docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
saidmlx/mysql              withdatabase        7c7f96bfc950        27 minutes ago      486MB
mysql                      saidmlx             7c7f96bfc950        27 minutes ago      486MB
mysql                      latest              f991c20cb508        3 weeks ago         486MB
```

Y ahora subimos la imagen

```terminal
λ docker push saidmlx/mysql:withdatabase
The push refers to repository [docker.io/saidmlx/mysql]
48994486ea8f: Pushed
withdatabase: digest: sha256:45a88952d0bf29136031b46608e154ee762c82ab3c37f7fa14e2cdb63fd08cb1 size: 3035
```

y a partir de aquí la imagen esta disponible para el mundo entero.

![docker hub repository ]({{ site.url }}/assets/images/docker/docker-hub.jpg)

## Descargar la imagen creada desde Docker-Hub

Para probar que todo funciona eliminamos todas las imagenes relacionadas con myql

```terminal
$ docker rm -f mysql-saidmlx:latest
$ docker image rmi mysql:saidmlx
$ docker image rmi saidmlx/mysql:withdatabase
```
Lanzamos un proceso Docker pero a la imagen __saidmlx/mysql:withdatabase__ y como no la encuentra en el Host la descarga

```terminal
λ docker run -d -p 3306:3306 --name mysql-saidmlx -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=demo saidmlx/mysql:withdatabase 
Unable to find image 'saidmlx/mysql:withdatabase' locally
withdatabase: Pulling from saidmlx/mysql
a5a6f2f73cd8: Pull complete
...
31c54ab0e6e7: Pull complete
Digest: sha256:45a88952d0bf29136031b46608e154ee762c82ab3c37f7fa14e2cdb63fd08cb1
Status: Downloaded newer image for saidmlx/mysql:withdatabase
ad1cb6c628e13a4256567bc2a39c3ad262c9b1e580899927842f7beae5d74986
```

Vemos el proceso ejecutándose
````terminal
$ docker ps
CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS              PORTS                               NAMES
ad1cb6c628e1        saidmlx/mysql:withdatabase   "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   mysql-saidmlx
```

Entramos al contenedor en modo interactivo y podemos ver la base de datos y la tabla creada

```terminal
λ docker exec -it mysql-saidmlx mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.13 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| demo               |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> use demo;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+----------------+
| Tables_in_demo |
+----------------+
| products       |
+----------------+
1 row in set (0.00 sec)
```

Y esta es la forma en que creamos una imagen customizada la subimos a Docker-Hub para que cuando necesitemos podamos descargarla.