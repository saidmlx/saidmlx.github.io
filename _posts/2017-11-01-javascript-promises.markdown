---
layout: post
title:  Javascript promises
date: 2017-11-01 12:00:00 -0000
categories: Javascript
tags: javascript
short: En algunas ocasiones necesitamos llamar peticiones HTTP, y sabemos que para que la experiencia de usuario no sea desfavorable estas peticiones deben ser asíncronas, pero que pasa si estas peticiones dependen una de otra, lo que significaría anidar estas peticiones; para este caso necesitamos promesas.

---

En algunas ocasiones necesitamos llamar peticiones HTTP, y sabemos que para que la experiencia de usuario no sea desfavorable estas peticiones deben ser asíncronas, pero que pasa si estas peticiones dependen una de otra, lo que significaría anidar estas peticiones; para este caso necesitamos promesas.

Una promesa no es más que una respuesta que puede o no estar disponible; esto significa que si lanzamos una petición la respuesta puede ser devuelta al instante o tardar 1,2,3,... minutos o no responder nunca.

## La idea principal de una promesa 
El método siguiente no es más que una petición **_Ajax_** con **_Jquery_** que utiliza el objeto _**Defered_** que nos ayuda a registrar diferentes callbacks, este objeto lo que hace es regresar el objeto cuando se tiene una respuesta ya sea satisfactoria (**_resolve_**) o cuando falla (**_reject_**).

```javascript
var ajaxCall = function(url,data){
  var deferred = $.Deferred();
  $.ajax ({
    type: "GET",
    url: url,
    dataType: 'json', 
    data:data,
    success: function (data){
      deferred.resolve(data );
    },
    fail: function (data,status){  
      deferred.reject(data); 
    },
    error: function (data,status){ 
      deferred.reject(data); 
    }
  });
  return deferred.promise();
}
```

### Petición básica

Un ejemplo de petición básica seria la siguiente, el método **_getBooks()_** llama al servicio **http://localhost:4001/books** una vez responda el servicio se jecuta el metodo **_printBooks()_**.


``` Javascript
getBooks = function(){
  return this.ajaxCall('http://localhost:4001/books',{});
}
printBooks = function(books){
  return books.forEach(this.printBook);		 
}

getBooks()
.then(this.printBooks.bind(this));
```

### Peticiones anidadas
Cuando necesitamos anidar peticiones podemos utilizar el método antes definido y utilizar el objeto **then**.

En este ejemplo tenemos la siguiente secuencia.
1. Con el método **_getBooks()_** se realiza la petición al servicio **_http://localhost:4001/books_** que nos regresara un listado de libros. 
2. Con el método **_getMusic()_** construimos HTML de los libros.
3. Con el método **_getMusic()_** se realiza la petición al servicio **_http://localhost:4001/music_** que nos regresara un listado de los álbumes de música. 
4. Con el método **_printMusic()_** construimos HTML de los álbumes de música que nos regresó el servicio.

El código para hacer lo anterior es el siguiente.
```javascript
this.getBooks()
.then(this.printBooks.bind(this))
.then(this.getMusic.bind(this))
.then(this.printMusic.bind(this));
```

## Peticiones encadenadas


