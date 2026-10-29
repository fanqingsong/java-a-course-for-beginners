# Spring Boot: Course API

A small REST API for courses. Spring Boot starts the server, Spring MVC maps HTTP methods, and Spring Data JPA stores `Course` rows. `1.md` is the H2 version of this project. `2.md` is the MySQL version. The code currently matches `2.md`.

## Layers

`Course` is a JPA entity. `@Id` and `@GeneratedValue` make `id` the generated primary key. The no-arg constructor is required by JPA.

```java
@Entity
public class Course {
    @Id
    @GeneratedValue
    private long id;
    private String name;
    private String author;
}
```

`CourseRepository` is an interface. Extending `JpaRepository<Course, Long>` supplies `findAll`, `findById`, `save`, and `deleteById`. There is no implementation class in this project.

`CourseController` is a `@RestController`. `CourseRepository` is injected with `@Autowired`.

| Method | Path | Action |
| --- | --- | --- |
| GET | `/courses` | `findAll` |
| GET | `/courses/{id}` | `findById`; throws if the id is missing |
| POST | `/courses` | `save` the JSON body |
| PUT | `/courses/{id}` | `save` the JSON body |
| DELETE | `/courses/{id}` | `deleteById` |

```bash
curl http://localhost:8080/courses
curl -X POST http://localhost:8080/courses \
  -H 'Content-Type: application/json' \
  -d '{"name":"Learn DevOps","author":"in28minutes"}'
```

## Database

`application.properties` points at MySQL and lets Hibernate update the schema:

```properties
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://localhost:3306/courses
spring.datasource.username=courses-user
spring.datasource.password=dummycourses
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL57Dialect
```

The commented command in `CourseController` starts that database:

```bash
docker run --detach \
  --env MYSQL_ROOT_PASSWORD=dummypassword \
  --env MYSQL_USER=courses-user \
  --env MYSQL_PASSWORD=dummycourses \
  --env MYSQL_DATABASE=courses \
  --name mysql \
  --publish 3306:3306 mysql:5.7
```

Actuator exposes `health` and `metrics` at `/actuator`.

To use the in-memory H2 database from `1.md` instead, swap the MySQL driver for `com.h2database:h2` in `pom.xml` and point `spring.datasource.url` at `jdbc:h2:mem:testdb`.

## Run

From `52-learn-spring-boot`, with MySQL listening on port 3306:

```bash
mvn spring-boot:run
```

The API listens on port 8080.
