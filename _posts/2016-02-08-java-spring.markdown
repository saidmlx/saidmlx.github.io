---
layout: post
title:  Configuración básica de Java Spring
date:   2016-02-08 12:00:00 -0000
categories: Java JavaSpring
tags: Java JavaSpring Maven Jetty
---

**Java** es una de los lenguajes más utilizados y **Java Spring** es una de las herramientas
mas utilizadas para desarrollar aplicaciones web, en esta publicación vamos a construir
una aplicación con **Java Spring MVC**.

### Que necesitamos
* **Java:** con un nivel básico es suficiente.
* **Maven:** hay que tenerlo configurado.

### Verificar Maven
Verificamos que tengamos instalado Maven correctamente para eso dentro de la terminal ejecutamos lo siguiente.
```
  mvn -v
```

Si la instalación es correcta veremos la versión que tengamos instalada
```
  Apache Maven 3.3.3
```


### Crear el proyecto vacío
Para crear un proyecto vacío ejecutamos lo siguiente en la terminal.
```
mvn archetype:generate
-DarchetypeArtifactId=maven-archetype-webapp
-DgroupId=io.saidmlx
-DartifactId=SpringWebMvc
-DinteractiveMode=false
```

Este comando construirá la siguiente estructura.

```
├── SpringWebMvc
│   ├── pom.xml
│   └── src
│       └── main
│           ├── resources
│           └── webapp
│               ├── WEB-INF
│               │   └── web.xml
│               └── index.jsp
```


Lo más importante de esta estructura es el archivo **pom.xml** donde se encuentran las
dependencias de nuestro proyecto.

Nuestro archivo pom.xml al iniciar se encuentra de esta forma.
```xml

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>io.saidmlx</groupId>
  <artifactId>SpringWebMvc</artifactId>
  <packaging>war</packaging>
  <version>1.0</version>
  <name>SpringWebMvc Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>SpringWebMvc</finalName>
  </build>
</project>

```

### Configurar Jetty como contenedor web
Vamos a configurar el contenedor **Jetty**, este se comporta al igual que un Tomcat o un Jboss pero tiene funcionalidades como que puede lanzar cambios en caliente es más rápido, más liviano
y no se tiene que configurar nada solo hay que agregarlo como un Plugin.
 

```xml
<build>
  <finalName>SpringWebMvc</finalName>
  <plugins>
    <plugin>
      <groupId>org.eclipse.jetty</groupId>
      <artifactId>jetty-maven-plugin</artifactId>
      <version>9.3.7.v20160115</version>
    </plugin>
  </plugins>
</build>
```

Ya agregado este Plugin podemos lanzar nuestro proyecto

```terminal
  mvn jetty:run
```

Vamos al navegador y tenemos el siguiente resultado


<div class="text-center">
  <img src="{{ site.url }}/assets/images/javaSpring/1.png" class="rounded img-thumbnail post-img-center" alt="Spring MVC configuración basica">
</div>


### Agregar dependencias Java Spring
Agregamos al archivo **pom.xml** las dependencias que necesitamos:

* Spring Core
* Spring MVC

``` xml 
<dependencies>
	<!-- Spring  dependences-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>4.2.2.RELEASE</version>
    </dependency>
  </dependencies>
```

### Creamos el Controller
Creamos el Controller `io.saidmlx.controller.Hello`

```java
package io.saidmlx.controller;

import org.springframework.ui.ModelMap;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.PathVariable;

@Controller
public class Hello{
  @RequestMapping(value= "/", method = RequestMethod.GET )
  public String wellcome(){
    return "wellcome";
  }
  @RequestMapping(value= "/wellcome/{name}", method = RequestMethod.GET )
  public ModelAndView sayWellcome(@PathVariable( value = "name") String name ){
    System.out.println(name);
    ModelAndView model = new ModelAndView();
    model.setViewName("sayWellcome");
    model.addObject("name",name);
    return model;
  }
}
```

### Agregamos la configuración al archivo `web.xml` 

```xml 
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
	http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
	version="2.5">
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>hello-spring</servlet-name>
    <servlet-class>
      org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
		<servlet-name>hello-spring</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

</web-app>

```

### Agregamos View Resolver
Creamos la configuración de View Resolver en el archivo `hello-spring-servlet.xml`

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.2.xsd">
  <!-- Scan Controllers -->
  <context:component-scan base-package="io.saidmlx.controller" />
  <!-- Define  View Resolver-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  	<property name="prefix">
  		<value>/WEB-INF/views/</value>
  	</property>
  	<property name="suffix">
  		<value>.jsp</value>
  	</property>
  </bean>

  <mvc:annotation-driven/>
</beans>

```

### Creamos los jsp que resolverán la vista

`Wellcome.jsp`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
    <h1>Wellcome</h1>
  </body>
</html>

``` 

`sayWellcome.jsp`

```html
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
    <h1> Wellcome <strong>${name}</strong> to Spring Web MVC</h1>

  </body>
</html>
```

Con lo anterior tenemos nuestro ejemplo listo

La estructura de archivos quedaría de la siguiente forma

 ```terminal
 ├── SpringWebMvc
 │   ├── pom.xml
 │   ├── src
 │   │   └── main
 │   │       ├── java
 │   │       │   └── io
 │   │       │       └── saidmlx
 │   │       │           └── controller
 │   │       │               └── Hello.java
 │   │       ├── resources
 │   │       └── webapp
 │   │           └── WEB-INF
 │   │               ├── hello-spring-servlet.xml
 │   │               ├── views
 │   │               │   ├── sayWellcome.jsp
 │   │               │   └── wellcome.jsp
 │   │               └── web.xml
 ```


Y podemos observar en el explorador dos peticiones
### El resultado

#### http://localhost:8080/

<div class="text-center">
  <img src="{{ site.url }}/assets/images/javaSpring/2.png" class="rounded img-thumbnail post-img-center" alt="Spring MVC configuración basica">
</div>

#### http://localhost:8080/wellcome/saidmlx

<div class="text-center">
  <img src="{{ site.url }}/assets/images/javaSpring/3.png" class="rounded img-thumbnail post-img-center" alt="Spring MVC configuración basica">
</div>

### El código fuente  
El código fuente lo puedes encontrar en mi repositorio <a href="https://github.com/saidmlx/SpringWebMvc" > github </a>
