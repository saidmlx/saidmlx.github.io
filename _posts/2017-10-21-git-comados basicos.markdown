---
layout: post
title:  GIT comandos básicos
date:   2017-10-21 12:00:00 -0000
categories: Git
short: Git es un software de control de versiones muy popular, en este post estoy dejando los comandos maás básicos que podemos utilizar...
---

### Que es GIT

Git es un software de control de versiones muy popular, en este post estoy dejando los comandos más básicos que podemos utilizar.

Como comentario no hay que confundir **Git** con **Github**, ya que este último es una plataforma de desarrollo colectivo, donde podemos alojar proyectos y no es la única en su tipo, también existe **Bitbucket** que es una herramienta colaborativa donde podemos versionar tanto en **Git** como en **Mercurial**.

Cuál de los dos en mejor, para mi los dos sirven para el mismo fin, en mi caso utilizo Github ya que fue el primero al que llegue y donde tengo alojado mis proyectos y mi sitio, así que aquí hare referencia a él.   


### ¿Como inicializar un repositorio?

Cuando tenemos un proyecto podemos agregarle el control de versiones.

``` bash
git init
```

###  ¿Como descargo un proyecto de github?

Este comando descargará el proyecto a tu ubicación local. 

``` bash
git clone https://github.com/saidmlx/SpringWebMvc.git
```

### ¿Como subo mi proyecto a Github?

Si ya tienes un proyecto y necesitas subirlo a Github esto se hace con tres comandos.

1. Agregamos los archivos.

	``` bash
	git add .
	```

2. Hacemos _**un commit**_ a mi local stage.

	``` ssh
	git commit -m "Descripcion del commit"
	```

3. Subimos el proyecto a la rama master

	``` bash
	git push origin master
	``` 

### ¿que pasa si quiero subir cambios y no tengo actualizado mi proyecto respecto a Github y ya realice modificaciones?

1. agregamos los archivos

	``` bash
	git add .
	```

2. Hacemos un _**commit**_ a mi local stage

	``` shell
	git commit -m "Descripcion del commit"
	```

3. actualizamos el proyecto respecto a Github

	``` bash
	git pull origin master
	``` 

4. subimos el proyecto a la rama master
	
	``` shell
	git push origin master
	``` 

### ¿Cómo crear una nueva rama?

``` bash
git checkout -b [nueva_rama]
``` 

### ¿Cómo me muevo entre ramas?

``` bash
git checkout  [nueva_rama]
``` 

### Tengo muchos repositorios en la nube como github, bitbucket, ... ¿como se a donde apunta mi repo?

```batch
git remote -v
```







