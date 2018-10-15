---
layout: post
title: Modificar un war y volver a empaquetarlo
date: 2018-10-15 12:00:00 
categories: Java 
tags: Java
---

En ocaciones es necesario modificar algo en un archivo WAR y sin tener disponible todo el ambiente, por ejemplo alguna configuración.

Como en mi caso el dia de hoy necesite cambiar el nombre del JNDI.

Para esto es necesario descomprimir el archivo war ¿pero como volvemos a empaquetarlo?

Es muy simple solo hay que ejecutar la siguiente linea de codigo y listo tendremos un archivo WAR sin 
```terminal
java jar cf nuevo_nombre.war carpeta_donde_se_encuentra_la_informacion
```

y listo tendremos un nuevo war con los cambios hechos.
