---
layout: post
title:  Instalar Postgresql utilizando brew
date: 2018-01-02 12:00:00 
categories: sql postgresql
tags: Postgresql SQL Brew 
short: Como instalar postgres en OSX con brew
---

Si vas utilizar una base de datos relacional [Postgresql](https://www.postgresql.org)   en una buena opción y en esta ocasión vamos a instalarlo utilizando [**Homebrew**](https://brew.sh).

Como breviario cultural [**homebrew**](https://brew.sh) es el manejador de paquetes para OSX.

## Instalamos Postgresql 

1. Como primer paso hay que actualizar brew
```terminal
$ brew update
$ brew doctor
```

2. Intalamos [Postgresql](https://www.postgresql.org)
```terminal
$ brew install postgresql
```
Listo ya tenemos instalado PostgreSql, y lo verificamos de la siguiente forma.
```terminal
$ postgres --version
postgres (PostgreSQL) 10.1
```

3. Lanzamos el servicio
```terminal
$ brew services start postgresql
```

## Trabajando con PSQL

PSQL es una terminal interactiva, a partir de aquí vamos hacer algunas operaciones básicas

1. Creamos una base de datos
```terminal
$ psql postgres
psql (10.1)
Type "help" for help.
postgres=# CREATE ROLE userdemo WITH LOGIN PASSWORD 'userdemo';
postgres=# CREATE DATABASE mydb;
postgres=# GRANT ALL PRIVILLEGES ON DATABASE mydb TO userdemo
```
Si no reconoce el comando puedes utilizar la opción siguiente
```terminal 
$ /usr/local/Cellar/postgresql/10.1/bin/createdb mydb
```

2. Creamos una tabla
```terminal 
$ psql mydb
mydb=# create table tasks(
mydb(# id int,
mydb(# description varchar(50),
mydb(# status int,
mydb(# active boolean);
```

3. Insertamos datos a la tabla creada
```terminal
mydb=# insert into tasks values (1, 'make a coffe', 2,true);
INSERT 0 1
```

4. Consultamos datos 
```terminal
mydb=# select * from tasks;
 id | description  | status | active 
----+--------------+--------+--------
  1 | make a coffe |      2 | t
(1 row)
```

## Logs

Para poder ver los logs 
```terminal 
$ tail -f /usr/local/var/log/postgres.log
```

## Comandos básicos

1. Mostrar la tablas **\dt**
```terminal
mydb=# \dt
             List of relations
 Schema | Name  | Type  |      Owner       
--------+-------+-------+------------------
 public | tasks | table | saidmoraleslemus
(1 row)
```
2. Mostrar la estructura de una tabla  **\d tasks**
```terminal
mydb=# \d tasks
                         Table "public.tasks"
   Column    |         Type          | Collation | Nullable | Default 
-------------+-----------------------+-----------+----------+---------
 id          | integer               |           |          | 
 description | character varying(50) |           |          | 
 status      | integer               |           |          | 
 active      | boolean               |           |          | 
```

## Modifcar la configuración
Por default todas las entradas son confiables es decir si entramos a la base de datos no nos pide la contraseña

Si queremos que siempre nos pida la contrasela tenemos que modificar el archivo **pg_hba.conf** y se debe cambiar el metodo **trust** por **password**

```terminal
$ cat /usr/local/var/postgres/pg_hba.conf

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust
```

Quedando de la siguiente forma

```terminal
$ cat /usr/local/var/postgres/pg_hba.conf

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     password
# IPv4 local connections:
host    all             all             127.0.0.1/32            password
# IPv6 local connections:
host    all             all             ::1/128                 password
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     password
host    replication     all             127.0.0.1/32            password
host    replication     all             ::1/128                 password
```

Reiniciamos el server

```terminal
$ brew services restart postgres
```

Y listo cuando tratamos de entrar nos pide la contraseña
```terminal
Password for user userdemo:  

mydb=>
```
