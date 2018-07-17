---
layout: post
title:  Proyecto con Direct Web Remoting (DWR) con Struts2
date:   2016-02-08 12:00:00 -0000
categories: java, struts2, AJAX
short: Direct Web Remoting (DWR) es una librería que nos permite hacer peticiones HTTP desde Javascript, si hacer peticiones AJAX, de esta forma podemos hacer nuestras aplicaciones más dinamicas dentro de una aplicacón con Struts2

---

**Direct Web Remoting (DWR)** es una librería que nos permite hacer peticiones HTTP desde Javascript, si hacer peticiones **AJAX**, de esta forma podemos hacer nuestras aplicaciones más dinámicas dentro de una aplicación con **Struts2**.

Si bien Struts ya no es una tecnología común a implementar en estos días, existen en las empresas muchos proyectos y por consiguiente es necesario darles mantenimiento como a mi me paso, razón por la cual estoy compartiendo esta información.

Aquí no explicare que es Struts ni cómo funciona, ya existen muchas páginas para eso, aquí solo realizaremos las configuraciones básicas desde el inicio.

## Que necesitamos
* **Java:** con un nivel básico es suficiente.
* **Maven:** hay que tenerlo configurado.

## Creamos el proyecto con Maven

Ya debemos tener instalado Maven, si no es así puedes seguir este Tutorial

Si quieres comprobar que lo tienes instalado correctamente puedes seguir el post Configuración básica de Java Spring

Ejecutamos en la terminal la instrucción siguiente que nos creara la configuración básica de un proyecto.

``` shell
mvn archetype:generate -B \
  -DgroupId=io.saidmlx \
  -DartifactId=dwr-struts-2 \
  -DarchetypeGroupId=org.apache.struts \
  -DarchetypeArtifactId=struts2-archetype-blank \
  -DarchetypeVersion=2.5.5 \
  -DremoteRepositories=http://struts.apache.org
```

## Agregamos Las dependencias dentro del archivo pom.xml

``` xml
<dependency>
	<groupId>commons-logging</groupId>
	<artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>

<dependency>
	<groupId>org.directwebremoting</groupId>
	<artifactId>dwr</artifactId>
	<version>3.0.2-RELEASE</version>
</dependency>
``` 

## El servlet que realizara el trabajo

La primer configuración compleja es crear un **Servlet** que servirá como punto de entrada

así que agregamos el **Servlet** dentro del **web.xml**

``` xml
<servlet>
	<servlet-name>dwr-invoker</servlet-name>
	<servlet-class>org.directwebremoting.servlet.DwrServlet</servlet-class>
	<init-param>
		<param-name>debug</param-name>
		<param-value> true </param-value>
	</init-param>
</servlet>
<servlet-mapping>
	<servlet-name>dwr-invoker</servlet-name>
	<url-pattern> /dwr/* </url-pattern>
</servlet-mapping>
```

## configuramos el DWR

Aquí está el punto central donde declaramos el DWR y lo hacemos creando un archivo **dwr.xml** al nivel del archivo **web.xml**

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE dwr PUBLIC
    "-//GetAhead Limited//DTD Direct Web Remoting 3.0//EN"
    "http://directwebremoting.org/schema/dwr30.dtd">

<dwr>
	<allow>
   		<create creator="new" javascript="Message">
			<param name="class" value="io.saidmlx.POJO.Message"/>
		</create>
	</allow>
</dwr>
```

## Creamos El POJO 

En el paso anterior declaramos un **DWR** y le asignamos una clase **io.saidmlx.POJO.Message**

``` java
package io.saidmlx.POJO;

public class Message {
	public String getMessage() {
		return "Hello DWR from Server";
	}
}
```

## Creamos el Front

Modificamos el archivo **hello.jsp** para que quede de con la siguiente estructura.

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<html>
<head>
<title><s:text name="hello.message" /></title>
<script type='text/javascript' src='/dwrStruts2/dwr/engine.js'></script>
<script type='text/javascript' src='/dwrStruts2/dwr/util.js'></script>
<script type='text/javascript' src='/dwrStruts2/dwr/interface/Message.js'></script>

</head>
<body>
	<h1>Example dwr</h1>
	<input value="click me!!!" type="button" onclick="update();" />
	<div style="background-color: #ffeaa7; font-weight: bold; width: 300px;" id="divResponse">Message From Server</div>
		
	<script type="text/javascript">
		function update() {
			Message.getMessage(function(data) {
				dwr.util.setValue("divResponse", data);
			});
		}
	</script>
	
</body>
</html>
``` 

En el archivo anterior podemos ver la forma en que se está mandando a llamar nuestro AJAX

```javascript
function update() {
	Message.getMessage(function(data) {
		dwr.util.setValue("divResponse", data);
	});
}
```

## Ejecutar la aplicación

Para ejecutar la aplicación solo tenemos que ejecutar en la terminal 
```shell
mvn jetty:run
```

#### Primer pantalla de ejemplo

Al entrar a la url http://localhost:8080/example/HelloWorld.action

<div class="text-center">
  <img src="{{ site.url }}/assets/images/dwr-struts2/dwr-struts2-pantalla-1.png" class="rounded img-thumbnail post-img-center" alt="Direct Web Remoting - pantalla inicial">
</div>

#### Segunda pantalla de ejemplo

Al dar click en el boton se ejecuta una peticion AJAX de tipo POST 

<div class="text-center">
  <img src="{{ site.url }}/assets/images/dwr-struts2/dwr-struts2-pantalla-2.png" class="rounded img-thumbnail post-img-center" alt="Direct Web Remoting - pantalla con peticion post">
</div>

## El código fuente 

Si quieres el codigo fuente solo tienes que hacer un pull request a <a href="https://github.com/saidmlx/dwr-struts-2" > github </a>





