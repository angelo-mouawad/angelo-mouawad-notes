# What Is Spring Boot

The full set of operations that a backend provides is called the **The Application Programming Interface** (API). The backend communicates with the frontend using the **Hyper Text Transfer Protocol** (HTTP).

Spring Boot is a backend framework that helps with typical backend functionality. It takes full control of your application. Spring boot works together with maven which is a build automation and dependency management tool for java projects. One of the useful things maven does is that it gives you a folder structure to start with.

Your backend is mainly divided into four directories.
1. Controller: Files in the controller act like the door to the backend, The controller talks to browsers and postman. Endpoints are defined in the controller,
2. Service: Files in the service hold the logic like rules, calculations, and validation. The service gives commands to the repository to store or receive data.
3. Repository: Files in the repository communicate with the database.
4. Model: Files in the model folder represent you application classes. The building blocks of your application.

---

## The POM File

POM is short for **Project Object Model** and acts as your project's blueprint. The `pom.xml` file contains all the dependencies for your project starting from the most obvious one, Spring boot. It is located on the same level as the `src` directory.
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.4.1</version>
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```

---

## The Spring Boot Folder Structure

This is the recommended folder structure that should be used and is generated when using maven and spring boot.
```
src
├── main
|	├── java
|	|	├── Controller
|	|	|	└── PonyController
|	|	|	
|	|	├── Model
|	|	|	└── Pony
|	|	|
|	|	├── Repository
|	|	|	└── PonyRepository
|	|	|
|	|	├── Service
|	|	|	└── PonyService
|	|	|
|	|	└── Application
|	|	
|	└── resources
|		├── application.properties
|		└── schema.sql
|	
└── test
	└── java
		├── PonyTest
		├── PonyServiceTest
		└── IntegrationTest
```

---
## The Spring Boot Application

The Application file runs your whole backend, it converts your application to a spring boot backend where spring has full control.
```java
@SpringBootApplication
public class Application {
	public static void main(string[]args) {
		SpringApplication.run(Application.class, args);
	}
}
```

When running your spring boot application you can go to [localhost](http://localhost:3000) to access your backend from your browser.

---

## JPA and Databases

There are three ways to link back end code to a database, JDBC, JPA, and Spring Boot JPA. JDBC stands for **Java Data Base Connectivity** and is the manual low level way of connecting a back end to a database, it requires you to manually write queries in the repository layer. On the other hand, JPA which stands for **Java Persistence API** is the more modern, automated and high level way of connecting a back end to a database. JPA alone still requires some level of query writing, however when you pair it with the Spring Boot Framework, it does not require you to manually write any queries.

Spring Boot JPA translates object operations to database queries behind the scenes. The mapping of Java objects to data base tables is called ORM which stands for **Object Relational Mapping**. An ORM library works as a bride between a Java application (classes and objects) and a relational database (tables and records). The purpose of an ORM library is to automatically sync operations done on Java objects to the database.

Spring Boot JPA can be used with many databases, an example of an in memory database is an `H2` database, it runs and stops with the spring boot application. The database looses all its data when the application is stopped.

Dependencies in `pom.xml`.
```xml
<dependencies>
	<dependency>
		<groupId>com.h2database</groupId>
		<artifactId>h2</artifactId>
	</dependency>
</dependencies>
```

Properties in `application.properties`.
```
spring.datasource.url=jdbc:h2:mem:pony  
spring.datasource.username=user  
spring.datasource.password=password  
  
spring.h2.console.enabled=true  
spring.h2.console.path=/h2  
  
spring.sql.init.mode=always  
  
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect  
spring.jpa.hibernate.ddl-auto=none  
spring.jpa.show-sql=true
```

JPA can be also used with `postgresql` which is an actual database. Using an actual database is more useful than an in memory database.

Dependencies in `pom.xml`.
```xml
<dependency>  
    <groupId>org.postgresql</groupId>  
    <artifactId>postgresql</artifactId>  
    <version>42.7.3</version>  
</dependency>
```

Properties in `application.properties`.
```
spring.application.name=name  
server.port=3000  
  
spring.jpa.hibernate.ddl-auto=create-drop  
spring.jpa.show-sql=true  
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect  
spring.sql.init.mode=always  
  
spring.datasource.url=jdbc:postgresql://localhost:5432/name 
spring.datasource.username=username  
spring.datasource.password=password
```

---
## Model Directory

The model directory contains all the domain model classes. Here is where Hibernate Validation occurs. Hibernate validator is a validation framework that is very useful for simple validation.
```java 
@Entity
@Table(name = "pony")  
public class Pony {  
  
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private Long id;  
  
    @Column(name = "name") 
    @NotBlank(message = "Name may not be empty")
    private String name;  
  
    @Column(name = "age")
    @Positive(message = "Age must be positive")
    private int age;  
  
