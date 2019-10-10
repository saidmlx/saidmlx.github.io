---
layout: post
title: Instalar Java en OSX
date: 2019-08-20 12:00:00 
categories: Java 
tags: Java OSX
---
descargamos las versiones que necesitamos

8

12
https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html

Verificamos la version por default que seria la ultima 

```
$java -version
java version "12.0.2" 2019-07-16
Java(TM) SE Runtime Environment (build 12.0.2+10)
Java HotSpot(TM) 64-Bit Server VM (build 12.0.2+10, mixed mode, sharing)
```

Verificamos las versiones que se instalaron

```
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (2):
    12.0.2, x86_64:	"Java SE 12.0.2"	/Library/Java/JavaVirtualMachines/jdk-12.0.2.jdk/Contents/Home
    1.8.0_221, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home
```

Cambiamos la version default
```
export JAVA_HOME=`/usr/libexec/java_home -v 1.8`
```
Verificamos, pero esto solo esta presente en la sesión actual de la terminal cuando cierres la terminal esto de saparecera
```
java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```

