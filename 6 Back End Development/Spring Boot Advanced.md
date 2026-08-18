## The Var Keywork

The `var` keyword allows a variable to be initialized without having to declare its type. The compiler will infer the type. The type of the variable has to be somewhat obvious. Var can be used for local variables, in loops, and in try catch.

Instead of initializing a variable like this.
```java
Pony pony = ponyRepository.findPonyByName(name);
```

You can do this.
```java
var pony = ponyRepository.findPonyByName(name);
```

---

## Records

Records are immutable data classes that require only the type and name of fields. Records have built in constructors, setters, getters, and methods like `toString`, `equals`, and `hashCode`. Records can't inherit from other classes because they inherit from `java.lang.Record`.
```java
public record Person(String name, String address) {}
```

The record automatically has a constructor called the canonical constructor, but you can also add logic to the canonical constructor. There is no need to declare the constructor arguments.
```java 
public record Person(String name, String address) {
	public Person {
		if (age < 0) {
			throw new IllegalArgumrntException("Age can't be negative");
		}
	}
}
```

You can also override existing auto generated methods you want to change.
```java 
public record Person(String name, String address) {
	@Override
	public String toString() {
		return String.format("Person", name, age);
	}
}
```

Records can have their own methods, however, these methods can’t change the state of the record.
```java
public record Person(String name, String address) {
	public boolean isAdult() {
		return age >= 18;
	}
}
```

This also means that records can implement Interfaces.

---

## Enums

An `enum` short for enumeration is a special class that represents a group of constants. Basically its like giving limited options for a variable. Enums only allow a fixed list of values that the IDE will present in autocomplete. Enums are also more readable. 

For example.
```java
enum Day {
	MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
	
	public boolean isWeekend() {
		return this == SUNDAY || this == SATURDAY;
	}
}
```

Or for example.
```java
enum Role {
	STUDENT, TEACHER, ADMINISTRATOR;
}
```

Using an `enum`.
```java
public class User {
	@Enumerated(EnumType.STRING)
	private Role role;
}
```

To use an `enum` in a database `sql` file you can just use text.
```sql
CREATE TABLE "user" (
	role TEXT NOT NULL
);
```

---

## Data Transfer Objects

A **DTO** is a **simple Java object** used to **transfer data** between different layers of an application, especially between your **back end** and the **front end** (or APIs).

Why use DTO’s  
- Data Hiding: DTOs allow you to expose only the necessary data to clients, hiding sensitive information or complex object structures.  
- Decoupling: They help separate the internal data model from the external API representation.  
- Performance: By transferring only required data, DTOs can improve network performance, especially in distributed systems. 
- Flexibility: You can tailor DTOs to specific use cases, combining data from multiple entities or omitting unnecessary fields.

DTOs are defined in the controller layer as separate classes. They act like a simpler form of model classes in the model layer.
```java
public record UserDTO(
	@NotNull
	Long course;
	@NotNull
	Long lecturer;
)
```

What happens.
- The controller takes a DTO as input.
- The validation happens on the DTO.
- The DTO is transferred to the service, where the translation is made to one or more models.
- A new DTO is sent back to the controller and ultimately to the client of the call (optional).

---

## Transactions

A transaction is a set of tasks which take place as a single, atomic, consistent, isolated, durable operation. By default, every database interaction (repository call) runs in a separate transaction. At the end of the transaction the results are committed to the database, meaning that they are made permanent in the database.

For example.
```java
@Transactional  
public Lecturer addLecturer(LecturerInput lecturerInput) {  
	User user = userService.signup(Role.LECTURER, lecturerInput.user());  
	return lecturerRepository.save(new Lecturer(lecturerInput.expertise(), user));
}
```

In Spring, you can add `@Transactional` to wrap all code from the start to the end of the annotated method in a transaction. The `@Transactional` annotation only works if your method is called from another class. In this example, a lecturer and user will always be committed together or not at all, this prevents dirty data.

Placing `@Transactional` at class level applies it to all public methods in the class. You can also  use it at method level to overwrite settings from the class level one. It is recommended to use it in the service layer as it is the layer right before objects are saved to the repository.

For example.
```java
@Service  
@Transactional  
public class LecturerService {}
```

There are places where it is not possible to use `@Transactional` due to technical reasons, like the `PostConstruct` method from the `DbInitializer`. In this case, you can use a programmatic transaction by auto wiring a `TransactionTemplate`.
```java
@PostConstruct  
public void init() {  
	transactionTemplate.executeWithoutResult((transactionStatus) -> {  
		clearAll();
		
		final var fullStack = courseRepository.save(new Course(  
			"Full-stack development",  
			"Learn how to build a full stack web application.",  
			2,  
			6  
		));
	}  
}
```

