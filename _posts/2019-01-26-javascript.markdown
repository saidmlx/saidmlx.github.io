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
```javascript

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

