---
layout: post
title: Instalar MySQL en un entorno servidor
date: 2000-01-01 12:00:00 
categories: SQL MySQL 
tags: SQL MySQL Server Terminal
---


En este post es una guía práctica de como instalar MySQL en un entorno de servidor.

## Descargar MySQL

Hay que dirigirnos a la [pagina oficial](https://dev.mysql.com/downloads/) y descargar la versión requerida.

Para este caso estaremos instalando la version __MySQL Community Server mysql-5.7__ asegúrate de bajar la versión ZIP

## Configuración inicial

Inicializamos el servicio de base de datos que creara una contraseña temporal para root.

```terminal
$ mysqld --initialize --console
...
2018-12-12T22:40:31.809023Z 1 [Note] A temporary password is generated for root@localhost: Gh+fCo_/b8.5
```

Iniciamos el servicio.

```terminal
$ mysqld --console
```

Entramos con la contraseña temporal.

```bash
$ mysql -u root -p
Enter password: ************
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 6
Server version: 5.7.20

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Reseteamos la contraseña para que nos permita hacer operaciones.
Una vez la contraseña ha sido cambiada hay que salirnos de la sesión y hay que volver a entrar.
Terminando este proceso nuestro servidor ya está listo para trabajar


```sql
ALTER USER 'root'@'localhost' IDENTIFIED  BY 'secret';
Query OK, 0 rows affected (0.00 sec)

mysql> quit
```

## Crear usuarios

Creamos un usuario
```sql
CREATE USER 'enero'@'localhost' IDENTIFIED  BY 'enero';
```

Ver usuarios creados.
```sql
mysql> SELECT user FROM mysql.user;
+------------------+
| user             |
+------------------+
| diciembre        |
| mysql.infoschema |
| mysql.session    |
| mysql.sys        |
| root             |
+------------------+
```

## Crear una base de datos

```bash
...

mysql> create database myDatabaseDemo;
Query OK, 1 row affected (0.01 sec)
```
Ver bases de datos
```mysql

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mydatabasedemo     |
| mysql              |
| performance_schema |
| platziprofesores   |
| sys                |
+--------------------+
6 rows in set (0.03 sec)


```

## Respaldo una base de datos

Cargar un respaldo de una base de datos

Desde la terminal( fuera de la interfaz de mysql)

```terminal
mysql -u root -p myDatabaseDemo  platziprofesores.dump
Enter password: 
 ```


Hacer el respaldo de una base de datos

```bash
mysql -u root -p myDatabaseDemo  platziprofesores.dump

```



