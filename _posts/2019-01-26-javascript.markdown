---
layout: post
title: JavaScript
date: 2019-02-25 12:00:00 
categories: JavaScript 
tags: JavaScript 
---
__Contenido__
- [Declaración de variables](#Declaración-de-variables)
- [Alcance de una variable](#alcance-de-una-variable)
- [Estructuras de control](#estructuras-de-control)
- [Plantillas de cadena de texto (Template Literals)](#plantillas-de-cadena-de-texto-Template-Literals)
- [JSON( JavaScript Object Notation.)](#json-javaScript-object-notation)
- [El objeto this](#el-objeto-this)
- [This y el método bind](#this-y-el-método-bind)
- [Objetos](#objetos)
- [Arrow functions](#Arrow-functions)
- [Arrays (solo se describen los más importantes)](#arrays-solo-se-describen-los-más-importantes)
- [Asincronismo (callbacks, promesas, async-await)](#asincronismo-callbacks-promesas-async-await)
- [Eventos](#eventos)


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

## This y el método bind

Otro momento que analizar es cuando trabajamos con eventos, en este script se crea un Objeto Button y se le agrega un evento click y se le asigna el método ```nextMonth()``` para que incremente el valor de la  variable ```currentMonth``` en 1, pero en la línea ```this.currentMonth++;``` no puede acceder al objeto base porque este obedece al objeto que generó el evento en este caso el Button, por lo tanto el ```currentMonth``` siempre será ```January```.

```javascript
const Demo = function(){    
this.months=['January','February','March','April','May','June','July','August','September','October','November','December']
  this.currentMonth=0
  this.btnNext = document.createElement('button')
  this.btnNext.addEventListener('click', this.nextMonth);
}
Demo.prototype.nextMonth = function(){
  this.currentMonth++;
}
Demo.prototype.printCurrentMonth = function(){
  console.info(this.months[this.currentMonth])
}
const demo = new Demo();
demo.printCurrentMonth(); //--print January
demo.btnNext.dispatchEvent(new Event('click'));
demo.printCurrentMonth(); //--print January
demo.btnNext.dispatchEvent(new Event('click'));
demo.printCurrentMonth(); //--print January
``` 

*If you copy and paste the script into Chrome Browser you will see the result*


Para eso es necesario utilizar el método bind que lo que hace es enviar el this al método del evento para poder uso de sus propiedades de la forma ```this.nextMonth.bind(this).```

```javascript
const Demo = function(){
   this.months=['January','February','March','April','May','June','July','August','September','October','November','December']
   this.currentMonth=0
   this.btnNext = document.createElement('button')
   this.btnNext.addEventListener('click', this.nextMonth.bind(this));
 }
 Demo.prototype.nextMonth = function(){
   this.currentMonth++;
 }
 Demo.prototype.printCurrentMonth = function(){
   console.info(this.months[this.currentMonth])
 }
 const demo = new Demo();
 demo.printCurrentMonth(); //--print January
 demo.btnNext.dispatchEvent(new Event('click'));
 demo.printCurrentMonth(); //--print February
 demo.btnNext.dispatchEvent(new Event('click'));
 demo.printCurrentMonth(); //--print March
``` 

## Objetos

Las tres formas de escribir Clases

### Prototype
```javascript
var Dog = function(){
   console.log('Dog:Constructor()')
 }
 Dog.prototype.talk = function(){
     console.info('auuuuuu');
 }
 var dog = new Dog();
 dog.talk();
 //--auuuuuu
``` 

### Object
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
 //--"auuuuuu"
```

### Class

```javascript
class Dog  {
   constructor(){
    
   }
   talk(){
     console.info('auuuuuu');
   }
 }
 const dog = new Dog();
 dog.talk();
 //--"auuuuuu"
 ```
 
## Arrow functions 

Las arrow functions son una forma más reducida de escribir funciones, esta es una forma de escribir funciones anónimas además que provee una particularidad de desvincular el this.  
La siguiente función calcula el área de un círculo y veras la forma de cómo los Array Functions te ayudan a reducir el código.
__Versión 1__
```javascript
var radio = 2;
var areaCircle =function(radio){
   return Math.PI *  Math.pow(radio,2)
}
var area=areaCircle(radio);
console.log('El area de un circulo con radio '+radio+' es: '+ area);//--El area de un circulo con radio 2 es: 12.566370614359172
```


__Versión 2: con reducción de codigo gracias __
```javascript
let radio = 2
const areaCircle = (radio) => Math.PI *  Math.pow(radio,2)
console.log(`El area de un circulo con radio ${radio} es: ${areaCircle(radio)}`);//--El area de un circulo con radio 2 es: 12.566370614359172
```


Pero qué pasa con el concepto de desvincular el this.
Cantes cuando se utilizaba un foreach y se quería acceder al this del objeto que lo contenía era necesario reasignar el this o pasarlo como parámetro de esta forma
```javascript
const Sum = function(){
   this.numbers =[1,2,3,4,5,6,7,8,9];
   this.total=0
    this.sumElements = function(){
     this.numbers.forEach(function(item){
       this.total= this.total+item;//en este punto this corresponde al objeto Window
     });
   }
 }
 let sum = new Sum();
 sum.sumElements();
 sum.total
```

```javascript
const Sum = function(){
   this.numbers =[1,2,3,4,5,6,7,8,9];
   this.total=0
   let self = this;
   this.sumElements = function(){
     this.numbers.forEach(function(item){
       self.total= self.total+item;
    })
   }
 }
 let sum = new Sum();
 sum.sumElements();
 sum.total
```

Ahora si utilizamos Array Functions no tenemos que asignar a otra variable el objeto this

```javascript
const Sum = function(){
   this.numbers =[1,2,3,4,5,6,7,8,9];
   this.total=0
    this.sumElements = function(){
     this.numbers.forEach( (item) => {this.total= this.total+item;});
   }
 }
 let sum = new Sum();
 sum.sumElements();
 sum.total
```

## Arrays (solo se describen los más importantes)
__filter()__ : regresa todos los elementos que cumplan con el filtro aplicado

```javascript
let days=['Monday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']

console.info(days.filter(item => item.length>6))
//-- ["Tuesday", "Wednesday", "Thursday", "Saturday"]
```


__map()__:  regresa un nuevo arreglo con las operaciones aplicadas a cada elemento
```javascript
let days=['Monday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']

console.info(days.map(item => item.toUpperCase()) )
//--  ["MONDAY", "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY", "SATURDAY", "SUNDAY"]
```

__push()__: agrega un nuevo elemento al array
```javascript
let days=['Monday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday']
days.push('Sunday')
console.info(days)
//--["Monday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
```

__pop()__: elimina el último elemento
```javascript
let days=['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']
days.pop()
console.info(days)
//--["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"]
```

__shift()__: elimina el primer elemento de un array
```javascript
let days=['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']
var day = days.shift()
console.info(days)
//--["Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"]
console.info(day)
//--"Monday"
```

__slice__:([posición inicial], [posición final]): hace una copia de los elementos seleccionados y el array principal queda intacto
let days=['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']

```javascript
var day = days.slice(2,4);

console.info(days);
//--["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
console.info(day);
//--["Wednesday", "Thursday"]
```


__splice__([posición inicial], [número de elementos a extraer]): extrae elementos seleccionados del array principal.
```javascript
let days=['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']

var day = days.splice(2,2);

console.info(days);
//-- ["Monday", "Tuesday", "Friday", "Saturday", "Sunday"]
console.info(day);
//-- ["Wednesday", "Thursday"]
```

__reduce()__: El método reduce hace la funcion de acululador teniendo en cada ciclo el valor anterior con el valor nuevo.
```javascript
const numbers=[1,2,3,4,5,6,7,8]

const total = numbers.reduce((a,b) => a+b)

console.info(numbers)
//--[1, 2, 3, 4, 5, 6, 7, 8]

console.info(`total: ${total}`)
//--"total: 36"
```
## Asincronismo (callbacks, promesas, async-await)
En algunas ocasiones necesitamos llamar peticiones HTTP, y sabemos que para que la experiencia de usuario no sea desfavorable estas peticiones deben ser asíncronas, pero qué pasa si estas peticiones dependen una de otra, lo que significa anidar estas peticiones; para este caso necesitamos promesas.
Una promesa no es más que una respuesta que puede o no estar disponible; esto significa que si lanzamos una petición la respuesta puede ser devuelta al instante o tardar 1,2,3,… minutos o no responder nunca.
Para los ejemplos posteriores estaremos utilizando  la API REST JSON PLACEHOLDER

### jQuery Callbacks 
Si analizamos el método ajax ```ajaxCallback()``` podemos ver que existe la línea ```var deferred = $.Deferred();``` el método ```$.Deferred()``` es el que nos ayuda a generar una cola de llamadas y retransmite una petición resuelta o una petición rechazada como lo podemos ver en las líneas  ```deferred.resolve(data );  deferred.reject(data);```.
Al analizar la llamada  ```ajaxCallback().then(resolvedAnswer, rejectedAnwer)``` podremos ver que llama al método then que es un manejador para ser llamado cuando un Objeto Deferred es resuelto o rechazado, este está aceptando dos funciones la primera es para cuando hay una petición ```resuelta(resolvedAnswer)``` y la segunda cuando hay una petición ```rechazada(rejectedAnwer)```.


```javascript
var ajaxCallback = function(){
  var deferred = $.Deferred();
  $.ajax ({
    type: "GET",
    url: 'https://jsonplaceholder.typicode.com/users/1',
    dataType: 'json',
    success: function (data,textStatus,jqXHR ){
      deferred.resolve(data );
    },
    fail: function (data,textStatus,jqXHR){ 
      deferred.reject(data);
    },
    error: function (data,textStatus,jqXHR){
      deferred.reject(data);
    }
  });
  return deferred.promise();
}
var resolvedAnswer = function(data){
  console.debug('--resolvedAnswer--')
  console.debug(data)
}
var rejectedAnwer = function(data){
  console.debug('--rejectedAnwer--')
  console.debug(data)
}
 
ajaxCallback().then(resolvedAnswer, rejectedAnwer)

```

__Resolved Answer__
```terminal

"--resolvedAnswer--"

Object {
 address: Object {
   city: "Gwenborough",
   geo: Object {},
   street: "Kulas Light",
   suite: "Apt. 556",
   zipcode: "92998-3874"
 },
 company: Object {
   bs: "harness real-time e-markets",
   catchPhrase: "Multi-layered client-server neural-net",
   name: "Romaguera-Crona"
 },
 email: "Sincere@april.biz",
 id: 1,
 name: "Leanne Graham",
 phone: "1-770-736-8031 x56442",
 username: "Bret",
 website: "hildegard.org"
}
```
__Rejected Answer__

```terminal
"--rejectedAnwer--"
Object{
  ...
  readyState: 4,
  responseJSON: Object{},
  responsetext:"{}"
  status:404,
  statusText:'error',
  ...
}

```

### Promise Callbacks

Si no queremos utilizar jQuery la opción es utilizar el Objeto Promise que funciona masomenos como un proxy para encontrar un valor no disponible en el momento pero puede o no estar en el futuro.
Tenemos el mismo ejemplo anterior, si analizamos ```promiseCallback```  veremos que regresa una promesa(return new ```Promise(function(resolve, reject) )``` y mediante el objeto ```XMLHttpRequest()``` hace una petición asíncrona para obtener un usuario y una vez responda se valida que tipo de status regresa si es un __200__ entonces la promesa envía un resolver (```resolve( req.response );```) y en caso contrario un reject ```reject( Error(req.statusText));```.
Para hacer la petición se hace de la siguiente forma  ```promiseCallback().then( resolvedAnswer, rejectedAnwer)``` y si observamos se utiliza también el then con los mismos parámetros el primero para una ejecución exitosa () y la segunda en caso de que exista algún error.

```javascript
const promiseCallback = function () {
   return new Promise(function(resolve, reject) {
     var req = new XMLHttpRequest();
     req.open('GET', 'https://jsonplaceholder.typicode.com/users/1');
      req.onload = function() {
       if (req.status == 200) {
         resolve(req.response);
       }
       else {
         reject(Error(req.statusText));
       }
     };
     req.onerror = function() {
       reject(Error("Network Error"));
     };
     req.send();
   });
 }
  var resolvedAnswer = function(data){
   console.debug('--resolvedAnswer--')
   console.debug(data)
 }
 var rejectedAnwer = function(data){
   console.debug('--rejectedAnwer--')
   console.debug(data)
 }
  promiseCallback().then(resolvedAnswer, rejectedAnwer)

```
__Resolved Answer__
```terminal
"--resolvedAnswer--"
{
   'id': 1,
   'name': 'Leanne Graham',
   'username': 'Bret',
   'email': 'Sincere@april.biz',
   'address': {
     'street': 'Kulas Light',
     'suite': 'Apt. 556',
     'city': 'Gwenborough',
     'zipcode': '92998-3874',
     'geo': {
       'lat': '-37.3159',
       'lng': '81.1496'
     }
   },
   'phone': '1-770-736-8031 x56442',
   'website': 'hildegard.org',
   'company': {
     'name': 'Romaguera-Crona',
     'catchPhrase': 'Multi-layered client-server neural-net',
     'bs': 'harness real-time e-markets'
   }
 }

```

### Fetch
La siguiente implementación son promesas con API Fetch que es una API para recuperar recursos, es muy similar a la anterior.
Lo que podemos decir de fetch es que tiene una particularidad de que todas las respuestas las responde de forma correcta lo que significa que todas caerán dentro del then y es ahí donde tenemos que evaluar el status para resolver la petición o rechazarla. 

```javascript
const promiseCallback = function () {
    return new Promise((resolve, reject) => {
     
   fetch('https://jsonplaceholder.typicode.com/users/1')
   .then((response) => {
     if(response.status==200){
       resolve(response.json())
     }else{
       reject(response);
     }
   })
   .catch((error) => console.info(error) )
   });
 }
  var resolvedAnswer = function(data){
   console.debug('--resolvedAnswer--')
   console.debug('->',data)
 }
 var rejectedAnwer = function(data){
   console.debug('--rejectedAnwer--')
   console.debug('-->',data)
 }
  promiseCallback().then(resolvedAnswer, rejectedAnwer)
```


### Async Await
Async Await apareció en el __ES2017__  y es una nueva definición para implementar promesas, lo que hace es definir un método de tipo __async__ de la forma ```async function getData() {}``` este utiliza el await para hacer esperar a la función hasta que esta responda; de forma de una promesa resuelta y sólo si entra dentro de la estructura catch devolverá una promesa rechazada. 

```javascript
async function asyncAwaitCallback() {
   try {
     const response1 = await fetch('https://jsonplaceholder.typicode.com/users/1');
     return await response1.json();
   } catch (error) {     
     throw error;
   }
 }
 var resolvedAnswer = function(data){
   console.debug('--resolvedAnswer--')
   console.debug('->',data)
 }
 var rejectedAnwer = function(data){
   console.debug('--rejectedAnwer--')
   console.debug('-->',data)
 }

asyncAwaitCallback().then(resolvedAnswer) .catch(rejectedAnwer);
```

## Eventos
Los eventos son interacciones de un usuario dentro del DOM(Document Object Model) como desarrollador usamos los eventos para generar tareas como por ejemplo cambiar el color de un botón cuando es presionado.
Existen tres modos de generar eventos
Evento dentro de las etiquetas HTML
En este ejemplo se asocia el evento onclick a un botón para que despliegue un mensaje de alerta a ser presionado.
```javascript
<button onclick="alert('Hello world!')"></button>
```

### Evento como propiedad de un elemento DOM
En este caso se referenci el objeto DOM y a la propiedad onclick se le asocia una función anónimo.

```javascript
const myButton = document.getElementsByTagName('button ')[0]
myButton.onclick = function(event){alert('Hello world');};
```

### Evento con EventTarget.addEventListener
Esta forma es la más utilizada en la actualidad, y lo que hace es registrar un evento específico y asociarse un método.

```javascript
const myButton = document.getElementsByTagName('button ')[0]
myButton.addEventListener('onclick',function(event){alert('Hello world')});
```





