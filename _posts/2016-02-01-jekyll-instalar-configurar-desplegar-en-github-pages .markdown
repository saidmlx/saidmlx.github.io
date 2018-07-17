---
layout: post
title:  "Jekyll: Instalar, configurar y desplegar en GitHub Pages  "
date:   2016-02-01 21:11:15 -0500
categories: jekyll
---
> Esta es una actualización a Junio 2018 con la version 3.8.3 de Jekyll.

Esta es una guia básica de como instalar Jekyll y desplegar un blog en **GitHub Pages**.

## Instalación 

Esta instalacion es sobre OSX asi que toda instalacion sera via **Homebrew**.

1. Para mepezar hay que instalar **GEM Ruby**
	```terminal
	$ brew install ruby
	```
2. Instalamos **Jekyll**
	```terminal
	$ gem install bundler jekyll
	```
3. El siguiente paso creara un proyecto básico con un post de ejemplo.
	```
	$ jekyll new my-awesome-site
	```
4. Ejecutamos el servidor local _http://127.0.0.1:4000/_
	```terminal
	$ cd my-awesome-site
	$ bundle exec jekyll serve
	```

## Agregar el proyecto en GitHub Pages

Para esta configuracion es necesario tener una cuenta en **GitHub**

Creamos el repositorio con el mismo nombre que nuesta cuenta.
Por ejemplo si nuestra cuenta se llama **_username_** creamos una cuenta como **_username.github.io_**

Dentro de nuestro blog inicializamos el repositorio.
```terminal
$ git init
$ git add .
$ git commit -m "Initial commit blog"
$ git remote add https://github.com/username/username.github.io.git
$ git push -u origin master
```

Y con eso podemos acceder a nuestra blog con la url **https://username.github.io**

Con esto ultimo podemos ver nuestro proyecto en http://127.0.0.1:4000/