---

## Transaction Isolation Levels

Isolation levels define **how much one transaction is isolated from others**, specifically, how much one transaction can see changes made by another transaction that has not yet committed. Different isolation levels balance **consistency** vs **performance**.

There are mainly three problems isolation levels prevent.
- Dirty Read: A transaction reads uncommitted data from another transaction.
- Non-Repeatable Read: A row is read twice and gets different values because another transaction modified it.
- Phantom Read: A query returns a different number of rows the second time, another transaction inserted or deleted rows.

There are four isolation levels that can be used. The first two are the most useful.
- `READ_UNCOMMITTED` 
- `READ_COMMITTED`  
- `REPEATABLE_READ`  
- `SERIALIZABLE`

Lets start with `READ_UNCOMMITTED` which is the lowest isolation level, it allows dirty reads, basically the current transaction can see the result of another uncommitted unit of work.
```java
@Transactional(isolation = Isolation.READ_UNCOMMITTED)
```

Next `READ_COMMITTED` does not allow dirty reads, only the committed information can be accessed. It is the default strategy for most databases, but you can also specify it.
```java
@Transactional(isolation = Isolation.READ_COMMITTED)
```

The two other isolation levels have even higher isolation. `REPEATABLE_READ` prevents dirty reads and non-repeatable reads. Used when you want reads to be stable inside a transaction.
```java
@Transactional(isolation = Isolation.REPEATABLE_READ)
```

Finally `SERIALIZABLE` makes transactions behave as if they run one at a time. It prevents all anomalies which makes it the strongest but the slowest. The higher the isolation level, the lower the performance.
```java
@Transactional(isolation = Isolation.SERIALIZABLE)
```

---

## Propagation In Transactions

When one method calls another method and they are both transactions, propagation tells the method whether it should make a new transaction or continue in the old one.

There are seven levels of propagation but these are the most useful two.
- `REQUIRED`
- `REQUIRES_NEW`

Starting with `REQUIRED`, it basically tells the method, if there is a transaction, join it, if not, start a new one. It reuses the caller's transaction, and creates a new one if needed. This is the default and most common propagation.
```java
@Transactional(propagation = Propagation.REQUIRED)
```

Next `REQUIRES_NEW` basically tells the method, always start a new transaction, suspend the existing one. The caller's transaction is paused and the new one is its own fully independent transaction. When the new transaction is done, the caller's transaction is then un-paused. 
```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

---

## Entity Lifecycle

When you use `@Transactional`, Spring creates a **persistence context** which is also called the **Entity Manager context**. This is basically a **unit of work** that.
- Tracks entities.
- Manages dirty checking, detects changes.
- Synchronizes changes to the DB during **flush.**
- Ensures caching within the transaction, first-level cache.

When the transaction ends, the persistence context closes. These four states describe where an entity object is in relation to the persistence context.
- Transient: Unknown entity, will be saved as a new row in the DB on flush after being added with persist. 
- Managed: Entity is being tracked, its changes will be synced to the DB on flush.
- Removed: Entity to be deleted, will be removed from the DB on flush.
- Detached: Disconnected from the persistence context. After the persistence context closes, all entities become detached

Transient is a new object that is not known by JPA, it is just a normal Java object. JPA has no idea it exists. It is not associated with the database nor tracked.

Managed is a persistent object, so JPA knows it and is actively tracking it. A transient object becomes managed when it is saved to the database.

Removed is when the object is still managed but flagged for deletion. It will be deleted from the database when the persistence context flushes.

Detached means the object is no longer tracked by the persistence context. This happens when the transaction ends or the persistence context closes.

With knowledge of the persistence context, transactions and the entity lifecycle, we can fully explain the behavior of Spring Data JPA. We can simplify some operations by using cascades.

---

## Cascades 

We will focus on persist cascading, When you save (persist) a parent entity, JPA automatically saves its child entities too. You don’t need to manually persist each child. This is useful when entities have relationships.

Example of a domain model.
```java
@Entity
public class User {

    @Id @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(cascade = CascadeType.PERSIST)
    private List<Address> addresses = new ArrayList<>();

    // constructors, getters, setters
}
```

```java
@Entity
public class Address {

    @Id @GeneratedValue
    private Long id;

    private String street;

    // constructors, getters, setters
}
```

Now if we want to save without `PERSIST` we would have to save the children and then the parents in a specific order.
```java
User user = new User("Ali");

Address a = new Address("Street 1");
Address b = new Address("Street 2");

