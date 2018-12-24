---
layout: post
title: El patrón de diseño Adapter
date: 2000-01-01 12:00:00 
categories: Java Patherns
tags: Java Patherns
---


El __*patrón de diseño adapter*__ es un de tipo interfaz y su implementación resuelve el problema de agregar una nueva implementación ha un código ya escrito.

El ejemplo más claro es cuando quiere cambiar un proveedor de servicios, imagina que tienes un contrato con la empresa X pero la empresa Y te da mejores beneficios, cambiar todo el desarrollo agregaría muchas horas de trabajo por lo cual generas una nueva interfaz y para heredar funcionalidad ya sea por clase o por objeto de la siguiente forma.

Tenemos la siguiente implementación que parecería normal en muchos lugares; una clase interfaz __PayOne__ y su implementación en la clase __PayOneImp__.
```java
public interface PayOne {
	public void methodOne();
}
```

```java
public class PayOneImp implements PayOne {
	public void methodOne() {
		System.out.println("methodOne()");
	}
}
```

```java
public class RunAdapterExample {
	public static void main(String[] args) {
		//-- First Implementation
		System.out.println("First Implementation");
		PayOne payOne = new  PayOneImp();
		payOne.methodOne();
	}
}
```

## Implementación adapter

La implementación del adapter puede ser por clase o por objeto 

Para empezar hay que agregar una nueva interfaz __PayTwo__ 

```java
public interface PayTwo {
	public void methodOne_();
	public void methodTwo();
}
```

### Implemetación por clase

En este tipo de implementación la clase __*hereda*__ de __PayOneImp__ e implementa la interfaz __PayTwo__

```java
public class PayTwoImpAdapterByClass extends PayOneImp implements PayTwo {
	public void methodTwo() {
		System.out.println("methodTwo()");
	}
	public void methodOne_() {
		this.methodOne();
	}
}
```

```java
public class RunAdapterExample {
	public static void main(String[] args) {
		//-- Implementation Adapter By Class
		System.out.println("Adapter Implementation by class");
		PayTwo payTwoByClass = new PayTwoImpAdapterByClass();
		payTwoByClass.methodOne_();
		payTwoByClass.methodTwo();
	}
}
```

### Implementación por objeto

La implementación por objeto se __*instancia*__ de la clase __PayOneImp__ y se implementa de la interfaz __PayTwo__

```java
public class PayTwoImpAdapterByObject implements PayTwo {
	private PayOne payOne;
	public PayTwoImpAdapterByObject(){
		this.payOne = new PayOneImp();
	}
	public void methodOne_() {
		this.payOne.methodOne();
	}
	public void methodTwo() {
		System.out.println("methodTwo()");
	}
}
```

```java
public class RunAdapterExample {
	public static void main(String[] args) {
		//-- Implementation By Object
		System.out.println("Adapter Implementation by object");
		PayTwo payTwoByObject = new PayTwoImpAdapterByObject();
		payTwoByObject.methodOne_();
		payTwoByObject.methodTwo();
	}
}
```

Y de esta forma implementamos en dos formas el patrón Adapter
