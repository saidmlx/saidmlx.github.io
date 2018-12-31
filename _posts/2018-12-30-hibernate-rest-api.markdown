---
layout: post
title: Crear un REST API con Spring Boot, Hibernate y MySQL
date: 2018-12-30 12:00:00 
categories: Java Hibernate
tags: Java Hibernate REST-API SpringBoot MySQL
---

__Tabla de contenido__

- [Descripción del proyecto](## Descripción del proyecto)
- [Preparar la base de datos con Docker](## Preparar la base de datos con Docker)
- [Modelo Entidad Relación](## Modelo Entidad Relación)
- [Estructura de archivos](## Estructura de archivos)
- [Configuración de base de datos](## Configuración de base de datos)
- [Modelo](## Modelo)
- [El acceso a datos con el patron DAO (Data Access Object)](## El acceso a datos con el patron DAO (Data Access Object))
- [El servicio](## El servicio)
- [El controlador](## El controlador)


## Descripción del proyecto
En esta ocación crearemos una REST API con Hibernate MySQL

- Spring Boot : v1.5.18.RELEASE
- Hibernate : v5.0.12
- JDK : v1.8
- Mysql : v5.7

## Preparar la base de datos con Docker

Para la base de datos vamos a utilizar un contenedor que he creado en mi repositorio personal que puedes ver [aqui](https://cloud.docker.com/u/saidmlx/repository/docker/saidmlx/mysql-5-courses) 

### Ejecutar la imagen _saidmlx/mysql-5-courses_

Donde:
> -p __33060__:3306  
> Indicamos que el puerto a exponer sera el *33060*

> name **mysql-db**  
> Indicamos que la imagen se creara como __*mysql-db*__

> -e MYSQL_ROOT_PASSWORD=**secret**  
> Indicamos que la contraseña para __*root*__ sera __*secret*__

> -e MYSQL_DATABASE=**courses**  
    Indicamos que cree la base de datos con nombre __*courses*__

```bash
$ docker run -d -p 33060:3306 --name mysql-db -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=courses saidmlx/mysql-5-courses 
```
### Entrar en modo interactivo
Con el siguiente comando entramos de forma interactiva al contenedor y ejecutamos mysql

```bash
$ docker exec -it mysql-db mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.24 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use courses;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+----------------------+
| Tables_in_courses    |
+----------------------+
| course               |
| social_media         |
| teacher              |
| teacher_social_media |
+----------------------+
4 rows in set (0.00 sec)

mysql>
```
### Cargar el script en tu MySQL Local
Si aun le tienes miedo a Docker puedes cargar el siguiente escript de base de datos __*database.sql*__ in you local MySQL 5

Solo recuerda que debe ser la version 5 de lo contrario puede causarte problemas

## Modelo Entidad Relación

La acción anterior creara una base de datos lista para operar

<img src="{{site.url}}/assets/images/javaSpring/Diagrama-ER.png" alt="" style="width:50%" >

## Estructura de archivos

La estructura de carpetas y archivos
```bash
.
├── pom.xml
└── src
    └── main
        └── java
            └── online
                └── saidmlx
                    ├── AppApplication.java
                    └── app
                        ├── configuration
                        │   └── DataBaseConfiguration.java
                        ├── controller
                        │   ├── CourseController.java
                        │   ├── MainController.java
                        │   ├── SocialMediaController.java
                        │   └── TeacherController.java
                        ├── dao
                        │   ├── AbstractSession.java
                        │   ├── CourseDao.java
                        │   ├── CourseDaoImp.java
                        │   ├── SocialMediaDao.java
                        │   ├── SocialMediaDaoImp.java
                        │   ├── TeacherDao.java
                        │   ├── TeacherDaoImp.java
                        │   ├── TeacherSocialMediaDao.java
                        │   └── TeacherSocialMediaDaoImp.java
                        ├── model
                        │   ├── Course.java
                        │   ├── SocialMedia.java
                        │   ├── Teacher.java
                        │   └── TeacherSocialMedia.java
                        ├── service
                        │   ├── CourseService.java
                        │   ├── CourseServiceImp.java
                        │   ├── SocialMediaService.java
                        │   ├── SocialMediaServiceImp.java
                        │   ├── TeacherService.java
                        │   ├── TeacherServiceImp.java
                        │   ├── TeacherSocialMediaService.java
                        │   └── TeacherSocialMediaServiceImp.java
                        └── util
                            └── CustomErrorType.java
        
```

## Configuración de base de datos

Dentro del archivo online.saidmlx.app.configuration.__*DataBaseConfiguration*__

Creamos propiedades adicionales
```java
	public Properties hibernateProperties() {
		Properties properties = new Properties();
		properties.put("hibernate.dialect","org.hibernate.dialect.MySQLDialect");
		properties.put("hibernate.show_sql", "true");
		return properties;
	}
```

Definimos la fuente de datos donde indicamos tolos los datos de la base de datos
```java
@Bean
public DataSource dataSource() {
    DriverManagerDataSource dataSource= new DriverManagerDataSource();
    dataSource.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource.setUsername("root");
    dataSource.setPassword("secret");
    dataSource.setUrl("jdbc:mysql://localhost:33060/courses?autoReconnect=true&useSSL=false");
    return dataSource;
}
```

Creamos el Session factory e indicamos que donde tiene que buscar los paquetes es __*online.saidmlx.app.model*__

```java
@Bean
public LocalSessionFactoryBean sessionFactory() {
    LocalSessionFactoryBean localSessionFactoryBean = new LocalSessionFactoryBean();
    localSessionFactoryBean.setDataSource(dataSource());
    localSessionFactoryBean.setPackagesToScan("online.saidmlx.app.model");
    localSessionFactoryBean.setHibernateProperties(hibernateProperties());
    return localSessionFactoryBean;
}
```

Definimos el administrador de transacciones

```java
@Bean
@Autowired
public HibernateTransactionManager transactionManager () {
    HibernateTransactionManager hibernateTransactionManager = new HibernateTransactionManager();
    hibernateTransactionManager.setSessionFactory(sessionFactory().getObject());
    return hibernateTransactionManager;
}
```

## Modelo

Todo parte del modelo y aqui tenemos cuatro clases que modelaran el acceso a datos

### Cursos

online.saidmlx.app.model.__Course__

Deficición de la entidad
- __@Entity__ indicamos que va ser una clase de tipo entidad
- __@Table(name="course")__ aqui conectamos esta clase con la tabla de base de datos llamada __*course*__ 
- __@Column(name="****")__ para cada columna de la tabla se crea una propiedad dentro de la clase
- __@Id__ en caso de que la columna sea una PRIMARY KEY
- __@GeneratedValue(strategy=GenerationType.IDENTITY)__ en caso de que el ID se necesite generar de forma automatica

Relaciones 
Un curso es impartido por un profesor pero un profesor puede dar varios cursos por lo cual la relacion en de muchos a uno y eso se define con:

- __@ManyToOne( fetch=FetchType.EAGER)__ indicamos que la relación va de muchos a uno con acceso inmediato(Eager)
- __@JoinColumn(name="id_teacher")__ indicamos que la columna por la cual uniremos los profesores sera _*id_teacher*_

```java
@Entity
@Table(name="course")
public class Course implements Serializable {

	@Id
	@Column(name="id_course")
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Long idCourse;
	
	@Column(name="name")
	private String name;
	
	@Column(name="themes")
	private String themes;
	
	@Column(name="project")
	private String project;
	
	@ManyToOne( fetch=FetchType.EAGER)
	@JoinColumn(name="id_teacher")
	private Teacher teacher;
}
```

### Profesores

online.saidmlx.app.model.__Teacher__

Deficición de la entidad

- __@Entity__ indicamos que va ser una clase de tipo entidad
- __@Table(name="teacher")__ aqui conectamos esta clase con la tabla de base de datos llamada __*course*__ 
- __@Column(name="****")__ para cada columna de la tabla se crea una propiedad dentro de la clase
- __@Id__ en caso de que la columna sea una PRIMARY KEY
- __@GeneratedValue(strategy=GenerationType.IDENTITY)__ en caso de que el ID se necesite generar de forma automatica

Relaciones

- __@OneToMany(cascade=CascadeType.ALL, mappedBy="teacher")__
- __@JsonIgnore__ 
- __@OneToMany(fetch = FetchType.EAGER, cascade = CascadeType.ALL)__
- __@JoinColumn(name="id_teacher")__

```java
@Entity
@Table(name="teacher")
public class Teacher implements Serializable {

	@Id
	@Column(name="id_teacher")
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Long idTeacher;
	
	@Column(name="name")
	private String name;
	
	@Column(name="avatar")
	private String avatar;
	
	@OneToMany(cascade=CascadeType.ALL, mappedBy="teacher")
	@JsonIgnore
	private Set<Course> courses;

	@OneToMany(fetch = FetchType.EAGER, cascade = CascadeType.ALL)
	@JoinColumn(name="id_teacher")
	private Set<TeacherSocialMedia> teacherSocialMedia;
	
}

```

### Social Media

online.saidmlx.app.model.__SocialMedia__

Deficición de la entidad 

- __@Entity__
- __@Table(name="social_media")__
- __@Id__
- __@Column(name="***")__
- __@GeneratedValue(strategy=GenerationType.IDENTITY)__

Relaciones

- __@OneToMany__
- __@JoinColumn(name="id_social_media")__
- __@JsonIgnore__


```java
@Entity
@Table(name="social_media")
public class SocialMedia implements Serializable {
	
	@Id
	@Column(name="id_social_media")
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Long idSocialmedia;
	
	@Column(name="name")
	private String name;
	
	@Column(name="icon")
	private String icon;
	
	
	@OneToMany
	@JoinColumn(name="id_social_media")
	@JsonIgnore
	private Set<TeacherSocialMedia> teacherSocialMedia; 
}
```

### Teacher Social Media

online.saidmlx.app.model.__TeacherSocialMedia__

Deficición de la entidad 

- __@Entity__
- __@Table(name="teacher_social_media")__
- __@Id__
- __@Column(name="***")__
- __@GeneratedValue(strategy=GenerationType.IDENTITY)__

Relaciones

- __@ManyToOne(fetch=FetchType.EAGER)__
- __@JoinColumn(name="id_social_media")__


```java
@Entity
@Table(name="teacher_social_media")
public class TeacherSocialMedia implements Serializable {

	@Id
	@Column(name="id_teacher_social_media")
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Long idTeacherSocialMedia;
	
	@Column(name="nickname")
	private String nickname;
	
	@ManyToOne(fetch=FetchType.EAGER)
	@JoinColumn(name="id_teacher")
	@JsonIgnore
	private Teacher teacher;
	
	@ManyToOne(fetch=FetchType.EAGER)
	@JoinColumn(name="id_social_media")
	private SocialMedia socialMedia;

}
```

## El acceso a datos con el patron DAO (Data Access Object)

Para crear nuestro DAO creamos una clase abstracta que nos provea de un objeto de tipo Session

online.saidmlx.app.dao.__*AbstractSession*__

```java
public abstract class AbstractSession {

	@Autowired
	SessionFactory sessionFactory;
	
	protected Session getSession() {
		return sessionFactory.getCurrentSession();	
	}
}

```

Ahora para cada uno de los DAO heredamos de la clase __*AbstractSession*__

Para la generación de un DAO es necesario generar su interfaz y su implementación, donde la implementación hereda de la clase bastracta __*AbstractSession*__ para aceder al objeto de tipo __*Session*__ y dentro de cada clase de implementación hacemos uso del modelo definido en un paso anterior


```java
public interface CourseDao {
	public void saveCourse(Course course);
	public void deleteCourseById(Long idCourse);
	public void updateCourse(Course course);
	public List<Course> findAllCourse();
	public Course findById(Long idCourse);
	public Course findByName(String name);
	public List<Course> findByIdTeacher(Long idTeacher);
}
```

- __@Repository__ indicamos que es una clase de tipo repositorio
- __@Transactional__ con perfil transaccional


```java
@Repository
@Transactional
public class CourseDaoImp extends AbstractSession implements CourseDao {
	@Override
	public void saveCourse(Course course) {
		getSession().persist(course);
	}
	@Override
	public void deleteCourseById(Long idCourse) {
		Course course = findById(idCourse);
		if(course != null) {
			getSession().delete(course);
		}
	}
	@Override
	public void updateCourse(Course course) {
		getSession().update(course);
	}
	@Override
	public List<Course> findAllCourse() {
		return getSession().createQuery("from Course").list();
	}
	@Override
	public Course findById(Long idCourse) {
		return (Course)getSession().get(Course.class, idCourse);
	}
	@Override
	public Course findByName(String name) {
		return (Course)getSession().createQuery("from Course where name = :name").setParameter("name", name).uniqueResult();
	}
	@Override
	public List<Course> findByIdTeacher(Long idTeacher) {
		return (List<Course>) getSession().createQuery(" from Course c join c.teacher where idteacher = : idTeacher")
				.setParameter("idTeacher", idTeacher).list();
	}
}
```

## El servicio

Para cada entidad es necesario agregar un servicio definiendo interfaz e implementación y para cada clase de implementacion hacemos uso del DAO generado. Es por eso que injectamos via __*@Autowired*__ 

```java
public interface CourseService {
	public void saveCourse(Course course);
	public void deleteCourseById(Long idCourse);
	public void updateCourse(Course course);
	public List<Course> findAllCourse();
	public Course findById(Long idCourse);
	public Course findByName(String name);
	public List<Course> findByIdTeacher(Long idTeacher);
}
```

```java
@Service("courseService")
@Transactional
public class CourseServiceImp  implements CourseService  {
	@Autowired
	CourseDao courseDao;
	@Override
	public void saveCourse(Course course) {
		courseDao.saveCourse(course);
	}
	@Override
	public void deleteCourseById(Long idCourse) {
		courseDao.deleteCourseById(idCourse);	
	}
	@Override
	public void updateCourse(Course course) {
		courseDao.updateCourse(course);	
	}
	@Override
	public List<Course> findAllCourse() {
		return courseDao.findAllCourse();
	}
	@Override
	public Course findById(Long idCourse) {
		return courseDao.findById(idCourse);
	}
	@Override
	public Course findByName(String name) {
		return courseDao.findByName(name);
	}
	@Override
	public List<Course> findByIdTeacher(Long idTeacher) {
		return courseDao.findByIdTeacher(idTeacher);
	}
}
```

## El controlador

El controlador es el que expondra nuestro API

Para cada clase de tipo controller tenemos:
- __@Controller__ indicamos que es un controlador
- __@RequestMapping("/v1")__ con una peticion de entrada llamada __v1__
- __@Autowired CourseService courseService;__ y usamos el servicio Curso 

e internamente exponemos varias entradas 



- ``` {[/v1/courses],methods=[GET]}```  Obtenemos todos los cursos
- ``` {[/v1/course/{id}],methods=[GET]}```  Obtenemos un curso pasando su ID
- ``` {[/v1/course],methods=[POST]}```  Guardamos un curso pasando un objeto de tipo Course
- ``` {[/v1/course/{id}],methods=[PATCH]}```   Actualizamos un Curso
- ``` {[/v1/course/teacher],methods=[PATCH]}```  Asignamos un profesor a un curso
- ``` {[/v1/course/{id}],methods=[DELETE]}```  Eliminamos un curso

```java

@Controller
@RequestMapping("/v1")
public class CourseController {
	@Autowired
	CourseService courseService; 
	@Autowired
	TeacherService teacherService;
	//- courses
	@RequestMapping(value="/courses", method= RequestMethod.GET, headers="Accept= application/json")
	public ResponseEntity<List<Course>> getCourses( ) {
	}
	//-- GET course
	@RequestMapping(value="/course/{id}", method= RequestMethod.GET, headers="Accept= application/json")
	public ResponseEntity<Course> getCourse(@PathVariable("id") Long idCourse ) {
	}
	//-- POST course
	@RequestMapping(value="/course", method=RequestMethod.POST, headers="Accept= application/json")
	public ResponseEntity<String> saveCourse(@RequestBody Course course, UriComponentsBuilder uri) {
	}
	@RequestMapping(value="/course/teacher", method=RequestMethod.PATCH, headers="Accept= application/json")
	public ResponseEntity<?> addTeacher(@RequestBody Course course) {
	}
	//-- UPDATE course
	@RequestMapping(value="/course/{id}", method=RequestMethod.PATCH)
	public ResponseEntity<Course> updateCourse(@RequestBody Course course, @PathVariable("id") Long idCourse ) {
	}
	//-- DELETE course 
	@RequestMapping(value="/course/{id}", method=RequestMethod.DELETE)	
	public ResponseEntity<Course> deleteCourse( @PathVariable("id") Long idCourse ) {
	}	
}
```

## Probando nuestro API REST

De esta forma con una aplicación __*REST Cliente*__ podemos ver como funciona en este caso usamo Postman

<img src="{{site.url}}/assets/images/javaSpring/API-REST-Courses.png" alt="" style="width:50%" >

Si quieres ver el codigo de la API pudes descargarla del repositorio [aqui](https://github.com/saidmlx/simple-java-hibernate-api-platzi)


