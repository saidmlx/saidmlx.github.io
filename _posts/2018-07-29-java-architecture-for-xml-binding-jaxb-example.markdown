---
layout: post
title: Java Architecture for XML Binding (JAXB) Example
date: 2018-07-29 1:00:00 
categories: Java WebService
tags: Java WebService XML JABXB
---

Cuando se implementa un Web Service se utiliza otra API para convertir de forma automática de XML a objetos; esto es muy útil cuando la definición del Web service es cambiante.
JAXB está presente a partir de la versión 1.6 del JDK. 
En esta entrega se demostrará cómo pasar de un XML Schema a Objetos Java y Viceversa.
## Entorno de Trabajo
- Apache Maven 3.3.3
- JDK 1.8

## Crear el proyecto 
Se crea el proyecto Maven con el arquetipo **_maven-archetype-quickstart_** el cual creará un proyecto vacío
```terminal
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes
-DgroupId=online.saidmlx.example.webservice
-DarchetypeArtifactId=maven-archetype-quickstart 
-DarchetypeVersion=1.0
-DartifactId=jaxb-example 
-DinteractiveMode=false
```

## Configurar el _pom.xml_
Dentro del archivo de configuración hay que agregar el plugin **maven-jaxb2-plugin**.

El punto a resaltar aquí es que dentro del tag **_configuration_** se declara el directorio destino donde se depositaran las clases resultado.

```xml
<plugin>
  <groupId>org.jvnet.jaxb2.maven2</groupId>
    <artifactId>maven-jaxb2-plugin</artifactId>
    <version>0.14.0</version>
    <executions>
      <execution>
        <id>generate</id>
        <goals>
          <goal>generate</goal>
        </goals>
      </execution>
    </executions>
    <configuration>
      <generateDirectory>${project.basedir}/src/main/generated/</generateDirectory>
  </configuration>
</plugin>
```


## Declarando el XML Schema
La idea es tomar de la datos Abierta Northwind la tabla Products y crear un XML Schema dentro del directorio _src/main/resources/_.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
    targetNamespace="http://www.saidmlx.online/database/northwind/products"
    xmlns:saidmlx="http://www.saidmlx.online/database/northwind/products"
    elementFormDefault="qualified">
 
  <element name="products" type="saidmlx:Products"></element>
  <complexType name="Products">
    <sequence>
      <element name="product_name" type="string" minOccurs="1" maxOccurs="1" />
      <element name="supplier_id" type="int" minOccurs="1" maxOccurs="1" />
      <element name="category" type="saidmlx:Categories" minOccurs="1" maxOccurs="1" />
      <element name="quantity_per_unit" type="string" minOccurs="1" maxOccurs="1" />
      <element name="unit_price" type="float" minOccurs="1" maxOccurs="1" />
      <element name="units_in_stock" type="int" minOccurs="1" maxOccurs="1" />
      <element name="units_on_order" type="int" minOccurs="1" maxOccurs="1" />
      <element name="reorder_level"	type="int" minOccurs="1" maxOccurs="1" />
      <element name="discontinued" type="int" minOccurs="1" maxOccurs="1" />
    </sequence>
    <attribute name="product_id" type="saidmlx:ID"></attribute>
  </complexType>
  
  <simpleType name="ID">
    <restriction base="int">
      <pattern value="[0-9]*"></pattern>
    </restriction>
  </simpleType>

  <complexType name="Categories">
    <all>
      <element name="category_id" type="int" />
      <element name="category_name" type="string" />
      <element name="description" type="string" />  
    </all>
  </complexType> 
</schema>
```

## Generando los recursos
Para generar el XML Schema a objetos no es más que ejecutar el plugin con ayuda del Maven.
```terminal
 mvn generate-sources
```

Y con eso nos realizará la siguiente estructura de clases.
```terminal
src/main/generated/
├── META-INF
│   └── sun-jaxb.episode
├── online
│   └── saidmlx
│       └── database
│           └── northwind
│               └── products
│                   ├── Categories.java
│                   ├── ObjectFactory.java
│                   ├── Products.java
│                   └── package-info.java
└── org
    └── w3
        └── _2001
            └── xmlschema
                └── Adapter1.java

```

## Marshaling 
El concepto de Marshaling es el proceso de Serialización(Marshal) corresponde al proceso de convertir un objeto XML a objetos y Deserialización (Unmarshal) que es el proceso de convertir de XML a objeto.

Para ese proceso de Marshaling se escribe la clase **_online.saidmlx.database.northwind.marshalling.Demo_**
```java
package online.saidmlx.database.northwind.marshalling;
import java.io.StringReader;
import java.io.StringWriter;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBException;
import javax.xml.bind.Marshaller;
import javax.xml.bind.Unmarshaller;
import online.saidmlx.database.northwind.products.Categories;
import online.saidmlx.database.northwind.products.Products;

public class Demo {

  public static void main(String[] args) {
  try {
    //-- fill data
    Products products = new Products();
    products.setProductId(1);
    products.setProductName("Chai");
    products.setSupplierId(8);
    products.setCategory(new Categories());
    products.getCategory().setCategoryId(1);
    products.getCategory().setCategoryName("Beverages");
    products.getCategory().setDescription("Soft drinks, coffees, teas, beers, and ales");
    products.setQuantityPerUnit("10 boxes x 30 bags");
    products.setUnitPrice(18);
    products.setUnitsInStock(39);
    products.setUnitsOnOrder(0);
    products.setReorderLevel(10);
    products.setDiscontinued(1);

    //-- Marshaling
    JAXBContext context= JAXBContext.newInstance(Products.class);			
    Marshaller marshaler = context.createMarshaller();
    StringWriter writer = new StringWriter();
    marshaler.marshal(products, writer);
    System.out.println(writer.toString());

    //-- Unmarshalling
    Unmarshaller unmarshaler = context.createUnmarshaller();
    Products productsResult = (Products)unmarshaler.unmarshal(new StringReader(writer.toString()));
    System.out.print(productsResult.getCategory().getDescription());

  } catch (JAXBException e) {
    e.printStackTrace();
  }
  }

}

```

## Resultado
Y el proyecto está listo si ejecutamos la clase **_online.saidmlx.database.northwind.marshalling.Demo_** tendremos el resultado

### Marshaling
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<products product_id="1"
xmlns="http://www.saidmlx.online/database/northwind/products">
  <product_name>Chai</product_name>
  <supplier_id>8</supplier_id>
  <category>
    <category_id>1</category_id>
    <category_name>Beverages</category_name>
    <description>Soft drinks, coffees, teas, beers, and ales</description>
  </category>
  <quantity_per_unit>10 boxes x 30 bags</quantity_per_unit>
  <unit_price>18.0</unit_price>
  <units_in_stock>39</units_in_stock>
  <units_on_order>0</units_on_order>
  <reorder_level>10</reorder_level>
  <discontinued>1</discontinued>
</products>

```

### Unmarshaling
Que corresponde a la propiedad **_productsResult.getCategory().getDescription()_**
```Terminal
Soft drinks, coffees, teas, beers, and ales
```

___

> Si lo que quieres es el codigo [aqui](https://github.com/saidmlx/jaxb-example){:target="_blank"}  esta


## Documentación 

[Java Architecture for XML Binding (JAXB)](https://es.wikipedia.org/wiki/JAXB){:target="_blank"}

[Maven Quickstart Archetype
](https://maven.apache.org/archetypes/maven-archetype-quickstart/){:target="_blank"}

[JAXB2 Maven Plugin
](https://github.com/highsource/maven-jaxb2-plugin){:target="_blank"}
