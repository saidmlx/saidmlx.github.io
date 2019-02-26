---
layout: post
title: JavaScript
date: 2019-02-25 12:00:00 
categories: JavaScript 
tags: JavaScript 
---

## Declaración de variables

Las formas de escribir variables son las siguientes.

### VAR
Utilizado para declarar variables que pueden ser redeclaradas y que conservan su valor en el contexto donde fueron declaradas. 
```javascript
var foo = 'bar'
```

### CONST

Utilizado para declaraciones constantes no reasignables pero no entra dentro del concepto de inmutabilidad ya sea locales o globales de acuerdo al contexto donde fueron declaradas
```javascript
const foo=’’
```

### LET

Utilizado para declaraciones variables reasignables pero no redeclarable con alcance donde fueron declaradas
```javascript
let foo=’bar’
Redeclaración
//Redeclaration
var a=1
const b = 2
let c = 3

var a="A" //--  The statement is correct
//const b = "B" //-- SyntaxError: Identifier 'b' has already been declared
let c = "C"  //-- SyntaxError: Identifier 'c' has already been declared

Redefinición
//Redeclaration
var a=1
const b = 2
let c = 3

a="A" //--  The statement is correct
//b = "B" //-- TypeError: Assignment to constant variable.
c = "C"  //--  The statement is correct
```

## Alcance de una variable


__Ejemplo 1__

```javascript
var a=1
const b = 2
let c = 3

function demo(){
 a++;
 //b++; //--TypeError: Assignment to constant variable.
 c++;
 console.info('a: '+a) //-- print: a: 2
 console.info('b: '+b) //-- print: a: 2
 console.info('c: '+c) //-- print: a: 4
}
demo()
console.info('a: '+a)//-- print: a: 2
console.info('b: '+b)//-- print: a: 2
console.info('c: '+c)//-- print: a: 4
```

__Ejemplo 2__
```javascript
var a=1
const b = 2
let c = 3

function demo(){
 var a='A'
 const b = "B"
 let c = "C"
 console.info('a: '+a) //-- print: a: A
 console.info('b: '+b) //-- print: a: B
 console.info('c: '+c) //-- print: a: C
}
demo()
console.info('a: '+a)//-- print: a: 1
console.info('b: '+b)//-- print: a: 2
console.info('c: '+c)//-- print: a: 3
```

## Estructuras de control

Las estructuras de control son similares a otros lenguajes.

### IF
```javascript
const a = true
if(a){
   //TODO
}else{
   //TODO
}
```
### FOR
```javascript
for(let i=0; i<=10;i++){
//TODO
}
```

### FOREACH
```javascript
const months = ['January','February','March','April','May','June','July','August','September','October','November','December']

months.forEach(function(item, index){
   console.info(`Tu estas en el indice ${index} mes ${item}`)
})
//--with arrow function
months.forEach((item, index)=>console.info(`Tu estas en el indice ${index} mes ${item}`))
```

### SWITCH
```javascript
let month='March'
switch(month){
   case "January": console.info('Is winter'); break;
   case "February": console.info('Is winter'); break;
   case "March": console.info('Is Spring'); break;
   case "April": console.info('Is Spring'); break;
   case "May": console.info('Is Spring'); break;
   case "June": console.info('Is Summer'); break;
   case "July": console.info('Is Summer'); break;
   case "August": console.info('Is Summer'); break;
   case "September":console.info('Is Fall'); break;
   case "October": console.info('Is Fall'); break;
   case "November": console.info('Is Fall'); break;
   case "December": console.info('Is winter'); break;
   default: console.info(`Error`);
}
```

## Plantillas de cadena de texto (Template Literals)

Plantillas de cadena de texto o Template Literals es una características que nos permite en una cadena de texto insertar expresiones, olvidándonos así de la concatenación con el signo +.
Para hacer un Template Literal en necesario que estén en comillas tipo ` content `
 y las expresiones deben estar dentro de ```${number}```

```javascript
const number=25;
console.debug(`My lucky number is ${number} `)
//-- "My lucky number is 25 "

console.debug(`If multiply my lucky number ${number*2} `)
//-- "If multiply my lucky number 50 "
```

## JSON( JavaScript Object Notation.)

Es un formato de texto para intercambio de datos muy utilizado en JavaScript que funciona como un reemplazo a XML gracias a su gran facilidad de manejo; y un gran número de APIs en los diferentes lenguajes para poderlos manejar.
Tipos de datos que acepta
- String
- Number
- Object (JSON object)
- Array
- Boolean
- null

### Estructuras permitidas
Los json se escriben en dos estructuras
Nombre Valor
```javascript
{
   var1:1,
   var2:true,
   var3:null,
   var4:{}
}
```

### Lista de valores
```javascript
{
   var_a:[1,2,3,4,5],
   var_b:[{},{},{}]
}
```

### Convertir JSON a String
```javascript
JSON.stringify({
   var1:1,
   var2:true,
   var3:null,
   var4:{}
})

//>"{"var1":1,"var2":true,"var3":null,"var4":{}}"
```


### Convirtiendo texto a JSON

```javascript
JSON.parse(`{"var1":1,"var2":true,"var3":null,"var4":{}}`)
//>
{
   var1:1,
   var2:true,
   var3:null,
   var4:{}
}
```

### Cómo acceder a los valores de un json
```javascript
const luke = {
    "name": "Luke Skywalker",
    "height": "172",
    "mass": "77",
    "films": [
       {
           "title": "The Empire Strikes Back",
           "episode_id": 5
       },
       {
           "title": "Revenge of the Sith",
           "episode_id": 3
       }
    ]
}
luke.name
//"Luke Skywalker"
luke.films[1].title
//"Revenge of the Sith"
```

## El objeto this
El this es el objeto para referirnos al contexto donde estamos pero en algunos casos es necesario; saber administrarlo ya que cambia su contexto ya estemos en un evento, dentro de un foreach.
Por ejemplo en el siguiente ejemplo:
en método ```demo.print()``` imprime el objeto Demo, pero el método ```demo.actionForEach()``` hace referencia al objeto Window

```javascript
class Demo{
   constructor(){
     this.months=['January','February']
   }
   actionForEach(){
     this.months.forEach(function(item){
       console.info(this)
     })
   }
   print(){
     console.info(this)
   }
 }
let demo = new Demo();
demo.print()
demo.actionForEach()
```

Para solucionar lo anterior debemos hacer referencia al this de la siguiente forma, para poder acceder a las propiedades y métodos de la clase Demo 
```javascript
class Demo{
   constructor(){
     this.months=['January','February']
   }
   actionForEach(){
     this.months.forEach(function(item){
       console.info(this)
     },this)
   }
   print(){
     console.info(this)
   }
 }
let demo = new Demo();
demo.print()
demo.actionForEach()
```




