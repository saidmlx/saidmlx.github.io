---
layout: post
title:  Como manejar rutas en angular
date:   2017-12-29 12:00:00 -0000
categories: JavaScript Angular
tags: Angular TypeScript JavaScript

---

Cuando se trabaja con Angular uno de los features mas importantes son las rutas estas son muy facil de implementar.

## ¿Qué necesitamos?

1. NPM, por lo tanto se necesita [Nodejs](https://nodejs.org/es/)

2. Editor de tu preferencia, pero con soporte para __TypeScript__

3. Un proyecto vacío, si no sabes cómo lee el post [
Creando un proyecto con angular 4]({{ site.url }}/javascript/angular/2017/12/15/iniciar-proyecto-angular-4.html)

## Que vamos a hacer

La estructura que nos arroja CLI es la siguiente o al menos la más importante

```
├── src
│   └── app
│       ├── app.component.html 
│       ├── app.component.ts 
│       ├── app.module.ts 
```

Lo que vamos hacer es mostrar una tabla de usuarios, y agregaremos un detalle de cada usuario.

## Los componentes

Se agregan dos componentes uno de usuarios y otro de detalle asi que la estructura de archivos queda de la siguiente forma

```terminal
├── app
│   ├── app.component.html
│   ├── app.component.ts
│   ├── app.module.ts
│   ├── detail
│   │   ├── detail.component.html
│   │   └── detail.component.ts
│   └── users
│       ├── users.component.html
│       └── users.component.ts
```

### El componente de usuarios

 En el archivo **_/app/users/users.component.ts_** agregamos una colección de datos de usuarios este lo puedes obtener del sitio [JSONPlaceholder](http://jsonplaceholder.typicode.com/users)

```javascript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  users: any=[
    {
      "id": 1,
      "name": "Leanne Graham",
      "username": "Bret",
      "email": "Sincere@april.biz",
      "address": {
        "street": "Kulas Light",
        "suite": "Apt. 556",
        "city": "Gwenborough",
        "zipcode": "92998-3874",
        "geo": {
          "lat": "-37.3159",
          "lng": "81.1496"
        }
      },
      "phone": "1-770-736-8031 x56442",
      "website": "hildegard.org",
      "company": {
        "name": "Romaguera-Crona",
        "catchPhrase": "Multi-layered client-server neural-net",
        "bs": "harness real-time e-markets"
      }
    }, ...
    
  ]
}

```

En el archivo **_/users/users.component.html_** y mostramos una tabla con  los usuarios

```html
  <h2>Users</h2>
  <table>
      <tr>
      <th>Id</th>
      <th>Name</th>
      </tr>
      <tr *ngFor="let user of users">
          <td><a routerLink="/detail/{ { user.id } }"> { { user.id } } </a></td>
          <td>{ { user.name } }</td>
      </tr>
  </table>
```

Lo importante de aquí es la siguiente línea `` <a routerLink="/detail/{{user.id}}"> { { user.id } } </a>`` la propiedad _routerLink_ se le pasa la ruta a la que vamos a llamar ``/detail`` y el parámetro id del usuario ``{ { user.id } }``

### El componente detalle

El archivo **_/app/detail/detail.component.ts_** vamos a recibir el parámetro **id** que nos ayudara a buscar dentro de nuestra colección de datos para mostrarlos dentro de la vista detalle

```typescript
import { Component } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-detail',
  templateUrl: './detail.component.html'
})
export class DetailComponent {
  users: any=[...]
  id= null;
  user = null;
  constructor(private route: ActivatedRoute ){
    this.id= this.route.snapshot.params['id'];
    this.user=(this.searchUser());
  } 
  searchUser(){
    return this.users.filter( (user)=>{ return user.id == this.id})[0] || null;
  } 
}
``` 

Esta línea ``this.id= this.route.snapshot.params['id'];`` nos ayuda a obtener el parámetro **id**

Con el método ``searchUser()`` mediante un arrow funtion extraemos la información del usuario 

El archivo **_/app/detail/detail.component.html_** solo mostramos la información del usuario

```html
<h2>Detail</h2>
<table>
    <tr>
        <td>Name</td><td>{{user.name}}</td>    
    </tr>
    <tr>
        <td>Email</td><td>{{user.email}}</td>
    </tr>
    <tr>
        <td>Phone</td><td>{{user.phone}}</td>
    </tr>
</table>
``` 

## Modificando el componente principal

Recordemos que por default había creado un componente, entonces hay que modificarlo para que active las rutas.

El archivo **_/app/app.component.html_** queda de la siguiente forma y lo mas importante es el tag **router-outlet**

```html
<div>
  <h1>Routes</h1>
  <router-outlet></router-outlet>
</div>
```

## Configurando la ruta

Dentro del archivo **_app/app.module.ts_**  realizamos lo siguiente

1. Agregamos la definición de las rutas en una variable **_appRoutes_** 
  ```javascript
  const appRoutes: Routes =[
  {path:'',component:UsersComponent},
  {path:'users',component:UsersComponent},
  {path:'detail/:id',component:DetailComponent}
  ]
  ```
2. agregamos la definicion en el **_@ngModule_** 
  ```javascript
  @NgModule({
    declarations: [
      UsersComponent,
      DetailComponent
    ],
    imports: [
        RouterModule.forRoot(appRoutes)
    ]    
  })
  ```

El archivo **_app/app.module.ts_** queda de la siguiente forma

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

import { Routes,RouterModule } from "@angular/router";

import {UsersComponent} from './users/users.component';
import {DetailComponent} from './detail/detail.component';

const appRoutes: Routes =[
  {path:'',component:UsersComponent},
  {path:'users',component:UsersComponent},
  {path:'detail/:id',component:DetailComponent}
]
@NgModule({
  declarations: [
    AppComponent,
    UsersComponent,
    DetailComponent,
  ],
  imports: [
    BrowserModule,
    RouterModule.forRoot(appRoutes)
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

## Y el resultado

1. Vista usuarios
![Angular vista usuarios]({{site.url}}/assets/images/2017_12_29_rutasAngular/ejemploAngularVista1.png)

2. Vista detalle
![Angular vista detalle de usuarios]({{site.url}}/assets/images/2017_12_29_rutasAngular/ejemploAngularVista2.png)

## ¿Donde está el codigo?

Quieres ver como funciona antes de escribir codigo, porque no, [aqui](https://github.com/saidmlx/angular-routes) te dejo el repo en git 