    // Database constructor  
    protected Pony() {}  
  
    // Normal constructor  
    public Pony(String name, int age) {  
        this.name = name;  
        this.age = age;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public String getName() {  
        return name;  
    }  
  
    public void setAge(int age) {  
        this.age = age;  
    }  
  
    public int getAge() {  
        return age;  
    }
}
```

Explanation of the headers.
- `@Entity` Helps the database work with this class, it will search for a table that corresponds to this class .
- `@Table` Specifies the table name in the database which would be the class name by default.
- `@Id` and `@GeneratedValue` Generate an id for each instance of the class.
- `@Column`) Specifies the column name which would be the field's name by default. When relating code to a database, fields translate to columns.
- Adding `@Transient` to fields says that this field should not be mapped in the database.

Hibernate validator dependencies in `pom.xml`.
```xml
<dependency>  
    <groupId>org.hibernate.validator</groupId>  
    <artifactId>hibernate-validator</artifactId>  
</dependency>
```

Hibernate validator headers.
- `@NotNull` validates that the annotated property value isn’t null.
- `@AssertTrue` validates that the annotated property value is true.
- `@Size` validates that the annotated property value has a size between the attributes min and max. We can apply it to String, Collection, Map, and array properties.
- `@Min` validates that the annotated property has a value no smaller than the value attribute, for example @Min(value = 40, message = "pony must be larger than 40 cm").
- `@Max` validates that the annotated property has a value no larger than the value attribute, for example @Max(value = 147, message = "pony must be smaller than 147 cm").
- `@Email` validates that the annotated property is a valid email address.
- `@NotEmpty` validates that the property isn’t null or empty. We can apply it to String, Collection, Map or Array values.  
- `@NotBlank` can be applied only to text values, and validates that the property isn’t null or whitespace.  
- `@Positive` apply to numeric values, and validate that they’re strictly positive, or positive including 0.
- `@Negative` apply to numeric values, and validate that they’re strictly negative, or negative including 0.

In the controller when taking an input, `@Valid` should be used to tell spring to check is the value of the input is valid using hibernate validator in the relevant model class. If the validation fails a `MethodArgumentNotValidException` is thrown and can be handled in the controller to return code 400 with a message instead of 500 with an error.

Database relationship in `schema.sql` for `h2` database.
```sql
CREATE TABLE PONY (  
    ID INT PRIMARY KEY AUTO_INCREMENT,  
    NAME VARCHAR(255) NOT NULL,  
    AGE INT
);
```

Database relationship in `schema.sql` for `postgresql` database.
```sql
CREATE TABLE PONY (  
    ID BIGSERIAL PRIMARY KEY,  
    NAME VARCHAR(255) NOT NULL,  
    AGE SERIAL
);
```

---

## Controller

The controller files are created in the controller directory and act as layer 1 of our layered structure.
```java
@RestController
@RequestMapping("/pony")  
public class PonyController {  
  
    private PonyService ponyService;  
  
	@Autowired
    public PonyController(PonyService ponyService) {  
        this.ponyService = ponyService;  
    }
  
    // GET Request /localhost:8080/pony  
    @GetMapping  
    public List<Pony> allPonies() {  
        return ponyService.allPonies();  
    }  
  
    // GET Specific variable (way 1) /localhost:8080/pony/Bella  
    @GetMapping("/{name}")  
    public Pony getPonyByName(@PathVariable String name) {  
        return ponyService.getPonyByName(name);  
    }  
  
    // GET Specific variable (way 1) /localhost:8080/pony/above/10  
    @GetMapping("/above/{age}")  
    public List<Pony> getPoniesAboveAge(@PathVariable int age) {
        return ponyService.getPoniesAboveAge(age);  
    }  
  
    // GET Specific variable (way 2) /localhost:8080/pony/between?min=3&max=7  
    @GetMapping("/between")  
    public List<Pony> getPoniesBetweenAges(@RequestParam int min,
	@RequestParam int max) {  
        return ponyService.getPoniesBetweenAges(min, max);  
    }  
  
    // POST Request /localhost:8080/pony  
    @PostMapping  
    public Pony addPony(@Valid @RequestBody Pony pony) {  
        return ponyService.addPony(pony);  
    }  
  
    // PUT Request /localhost:8080/pony/Bella
    @PutMapping("/{name}")  
    public void updatePony(@PathVariable String name,
    @Valid @RequestBody Pony updatedPony) {  
        ponyService.updatePony(name, updatedPony);  
    }  
  
    // DELETE Request / localhost:8080/pony/Bella
    @DeleteMapping("/{name}")  
    public void removePony(@PathVariable String name)
        ponyService.removePony(name);  
    }  
  
    // One to One endpoint to add an owner to a pony  
    @PostMapping("/{name}/owner")  
    public Pony addOwner(@PathVariable String name,
    @Valid @RequestBody Owner owner) {  
        return ponyService.addOwner(name, owner);  
    }  
  
