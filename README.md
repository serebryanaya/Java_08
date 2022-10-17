# Day 08 – Piscine Java
### Spring

### Exercise 00 – Spring Context

Exercise 00: Spring Context ||
---|---
Turn-in directory |	ex00
Files to turn-in |	Spring-folder

Let's implement a loosely-coupled system comprised of a set of components (beans) and compliant with IoC/DI principles.

Let's assume there is Printer interface designed to display a specific message.

This class has two implementations: PrinterWithDateTimeImpl and PrinterWithPrefixImpl. The first class outputs messages by specifying output date/time using LocalDateTime, while the second class can be used to set a text prefix for a message.

In turn, both Printer implementations have a dependency on Renderer interface that sends messages onto the console. Renderer also has two implementations: RendererStandardImpl (outputs a message via standard System.out) and RendererErrImpl (outputs messages via System.err).

Renderer also has a dependency on PreProcessor interface that pre-processes messages. Implementation of PreProcessorToUpperImpl translates all letters into upper case, while implementation of PreProcessorToLower translates all letters into lower case.

UML diagram of classes is shown below:

![Diagram of classes](misc/images/Diagram.png)

An example of code using these classes in a standard way:
```java
public class Main {
   public static void main(String[] args) {
       PreProcessor preProcessor = new PreProcessorToUpperImpl();
       Renderer renderer = new RendererErrImpl(preProcessor);
       PrinterWithPrefixImpl printer = new PrinterWithPrefixImpl(renderer);
       printer.setPrefix("Prefix");
       printer.print("Hello!");
   }
}
```
Running this code will deliver the following result:

```
PREFIX HELLO!
```
You need to describe context.xml file for Spring, where all settings for each component and links between them will be specified.

Using these components with Spring looks as follows:
```java
public class Main {
   public static void main(String[] args) {
       ApplicationContext context = new ClassPathXmlApplicationContext("context.xml");
       Printer printer = context.getBean(“printerWithPrefix”, Printer.class);
       printer.print("Hello!");
   }
}
```

### Exercise 01 – JdbcTemplate

Exercise 01: JdbcTemplate ||
---|---
Turn-in directory |	ex01
Files to turn-in |	Service-folder

JdbcTemplate and its extension NamedParameterJdbcTemplate are convenient mechanisms for working with databases. These classes allow to eliminate writing template code for query execution and processing, as well as the need to intercept exceptions under check.

In addition, they provide a convenient RowMapper concept for ResultSet processing and converting resulting tables into objects.

Now, you need to implement the User model with the following fields:
- Identifier
- Email

You also need to implement `CrudRepository<T>` interface with the following methods:
- `Optional<T>` findById(Long id)
- `List<T>` findAll()
- void save(T entity)
- void update(T entity)
- void delete(Long id)

UsersRepository interface declared as UsersRepository extends CrudRepository<User> shall contain the following method:
- `Optional<T>` findByEmail(String email)

In addition, two implementations of UsersRepository are required:<br> UsersRepositoryJdbcImpl (uses standard Statements meachanisms) and UsersRepositoryJdbcTemplateImpl (is based on JdbcTemaplte/NamedParameterJdbcTemaple). Both classes shall accept DataSource object as a constructor argument.

In context.xml file, beans shall be declared for both repository types with different identifiers, as well as two beans of DataSource type: DriverManagerDataSource and HikariDataSource.

In addition, data for connecting to DB shall be specified in db.properties file and included in context.xml using `${db.url}`  placeholders.

Example of db.properties:
```
db.url=jdbc:postgresql://localhost:5432/database
db.user=postgres
db.password=qwerty007
db.driver.name=org.postgresql.Driver
```

In Main class, operation of findAll method shall be demonstrated using both repositories:
```
ApplicationContext context = new ClassPathXmlApplicationContext("context.xml");
UsersRepository usersRepository = context.getBean("usersRepositoryJdbc", UsersRepository.class);
System.out.println(usersRepository.findAll());
usersRepository = context.getBean("usersRepositoryJdbcTemplate", UsersRepository.class);
System.out.println(usersRepository.findAll());
```

**Project structure**:
- Service
    - src
        - main
            - java
                - school21.spring.service
                    - models
                        - User
                    - repositories
                        - CrudRepository
                        - UsersRepository
                        - UsersRepositoryJdbcImpl
                        - UsersRepositoryJdbcTemplateImpl
                    - application
                        - Main
            - resources
                -	db.properties
                -	context.xml
    -	pom.xml
