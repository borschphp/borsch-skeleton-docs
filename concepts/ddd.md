# Domain Driven Design (DDD)

## Definition

Domain Driven Design (DDD) is a software design approach that emphasizes collaboration between technical and domain
experts to create a shared understanding of the domain. It focuses on modeling the core business concepts and logic,
ensuring that the software reflects the real-world processes and rules of the business.

## Key Concepts

Borsch Skeleton is mainly focused on developing microservices, therefore it uses DDD concepts to structure the codebase.  
Here are some key concepts of DDD that are relevant to Borsch Skeleton.

> [!NOTE]
> You can use Borsch Skeleton to see a concrete example of how to structure the codebase using DDD concepts.

### Application Layer

The **Application layer** (located in `src/Application`) is responsible for receiving the requests, validating them, and
asking the **Domain layer** to perform the business logic.

If there is no need to perform any business logic, the **Application layer** can return the response directly (using a 
template renderer/engine for example).

### Domain Layer

The **Domain layer** (located in `src/Domain`) contains the core business logic and rules. It is where the main entities,
value objects, and aggregates are defined. The **Domain layer** is independent of any frameworks or external libraries,
ensuring that the business logic remains pure and focused on the domain.

It is responsible for validating the business rules and ensuring that the state of the entities is consistent.  
The **Domain layer** should not depend on the **Application layer** or any other layers, as it is the heart of the
business logic.

### Infrastructure Layer

The **Infrastructure layer** (located in `src/Infrastructure`) provides the necessary technical support for the
application, such as database access, messaging, and external service integrations. 

It is responsible for implementing the interfaces defined in the **Domain layer** and providing the necessary
infrastructure to support the application (database query, file reader, etc.).