    // Error handling for RuntimeException
    @ResponseStatus(HttpStatus.BAD_REQUEST)  
    @ExceptionHandler({RuntimeException.class})  
    public Map<String, String> handleRuntimeException(RuntimeException e) {  
        Map<String, String> errors = new HashMap<>();  
        errors.put("error", e.getMessage());  
        return errors;  
    }  
  
    // Error handling for IllegalArgumentException
    @ResponseStatus(HttpStatus.BAD_REQUEST)  
    @ExceptionHandler(IllegalArgumentException.class)  
    public Map<String, String> handleIllegalArgument(IllegalArgumentException e) {  
        Map<String, String> errors = new HashMap<>();  
        errors.put("error", e.getMessage());  
        return errors;  
    }  
  
    // Error handling for Hibernate Validator input validation  
    @ResponseStatus(HttpStatus.BAD_REQUEST)  
    @ExceptionHandler({MethodArgumentNotValidException.class})  
    public Map<String, String> handleValid(MethodArgumentNotValidException e) {  
        Map<String, String> errors = new HashMap<>();  
        for (FieldError error : e.getFieldErrors()) {  
            String fieldName = error.getField();  
            String errorMessage = error.getDefaultMessage();  
            errors.put(fieldName, errorMessage);  
        }  
        return errors;  
    }  
}
```

Explanation of the headers.
- `@RestController` Tells spring this is a controller so dependencies should be injected and JSON should be returned.
- `@Autowired` Tells spring to inject the class, however in newer versions this is no longer needed.

HTTP requests and Reponses consist of a header and a body. The header consists of key value pairs with meta data. And the body contains the actual data of the request or response in JSON format as objects or arrays.

---

## Service

The service files are created in the service directory and act as layer 2 of our layered structure.
```java
@Service  
public class PonyService {  
  
    private PonyRepository ponyRepository;  
    private OwnerRepository ownerRepository;  
  
	@Autowired
    public PonyService(PonyRepository ponyRepository,
    OwnerRepository ownerRepository) {  
        this.ponyRepository = ponyRepository;  
        this.ownerRepository = ownerRepository;  
    }  
  
    // GET Requests section  
    public List<Pony> allPonies() {  
        return ponyRepository.findAll(); // Built in JPA method  
    }  
  
    public Pony getPonyByName(String name) {  
        return ponyRepository.findByName(name); // Redirects to repo 
    }  
  
    public List<Pony> getPoniesAboveAge(int age) {  
        if (age < 0) {  
            throw new IllegalArgumentException("Age can't be less than 0");  
        }  
        return ponyRepository.findByAgeGreaterThan(age); // Redirects to repo
    }  
  
    public List<Pony> getPoniesBetweenAges(int min, int max) {  
        if (min < 0) {  
            throw new IllegalArgumentException("Age can't be less than 0");  
        } if (max > 100) {  
            throw new IllegalArgumentException("Age can't be greater than 100");  
        }  
        return ponyRepository.findByAgeBetween(min, max); // Redirects to repo
    }  
  
    // POST Requests section  
    public Pony addPony(Pony pony) {  
        return ponyRepository.save(pony); // Built in JPA method  
    }  
  
    // PUT Requests section  
    public void updatePony(String name, Pony updatedPony) {  
        Pony pony = getPonyByName(name);  
        pony.setAge(updatedPony.getAge());  
        ponyRepository.save(pony); // Built in JPA method  
    }  
  
    // DELETE Requests section  
    public void removePony(String name) {  
        Pony pony = getPonyByName(name);  
        ponyRepository.delete(pony); // Built in JPA method  
    }  
}```

Explanation of the headers.
- `@Service` Tells spring this is a service so dependencies should be injected.
- `@Autowired` Tells spring to inject the class, however in newer versions this is no longer needed.

JPA repositories have built in methods that do not need to be hand written in the repository.
- `findAll`  
- `save`  
- `findById`  
- `delete`  
- `count`  
  
JPA also offers self building prefixes that can be used to build your own methods.
- `find`  
- `count`  
- `top`  
- `first`  
- `startingWith`  
- `containing`  
- `equals`  
- `isNot`  
- `greaterThan`

---

## Repository

The Repo files are created in the repo directory and act as layer 3 of our layered structure.
```java
@Repository  
public interface PonyRepository extends JpaRepository<Pony, Long> {  
    Pony findByName(String name);  
  
    List<Pony> findByAgeGreaterThan(int age);  
  
    List<Pony> findByAgeBetween(int min, int max);  
}
```

Explanation of the headers.
- `@Repository` Tells spring to generate the query logic for the repository at runtime based on the method names.

---

## Relationships

### One To One

### One To Many 

### Many To Many

---