---
layout: post
title:  Trabajando con nuevas funcionalidades de Javascript
date:   2017-11-24 12:00:00 -0000
categories: javascript
short: Ejemplos de nuevas funcionalidades de Javascript, como son los metodos reduce, map, filter, uso de arrow funcion y trabajo con objetos ...
---

Ejemplos de nuevas funcionalidades de Javascript, como son los metodos reduce, map, filter, uso de arrow funcion y trabajo con objetos

### Funciones

1 Antes
```javascript
var radio = 2;
var areaCircle =function(radio){
    return Math.PI *  Math.pow(radio,2) 
}
var area=areaCircle(radio);
console.log('El area de un circulo con radio '+radio+' es: '+ area);

//--> El area de un circulo con radio 2 es: 12.566370614359172
```
2 Ahora
``` javascript
let radio = 2
const areaCircle = (radio) => Math.PI *  Math.pow(radio,2) 
console.log(`El area de un circulo con radio ${radio} es: ${areaCircle(radio)}`);

//-->  El area de un circulo con radio 2 es: 12.566370614359172
```

### Trabajando Objetos

1 Prototype
```javascript
var Dog = function(){
  console.log('Dog:Constructor()')
}
Dog.prototype.talk = function(){
    console.info('auuuuuu');
}
var dog = new Dog();
dog.talk();
``` 
2 Create
```javascript
const Dog = {
  init:function init(){
    console.log('Dog:Constructor()');
  },
  talk : function talk(){
    console.info('auuuuuu');
  }
}
const dog = Object.create(Dog);
dog.talk();
```
3 Class
```javascript
class Dog  {
  constructor(){
    console.log('Dog:Constructor()')
  }
  talk(){
    console.info('auuuuuu');
  }
}
const dog = new Dog();
dog.talk();
```
### Arrays

#### Reduce
Sumar numeros de un array.

1 Antes
```javascript
var add = function(array){
  var total = 0;
  for(i=0;i<array.length;i++){
    total +=array[i];
  }
  return total;
}
add([1,2,3]);
//-- 6
```
2 Despues 
```javascript
function add(array){
  return array.reduce(
    function(acum,next){ 
      return(acum +=next)  
    }
  )
}
add([1,2,3])
//-- 6
```
3 Despues utilizando arrow function
```javascript
const add = (array) => array.reduce((acum,next) => acum +=next,0);
add([1,2,3]);
//-- 6
```
#### Map
Duplicar el valor de los numeros de un array

1 Antes utilizando la estructura _**For**_
```javascript
function duplicate(array){
  var arrayDuplicate=[];
  for(i=0;i<array.length;i++){
    arrayDuplicate.push(array[i]*2);
  }
  return arrayDuplicate;
} 
duplicate([1,2,3])

//-- [2,4,6]
```
2 Despues utilizando la estructura _**Map**_
```javascript
function duplicate(array){
  return array.map(function(item){return item*2});
} 
duplicate([1,2,3])
```
3 Despues utilizando la estructura _**Map**_ combinada con _**Arrow Functions**_
```javascript
const  duplicate =(array) => array.map(item => item*2);
duplicate([1,2,3]);

//-- [2,4,6]
```

#### Filter
Obtener numeros pares de un array

1 Antes utilizando la estructura _**For**_ 
```javascript
function pairNumbers(array){
  var arrayPairNumbers=[];
  for(i=0;i<array.length;i++){
    if(array[i]%2 == 0){
      arrayPairNumbers.push(array[i]);
    }
  }
  return arrayPairNumbers;
} 
pairNumbers([1,2,3,4,5,6])

//-- [2,4,6]
```
2 Despues utilizando la estructura _**Filter**_
```javascript

function pairNumbers(array){
  return array.filter(function(item){return item%2 == 0})
} 
pairNumbers([1,2,3,4,5,6])

//-- [2,4,6]
```
3 Despues utilizando la estructura _**Filter**_ combinada con _**Arrow Functions**_
```javascript
const  pairNumbers = (array) => array.filter( item =>   item%2 == 0);
pairNumbers([1,2,3,4,5,6])

//-- [2,4,6]
```
### Closures

```javascript
function sayHelloFamily(lastName){
  return function sayHelloFamilyMember(name){
    console.log(` Hello ${lastName} ${name}`)
  }
}
const  saludarMorales=sayHelloFamily('Morales');
saludarMorales('Said')

//-- Hello Morales Said
```

### Inmediatly Invoked Function Expression (IIFE)

```javascript
const name = 'said';
(function sayHello(){
 console.log(`Hello ${name}`)
})()
```
### El Scope this

Si utilizabas el self como en el siguiente ejemplo
```javascript
class Persona {
  constructor(nombre, amigos = []) {
    this.nombre = nombre
    this.amigos = amigos
  }

  listarAmigos() {
    const self = this
    this.amigos.forEach(function(amigo)  {
      console.log(`Hola, mi nombre es ${self.nombre} y soy amigo de ${amigo}`)
    })
  }
}
const persona = new Persona("Said", [ "Pedro", "Juan", "Pepe" ])
persona.listarAmigos()

//-- Hola, mi nombre es Said y soy amigo de Pedro
//-- 11 Hola, mi nombre es Said y soy amigo de Juan
//-- Hola, mi nombre es Said y soy amigo de Pepe
```

Tabbien lo puedes utilizar de la siguiente forma.

``` javascript
class Persona {
  constructor(nombre, amigos = []) {
    this.nombre = nombre
    this.amigos = amigos
  }
  listarAmigos() {
    this.amigos.forEach(function(amigo)  {
      console.log(`Hola, mi nombre es ${this.nombre} y soy amigo de ${amigo}`)
    }.bind(this) )
  }
}
const persona = new Persona("Said", [ "Pedro", "Juan", "Pepe" ])
persona.listarAmigos()

//-- Hola, mi nombre es Said y soy amigo de Pedro
//-- 11 Hola, mi nombre es Said y soy amigo de Juan
//-- Hola, mi nombre es Said y soy amigo de Pepe
```

Un poco mas reducido con Arrow Functions

```javascript
class Persona {
  constructor(nombre, amigos = []) {
    this.nombre = nombre
    this.amigos = amigos
  }
  listarAmigos() {
    this.amigos.forEach((amigo) => console.log(`Hola, mi nombre es ${this.nombre} y soy amigo de ${amigo}`))
  }
}
const persona = new Persona("Said", [ "Pedro", "Juan", "Pepe" ])
persona.listarAmigos()

//-- Hola, mi nombre es Said y soy amigo de Pedro
//-- 11 Hola, mi nombre es Said y soy amigo de Juan
//-- Hola, mi nombre es Said y soy amigo de Pepe
```




