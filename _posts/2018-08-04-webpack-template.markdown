---
layout: post
title: Template Basico Webpack + Babel + SASS
date: 2018-08-06 12:00:00 
categories: JavaScript Webpack
tags: JavaScript Webpack Babel SASS
---

## Inicializamos un protecto
```terminal
$ mkdir webpack-template
$ cd webpack-template
$ npm init -y
```
## Instalamos dependencias
Las dependencias que utilizaremos son 

1. babel-core 6.26.3
2. babel-loader 7.1.5
3. babel-preset-es2015 6.24.1
4. css-loader 1.0.0",
5. mini-css-extract-plugin 0.4.1
6. node-sass 4.9.2
7. path 0.12.7
8. sass-loader 7.1.0
9. style-loader 0.21.0
10. webpack 4.16.4
11. webpack-cli 3.1.0

## Creamos el archivo de configuracion 

La configuración esta definida en el archivo `webpack.config.js`
### Configuracion básica
La configuración basica solo agrega configuracion para generar un bundle para Javascript
```javascript

const path = require('path');

module.exports = {
  entry: path.resolve(__dirname, 'src/js/index.js'),
  output: {
    filename: 'app.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test:/\.js$/,
      }
    ]
  }
};

```

### Agregamos babel
Para poder escribir ES7 y todos los navegadores puedan leer el Javascript
```javascript
...

module.exports = {
  module: {
    rules: [
      {
        test:/\.js$/,
        use: {
            loader: 'babel-loader',
            options: {
                presets: ['es2015']
            }
        }
      },
    ]
  },
};
```

### Agregamos SASS como preprocesador de estilos
```javascript

const path = require('path');
const MiniCssExtractPlugin = require("mini-css-extract-plugin"); 

module.exports = {

  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [
          process.env.NODE_ENV !== 'production' ? 'style-loader' : MiniCssExtractPlugin.loader,
          "css-loader",
          "sass-loader"
        ] 
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
    ]
};
```

## Agregamos la tarea al archivo `package.json`

```json
...
"scripts": {
    "build": "webpack --mode=development",
  },
...
```

## Ahora la estructura de archivos
```terminal
├── package.json
├── src
│   ├── html
│   │   └── index.html
│   ├── js
│   │   ├── app.js
│   │   └── index.js
│   └── sass
│       └── styles.scss
└── webpack.config.js
```


**index.html**
```html
<!doctype html>
<html>
  <head>
    <title>Getting Started</title>
    <script src="https://unpkg.com/lodash@4.16.6"></script>
  </head>
  <body>
    <script src="../../dist/app.js"></script>
  </body>
</html>
```
**app.js**
```javascript
class App {
  constructor(){
    const element = document.createElement('h1');
    element.innerHTML='Wellcome to WebPack';
    document.body.appendChild(element);
  }
}
export default App;  
```
**index.js**
```javascript
import '../sass/styles.scss'
import App from './app.js'
const a= new App()
```
**styles.scss**
```css
$font-color: #121212;
$font-inverse-color: #F2F2F2;
$color-background-color: #0BC8FF;
body{
  background-color: $color-background-color;
}
h1{
  text-align: center;
  color: $font-inverse-color;
}
```







