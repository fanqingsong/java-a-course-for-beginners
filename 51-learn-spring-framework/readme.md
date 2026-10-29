# Spring Framework

Two Maven projects. `learn-spring-framework-01` creates beans by hand in a `@Configuration` class. `learn-spring-framework-02` lets component scan find them. The step notes under this directory (`01-Step03.md`, `02-Step01.md`, `step-by-step-code-changes/step-by-step-guide.md`) are the course's code dumps for the same examples.

## POJO, JavaBean, Spring Bean

`SpringBeanVsJavaBean` puts the three terms next to each other.

- A **POJO** is an ordinary class.
- A **JavaBean** has a public no-arg constructor, getters and setters, and is `Serializable`.
- A **Spring bean** is an object Spring creates and wires. It does not have to follow the JavaBean rules.

## 01: You Create the Objects

`App01GamingBasicJava` constructs the game and the runner:

```java
var game = new PacmanGame();
var gameRunner = new GameRunner(game);
gameRunner.run();
```

`GameRunner` depends on a `GamingConsole`. `MarioGame`, `SuperContraGame`, and `PacmanGame` implement that interface. Changing the game means changing the `new` call.

`GamingConfiguration` and `App03GamingSpringBeans` move that `new` into `@Bean` methods. Spring's `ApplicationContext` creates the objects and passes the game into `GameRunner`.

## 01: `@Bean`, `@Primary`, `@Qualifier`

`HelloWorldConfiguration` declares beans with `@Bean`. A method parameter is a dependency: Spring looks up a bean of that type and passes it in.

```java
@Bean
public Person person3Parameters(String name, int age, Address address3) {
    return new Person(name, age, address3);
}
```

When two `Address` beans exist, injection by type is ambiguous. `@Primary` marks the default. `@Qualifier("address3qualifier")` picks a specific bean. `Person` and `Address` are records, so the accessors and constructor are generated.

Launch the context, then ask it for a bean:

```java
try (var context = new AnnotationConfigApplicationContext(HelloWorldConfiguration.class)) {
    System.out.println(context.getBean("name"));
    System.out.println(context.getBean(Person.class));
}
```

## 02: Component Scan and Injection

`@Configuration` plus `@ComponentScan` registers every `@Component` in the scanned packages. Constructor parameters are injected. One constructor does not need `@Autowired`:

```java
@Component
class YourBusinessClass {
    public YourBusinessClass(Dependency1 dependency1, Dependency2 dependency2) {
        this.dependency1 = dependency1;
        this.dependency2 = dependency2;
    }
}
```

The commented setters in `DepInjectionLauncherApplication` are the setter-injection version of the same wiring.

`GamingAppLauncherApplication` scans the `game` package. `GameRunner` asks for a `GamingConsole` with `@Qualifier("SuperContraGameQualifier")`. `MarioGame` is `@Primary`. `SuperContraGame` carries the qualifier.

## 02: Stereotypes, Lazy, Scope, Lifecycle

| Example | What it shows |
| --- | --- |
| `c1` | `@Service` `BusinessCalculationService` depends on `DataService`. `MongoDbDataService` and `MySqlDataService` are `@Repository`. `@Primary` chooses Mongo. `findMax` streams the retrieved numbers. |
| `d1` | `@Lazy` delays creation of `ClassB` until something asks for it. |
| `e1` | The default scope is singleton. `@Scope(SCOPE_PROTOTYPE)` creates a new instance on every lookup. |
| `f1` | `@PostConstruct` runs after injection. `@PreDestroy` runs when the context closes. |
| `g1` | The same injection using the CDI annotations `@Named` and `@Inject`. |
| `h1` | The same context loaded from XML instead of annotations. |

## Run

Each module is a Maven project. From `learn-spring-framework-01` or `learn-spring-framework-02`:

```bash
mvn -q spring-boot:run
```

The launcher classes have their own `main` methods. Run the one for the example you are reading, for example `DepInjectionLauncherApplication` or `App02HelloWorldSpring`.