repository.save(a);
repository.save(b);

user.getAddresses().add(a);
user.getAddresses().add(b);

repository.save(user);

```

However if we want to save with `PERSIST` we only need to save the parent and not the children.
```java
User user = new User("Ali");

Address a = new Address("Street 1");
Address b = new Address("Street 2");

user.getAddresses().add(a);
user.getAddresses().add(b);

repository.save(user);

```

---

## Fetch Types

When you define a relationship between entities, like `@OneToOne`, `@OneToMany`, or `@ManyToOne`, you need to decide **when the related entity should be loaded**. This is where `FetchType` comes in. 

There are two fetch types. The first one is `EAGER` and it immediately retrieves the associated entity from the database. When you fetch the parent entity from the database, Hibernate automatically retrieves the related entity as well. This is used when you always need the associated entity. It simplifies access to the related entity; you don’t have to worry about it being unloaded. However, it can cause **performance issues** if the related entity is large or if there are many relationships, because it fetches everything even if you don’t use it.
```java
@Entity
public class User {
    @Id
    private Long id;

    @OneToOne(fetch = FetchType.EAGER)
    private Profile profile;  // Profile will be fetched immediately with User
}
```

If you query for a `User`.
```java
User user = userRepository.findById(1L).get();
Profile profile = user.getProfile(); // Already loaded
```

The second fetch type is `LAZY` and only retrieves the associated entity when it is accessed. Initially, only the parent entity is loaded. The related entity is fetched **on demand**, typically using a proxy object. It is used when the related entity is **not always needed**, which improves performance. However it can lead to **`LazyInitializationException`** if you try to access it **outside of a managed context**, like after the session/entity manager is closed.
```java
@Entity
public class User {
    @Id
    private Long id;

    @OneToOne(fetch = FetchType.LAZY)
    private Profile profile;  // Profile is fetched only when accessed
}
```

If you query for a `User`.
```java
User user = userRepository.findById(1L).get();
Profile profile = user.getProfile(); // Fetch happens here, not before
```

It is important to understand that `LAZY` loading only works if the entity is **managed** (still within the persistence context). For example, if you try to access a lazy property after the transaction is closed, you’ll get a `LazyInitializationException`.

**Default behavior:**
- `@OneToOne` is **EAGER** by default.
- `@ManyToOne` is **EAGER** by default.
- `@OneToMany` and `@ManyToMany` is **LAZY** by default.

---
## Orphan Removal 

Orphan Removal is an option, `orphanremoval`, that you can set on a relationship, like `@OneToOne` or `@OneToMany`, that automatically deletes a child entity if it is no longer referenced by its parent. This is useful when a child entity **cannot exist without its parent**.

It is important to know that **`orphanRemoval` is not the same as `cascade`**.
- `cascade = CascadeType.REMOVE` deletes the child **when the parent itself is deleted**.
- `orphanRemoval = true` deletes the child **when the child is no longer referenced by the parent**, even if the parent is not deleted.

It is also important to know that `orphanremoval` **works for `@OneToOne` and `@OneToMany` relationships**, not `@ManyToOne`.

For example. Removing an `Order` from `orders` list automatically deletes it from the database.
```java
@OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
private List<Order> orders = new ArrayList<>();
```

---

## Open API & Swagger

Open API is a specification or blueprint for building and documenting REST APIs. It defines endpoints, parameters, responses, and data models in a language agnostic way.

The Open API Specification defines a standard, language-agnostic interface to HTTP APIs which allows both humans and computers to discover and understand the capabilities of the service without access to source code, documentation, or through network traffic inspection. When properly defined, a consumer can understand and interact with the remote service with a minimal amount of implementation logic.

Swagger is a set of tools built around the Open API specification. Basically Swagger allows you to check how a back end functions using a user friendly user interface. You can find an example of this in action at [Swagger Pet Store Example](https://petstore.swagger.io/).

To implement swagger in a spring boot app, you need yo add the swagger dependency.
```pom
<dependency>  
	<groupId>org.springdoc</groupId>  
	<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>  
	<version>2.8.4</version>  
