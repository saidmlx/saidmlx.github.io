---
layout: post
title: Ejemplo de una aplicación con Vue.js
date: 2018-03-24 12:00:00 
categories: javascript vue
tags: JavaScript Vue pug stylus webpack pug
short: Actualmente hay muchos frameworks de javascript y vue.js se ha puesto como uno de los populares pese a que llego demasiado tarde. En este ejemplo vamos a mostrar de que es capaz he hacer este framework
---

Actualmente hay muchos frameworks de javascript y  [vue.js](https://vuejs.org) se ha puesto como uno de los populares pese a que llego demasiado tarde.

En este ejemplo vamos a mostrar de que es capaz he hacer este framework

Aqui vamos a realizar un ejemplo, no vamos a describir que es el framawork para eso ya hay mucha documentacion en la pagina oficial como enotros sitios, aqui solo demostraremos los pasos minimos para tener una aplicacion funcional en minutos

## Instalamos [vue.js](https://vuejs.org)

Para empezar hay que instalarlo con NPM de forma global
```terminal
$ npm install -g vue
$ npm install -g vue-cli
````
## Creamos un proyecto
Utilizando el Comand Line Interface cramos un proyecto con las configuraciones minimasrequeridas
```terminal
$ vue init webpack-simple my-project
$ cd my-project
$ npm install
$ npm run dev
```

## Agregamos un **api** para consumir servicios

Para esto utilizamos una API de [last.fm](https://www.last.fm/api)

El Endpoint que vamos a utilizar nos trae los Top Artist por pais

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

## Agregamos un componente 

por cada uno de los artistas que nos devuelva el Endpoint creamos un componente

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

<div class="text-center">
  <img src="{{ site.url }}/assets/images/2018-03-24-aprendiendo-vue/simple-vue.gif" class="rounded img-thumbnail post-img-center" style="width: 300px; height: 300px;" alt="Simple example vue aplication">
</div>

**Quieres ver como funciona**
>[saidmlx.online/simple-vue](http://saidmlx.online/simple-vue/)

**Quieres descagar el codigo**
>[github.com/saidmlx/simple-vue](https://github.com/saidmlx/simple-vue)








