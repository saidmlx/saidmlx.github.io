---
layout: post
title: Conceptos basicos de SASS
date: 2000-01-01 1:00:00 
categories: css SASS preprocesadores
tags: css SASS
---

Sass es uno de los preprocesadores de estilos mas utilizados y en afan de saber su estructura y funcionalidad comparto este documento con las caracteristicas mas importantes.

## Partials

Los **Partials** no son mas que la capacidad de poder generar multiples archivo y despues importalos al archivo principal de esta forma podemos escribir componenetes dentro de archivos aislados para despues cuando se transpile el **sass** quede todo integrado.

Por ejemplo la estructura basica que utilizo es la siguiente:
```terminal
style.scss
_variables.scss
```


## Mixins


<p data-height="265" data-theme-id="0" data-slug-hash="zLLxyy" data-default-tab="html,result" data-user="saidmlx" data-pen-title="sass-mixin" class="codepen">See the Pen <a href="https://codepen.io/saidmlx/pen/zLLxyy/">sass-mixin</a> by Said (<a href="https://codepen.io/saidmlx">@saidmlx</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


Son artefactos que nos ayudan a reutilizar codigos por ejemplo cuando 

1. definimos el mixin
```sass
@mixin demo{
  padding: 10px;
}
```

2. Aplicamos el mixin
```css
section{
    @include demo;
}
```
3. Resultado CSS
```
```




