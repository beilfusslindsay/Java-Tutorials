# Dependency Injection 

What’s dependency injection? Passing the service to the client, rather than allowing a client to build or find the service, is the fundamental requirement of the pattern. The intent behind dependency injection is to achieve Separation of Concerns of construction and use of objects. This can increase readability and code reuse. Dependency injection is one form of the broader technique of inversion of control. A client who wants to call some services should not have to know how to construct those services. Instead, the client delegates the responsibility of providing its services to external code (the injector).

The client is not allowed to call the injector code; it is the injector that constructs the services. The injector then injects (passes) the services into the client which might already exist or may also be constructed by the injector. The client then uses the services. This means the client does not need to know about the injector, how to construct the services, or even which actual services it is using. The client only needs to know about the intrinsic interfaces of the services because these define how the client may use the services. This separates the responsibility of "use" from the responsibility of "construction". Dependency injection separates the creation of a client's dependencies from the client's behavior, which allows program designs to be [loosely coupled](https://glossary.cncf.io/loosely-coupled-architecture/) and to follow the dependency inversion and single responsibility principles.
![dagger_general](https://github.com/user-attachments/assets/cfd6e368-1251-462a-8858-fd71a109ff84)

## Inversion of control (IoC)
[Inversion of control (IoC)](https://www.geeksforgeeks.org/spring-difference-between-inversion-of-control-and-dependency-injection/) is more general than DI. Put simply, IoC means letting other code call you rather than insisting on doing the calling. 
### Dependency injection involves four roles:
1. the service object(s) to be used
1. the client object that is depending on the service(s) it uses
1. the interfaces that define how the client may use the services
1. the injector, which is responsible for constructing the services and injecting them into the client

As an analogy, a service is an electric, gas, hybrid, or diesel car. The client is a driver who uses the car the same way regardless of the engine. An interface ensures the driver doesn't have to understand engine details like gears. An injector is the parent who bought the kid the car and decided which kind

Without dependency injection[edit]
In the following Java example, the Client class contains a Service member variable 
that is initialized by the Client constructor. The client controls which 
implementation of service is used and controls its construction. In this situation, 
the client is said to have a hard-coded dependency on ExampleService.

// An example without dependency injection
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
Constructor injection[edit]
This method requires the client to provide a parameter in a constructor for the 
dependency.

// Constructor
Client(Service service) {
    // Save the reference to the passed-in service inside this client
    this.service = service;
}
Setter injection[edit]
This method requires the client to provide a setter method for the dependency.

// Setter method
public void setService(Service service) {
    // Save the reference to the passed-in service inside this client.
    this.service = service;
}
Interface injection[edit]
This is simply the client publishing a role interface to the setter methods of the
 client's dependencies. It can be used to establish how the injector should talk to
  the client when injecting dependencies.

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
 https://en.wikipedia.org/wiki/Dependency_injection 

Dagger
Dagger is a fully static, compile-time dependency injection framework for both Java and Android. It is an adaptation of an earlier version created by Square and now maintained by Google.
Dagger aims to address many of the development and performance issues that have plagued reflection-based solutions.

https://dagger.dev

@Inject
@Component
@Provide
@Module
