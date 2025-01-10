# Dependency Injection 

## What is dependency injection? 
<br>

Dependency Injection refers to passing the service to the client, rather than allowing a client to build or find the service. The intent behind dependency injection is to achieve [Separation of Concerns](https://www.geeksforgeeks.org/separation-of-concerns-soc/) for the construction and use of objects. This can increase code readability and allows for code reuse. Dependency injection is one form of the broader technique of Inversion of Control (IoC). With IoC, a client who wants to call some services shouldn't have to know how to construct those services. Instead, the client delegates the responsibility of providing its services to external code (the injector).

The client is not allowed to call the injector code; it is the injector that constructs the services. The injector then injects (passes) the services into the client which might already exist or may also be constructed by the injector. The client then uses the services. This means the client does not need to know about the injector, how to construct the services, or even which actual services it is using. The client only needs to know about the intrinsic interfaces of the services because these define how the client may use the services. This separates the responsibility of "use" from the responsibility of "construction". Dependency injection separates the creation of a client's dependencies from the client's behavior, which allows program designs to be [loosely coupled](https://glossary.cncf.io/loosely-coupled-architecture/) and to follow the dependency inversion and single responsibility principles. An example of this prinicple is shown below.

<br>
<br>

![dagger_general](https://github.com/user-attachments/assets/cfd6e368-1251-462a-8858-fd71a109ff84)

<br>

## Inversion of control (IoC)
[Inversion of control (IoC)](https://www.geeksforgeeks.org/spring-difference-between-inversion-of-control-and-dependency-injection/) is more general than DI. In simple terms, IoC means letting other code call you rather than insisting on doing the calling. 


### Dependency injection involves four roles:
1. the service object(s) to be used
1. the client object that is depending on the service(s) it uses
1. the interfaces that define how the client may use the services
1. the injector, which is responsible for constructing the services and injecting them into the client

As an analogy, a service is an electric, gas, hybrid, or diesel car. The client is a driver who uses the car the same way regardless of the engine. An interface ensures the driver doesn't have to understand engine details like gears. An injector is the parent who bought the kid the car and decided which kind of car to buy.

<br>

## Without dependency injection
In the following Java example, the Client class contains a Service member variable that is initialized by the Client constructor. The client controls which implementation of service is used and controls its construction. In this situation, 
the client is said to have a hard-coded dependency on ExampleService.

```java
public class Client {
    // Internal reference to the service used by this client
    private ExampleService service;

    // Constructor
    Client() {
        // Specify a specific implementation in the constructor instead of using dependency injection
        service = new ExampleService();
    }

    // Method within this client that uses the services
    public String greet() {
        return "Hello " + service.getName();
    }
}
```

### Constructor injection
This method requires the client to provide a parameter in a constructor for the dependency.
```java
// Constructor
Client(Service service) {
    // Save the reference to the passed-in service inside this client
    this.service = service;
}
```
### Setter injection
This method requires the client to provide a setter method for the dependency.
```java
// Setter method
public void setService(Service service) {
    // Save the reference to the passed-in service inside this client.
    this.service = service;
}
```
### Interface injection
This is simply the client publishing a role interface to the setter methods of the client's dependencies. It can be used to establish how the injector should talk to the client when injecting dependencies.
```java
// Service setter interface.
public interface ServiceSetter {
    public void setService(Service service);
}

// Client class
public class Client implements ServiceSetter {
    // Internal reference to the service used by this client.
    private Service service;

    // Set the service that this client is to use.
    @Override
    public void setService(Service service) {
        this.service = service;
    }
}
```

<br>

## [Dagger](https://dagger.dev)

Dagger is a fully static, compile-time dependency injection framework for both Java and Android. It is maintained by Google and aims to address many of the development and performance issues that are reflected in the injector solutions above.

Here’s a Java example that demonstrates the use of Dagger 2 annotations: @Inject, @Component, @Provide, and @Module. The example simulates a simple dependency injection scenario where we have a Car that depends on an Engine and a Fuel.

<br>

### Step 1: Define the Dependencies

```java
public class Engine {
    private final Fuel fuel;

    @Inject // Dagger will inject Fuel into Engine's constructor
    public Engine(Fuel fuel) {
        this.fuel = fuel;
    }
    public String start() {
        return "Engine started with " + fuel.getType();
    }
}
```

```java
public class Fuel {
    @Inject // Dagger can create an instance of Fuel directly
    public Fuel() {}

    public String getType() {
        return "Gas";
    }
}
```

```java
public class Car {
    private final Engine engine;

    @Inject // Dagger will inject Engine into Car's constructor
    public Car(Engine engine) {
        this.engine = engine;
    }

    public String drive() {
        return engine.start() + " and car is driving!";
    }
}
```
<br>

### Step 2: Create a Dagger Module

A Dagger @Module provides dependencies that cannot be created with @Inject.


```java
import dagger.Module;
import dagger.Provides;

@Module
public class AppModule {

    @Provides
    public Fuel provideFuel() {
        return new Fuel();
    }
}
```
<br>

### Step 3: Create a Component

A Dagger @Component connects the dependency providers (@Module) with the dependent classes.


```java
import dagger.Component;

@Component(modules = AppModule.class)
public interface AppComponent {
    Car getCar(); // Dagger will know how to create a Car instance with all its dependencies injected
}
```
<br>

### Step 4: Create a Service to use the Dagger setup

The functionality is encapsulated in CarService, which uses Dagger's component for dependency injection. The CarService can now be instantiated in any context, and dependencies are managed entirely by Dagger. This follows the modularity software design principle and makes the system easier to develop, maintain, scale, and test.

```java
public class CarService {
    private final Car car;

    public CarService(AppComponent appComponent) {
        //Get the Car instance from the Dagger component
        this.car = appComponent.getCar();
    }

    public String driveCar() {
        return car.drive();
    }
}
```

<br>

## Dagger Annotations explained

<br>

@Inject - annotates the constructor that Dagger should use to create instances of a class. When a new instance is requested, Dagger will obtain the required parameters values and invoke this constructor.
```java
@Inject public Engine(Fuel fuel)
```

@Provide - used in a @Module to specify how to provide a dependency.

```java
@Provides public Fuel provideFuel()
```

@Module - groups @Provides methods to tell Dagger how to create or configure objects. Example

```java
@Module public class AppModule.
```

@Component - acts as a bridge between the modules and the dependent classes.

```java
@Component(modules = AppModule.class)
```