</dependency>
```

On the same level as the Controller, Service, and Repository directories, you can add a Config directory in which you can then make a `SwaggerConfig` class. This will help load and customize the swagger user interface.
```java
@Configuration  
public class SwaggerConfig {  
    @Bean  
    public OpenAPI customOpenAPI() {  
        return new OpenAPI()  
                .info(new Info()
		                .title("title")
		                .version("1.0.0")  
                        .description("API description"));  
    }  
}
```

You can then annotate your endpoints using several different annotation, but the most important two are `@Operation` and `@ApiResponse`. This also helps with customization.
```java
@Operation(summary = "Get course by Id")
@ApiResponse(responseCode = "200", description = "List of Courses")
@GetMapping("/{id}")
```

You can then view your user interface at [http://localhost:3000/swagger-ui/index.html](http://localhost:3000/swagger-ui/index.html).

---

## CORS Security 
  
**CORS, Cross Origin Resource Sharing**, is a browser security mechanism that controls whether a web application from one origin is allowed to access resources from another origin. By default, browsers block cross-origin HTTP requests due to the Same-Origin Policy.  

In Spring Boot, CORS is used to specify which origins, HTTP methods, and headers are permitted to access the backend API. When CORS is enabled, Spring Boot sends specific HTTP response headers that tell the browser the request is allowed.  

CORS mainly affects browser-based requests and does not block tools like Postman or curl.  
It is commonly needed when a frontend and backend run on different domains or ports.

Add the `@CrossOrigin` annotation to every controller class.
```java
@RestController
@RequestMapping("/courses")
@CrossOrigin(origins = "http://localhost:8080")
public class LecturerController {}
```

Then add the CORS Configuration.
```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
            .allowedOrigins("http://localhost:3000")
            .allowedMethods("GET", "POST", "PUT", "DELETE")
            .allowedHeaders("*");
    }
}
```

---

## Login Session Security

It is very important for the connection between our front end and back end to be secure. This document will contain the three most important security measures.
- **User Signup**: Transfer and store a password safely.
- **Authentication**: Verify a user's identity.
- **Authorization**: Check a user's permissions.

### User Signup

It should be clear that a password should never be stored as plain text. Storing a password in a database can be very risky due to data breaches. The solution is to store the password as a one way computed **Hash**. Hashing is only one sided, it is not reversable, on the other hand, Encrypting is reversable so the password can be cracked. That is why we use Hashing to store passwords in a database and not Encryption.

We need two new dependencies in our pom file in order to use the spring boot security feature and configure the application as an `OAuth 2` Resource Server. `OAuth 2` is the industry-standard protocol for authorization, enabling secure access to resources without sharing credentials.
```pom
<dependency>  
	<groupId>org.springframework.boot</groupId>  
	<artifactId>spring-boot-starter-oauth2-resource-server</artifactId>  
</dependency>  
<dependency>  
	<groupId>org.springframework.boot</groupId>  
	<artifactId>spring-boot-starter-security</artifactId>  
</dependency>
```

The **signup flow** goes as follows.
- **Step 1**: The client calls `POST /users/signup { username, password }`.
- **Step 2**: The backend checks if the user already exists and if the input is valid. Then it Hashes the password using `BCrypt`.
- **Step 3**: Save user and return a success message.

### Authentication

Authentication used for user login, so after a user is signed up and their credentials are safely stored in the database, they need to be able to log back in.

The **login flow** goes as follows.
- Step 1: The client calls `POST /users/login { username, password }`.
- Step 2: The backend looks up the user, hashes the incoming password, compares the hash with the one in the backend with `BCrypt`. If the hashes match the backend generates a JWT token.
- Step 3: The backend returns the user's name, role, and token. The token is stored as an `HttpOnly` cookie which is more secure than `localStorage`.

In Spring Security, a token is generated at login to prove that the user has been successfully authenticated. After verifying the username and password, the backend creates a JWT Token and sends it to the Front End in an HTTP response so the user does not need to send their credentials with every request, they just have to send the token.

A JWT has **three parts**, a header, a payload, and a signature. The user's name and role are stored inside the payload, allowing the backend to authorize requests without querying the database each time.  JWT-based authentication is **stateless**, meaning the server does not store session data and can scale more easily.  

The client must include the token with every request so the backend can verify who the user is.  
Storing the token in an `HttpOnly` cookie improves security by preventing JavaScript access and reducing XSS attacks.

A logout endpoint exists so the backend can instruct the browser to remove the authentication token. Since JWT authentication is stateless, the backend cannot log out a user by itself and must explicitly clear the token stored in the client, usually by deleting the `HttpOnly` cookie.

### Authorization

After a user is logged in, you need to assign what they can and can not do based on their role. When they make an API request with their token, you should verify their role to make sure they are allowed to make this API request. Make sure to use the annotation `@EnableMethodSecurity` in the security configuration file.

An annotation is added above each endpoint like this.
```java
@PostMapping
@PreAuthorize("hasRole('ADMIN')")
public student addStudent(@RequestBody StudentInput studentInput) {}
```

---
