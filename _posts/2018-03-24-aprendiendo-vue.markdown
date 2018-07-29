---
layout: post
title: Ejemplo de una aplicación con Vue.js
date: 2018-03-24 12:00:00 
categories: Javascript Vue.js
tags: JavaScript Vue.js Pug Stylus Webpack
short: Actualmente hay muchos frameworks de javascript y vue.js se ha puesto como uno de los populares pese a que llego demasiado tarde. En este ejemplo vamos a mostrar de que es capaz he hacer este framework
---

Actualmente hay muchos frameworks de javascript y  _**Vue.js**_ se ha puesto como uno de los populares pese a que ha llegado demasiado tarde.

En este ejemplo mostraremos de que es capaz he hacer este framework.

El objetivo es ecribir una pequeña aplicación, este documento no es una guia de referencia, para eso esta la [documentación](https://vuejs.org){:target="_blank"} oficial mejor explicada de lo que podria hacer yo.

## Instalamos _Vue.js_

Para empezar se instala de forma global via NPM
```terminal
$ npm install -g vue
$ npm install -g vue-cli
```

## Creamos un proyecto

Utilizando el Comand Line Interface (CLI) se crea un proyecto, que construira una estructura con su configuración minima requerida.
```terminal
$ vue init webpack-simple my-project
$ cd my-project
$ npm install
$ npm run dev
```
## Que vamos a construir

La idea es crear una aplicaciòn donde seleccionando un pais nos despliege Los Top Artist.


## Agregamos un _API_ para consumir servicios

Para la aplicaciòn se necesita un endpoint de donde se tomara la informacón a mostrar; para esto utilizamos la API de [last.fm](https://www.last.fm/api){:target="_blank"}.

Para eso se crea una clase que nos ayudara a obtener la información de la API


Dentro de **src/api/index**

```javascript
const apikey = xxxxxxxxxx
const url = `http://ws.audioscrobbler.com/2.0/?method=geo.gettopartists&country=:country&api_key=${apikey}&format=json`
export default function getArtists (country){
    const simpleUrl = url.replace(':country',country) 
    return fetch(simpleUrl)
    .then(res => res.json())
    .then(json => json.topartists.artist)
}
``` 

## Agregamos un componente Artist

Se crea un componente Vue de tipo Artist donde se muestra el nombre y la imagen del artista

Dentro de **src/component/Artist.vue**
```javascript
<template lang="pug">
  li.artist
    h2: a(:href="artist.url" target="_blank") {{ artist.name }}
    img(:src="artist.image[2]['#text']")
</template>
<script>
export default {
  name:'artist',
  props:['artist']
}
</script>
<style lang="stylus" scoped>
li.artist
  display block
  margin 10px 0
</style>
```

## Agregar el template
Dentro de la aplicación configuramos nuestro Template

```html
<template lang="pug">
#app
  img(src='http://saidmlx.online/simple-vue/dist/logo.png')
  h1 SaidMusic
  select(v-model="selectedCountry")
    option(v-for= "country in countries" :value="country.value") {{country.name}}
  ul
    artist(v-for= "artist in artists" v-bind:artist="artist" v-bind:key="artist.mbid") {{artist.name}}
</template>
```

## Agregar el javascript

```javascript
<script>
import getArtists from './api';
import Artist from  './components/Artist.vue';

export default {
  name: 'app',
  data () {
    return {
      artists: [],
      countries:[
        {name:"España",value:"spain"},
        {name:"México",value:"mexico"},
        {name:"Colombia",value:"colombia"}
      ],
      selectedCountry:'colombia',
      loadding:true
    }
  },
  methods:{
    refreshArtist(){
      var self = this
      self.artists=[];
      getArtists(this.selectedCountry)
      .then(function(artists){
        self.artists = artists
      })
    }
  },
  components:{
    Artist,
    spinner
  },
  watch:{
    selectedCountry:function(){
      this.refreshArtist()
    }
  },
  mounted: function() {
    this.refreshArtist()
  }
}
</script>
```

## Ejecución

Ya con eso tenemos nuestra aplicación, solo es cuestión de ejecutar nuestra aplicación

```terminal
$ npm run dev
```
y tenemos el siguiente resultado



![Image of Yaktocat]({{ site.url }}/assets/images/2018-03-24-aprendiendo-vue/simple-vue.gif)


**Quieres ver como funciona**
>[saidmlx.online/simple-vue](http://saidmlx.online/simple-vue/)

**Quieres descagar el codigo**
>[github.com/saidmlx/simple-vue](https://github.com/saidmlx/simple-vue)








