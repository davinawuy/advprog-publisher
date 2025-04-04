# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

In a observer pattern a trait defines the common methods like update() for example. In the example Bambangshop all subscribers exist as external rocket instances. Then notifications are sent via HTTP POST instead of method calls. Since there is no inherent need for polymorphism or any method overriding a trait in this case is not needed. A single model struct should be enough.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

A Dashmap ensures the uniqueness of keys or id for the program and url for subscriber. Vec would require a manual iteration to check for duplicates which may not be the best choice. DashMap supports faster lookups and concurrent access. This is ideal for multi-threaded environments  where multiple threads manage subscribers.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

A singleton guarantees that only instance of subscribers exists globally but it does not ensure thread safety it only guarantees single instantiation not safe concurrent access. This is where something like a DashMap becomes useful since a DashMap is thread-safe concurrent HashMap designed to handle multiple readers and writers. It will allow safe operations across multi-threaded which is ideal where different threads may be adding or removing subscribers. So while the implementation of a singleton like lazy_static! which allows for a single global instance is useful it must be paired with thread-safe structure like DashMap.

#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

The most basic reason would be to adhere to the SRP or single responsibility principle. This means that each component has its own role. The repository layer manages data access and database access. It also isolates the data storing from the rest of the application. The service layer deals with business logic independent of data storage concerns. It acts as the bridge between Controller and Repository. The reason we use SRP is to ensure that it is easier to maintain, scale, and understand. It also helps isolate items to prevent confusion during debugging.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Using only the model may not be the best choice since it can increase code complexity since it handles all responsibilities. It can also duplicate certain functions making them redundant. It also will force tighter dependency between each other and it strictly locks the scalability of the program since a small change will require a complete edit of everything else in model. Overall refactoring, updating, and etc will be overall harder.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

I have explored Postman a bit more, the features I found helpful were the ones that help simulate behaviours of the server and HTTP interactions.

Stuff I found helpful were:

- Environment Variables: Switch between dev/staging/production environments easily.

- Collections: Organize and group related API requests.

- Mock Servers: Simulate server responses for frontend or integration testing.

#### Reflection Publisher-3

1. Which variation of the Observer Pattern do we use in this tutorial case?

I believe in this tutorial we are using a push model of the observer Pattern. When a product is created or deleted the system or publish will instantly push the update to all subscribers who have been assigned that product. This is done through the notification servie which has an notify() method. This results in a real-time update without the need to manually do it.

2. What are the advantages and disadvantages of using the other variation of the Observer Pattern (Pull model) for this tutorial case?

If the **Pull model** were used instead:

**Advantages:**
- Subscribers have more control, they can choose when to fetch updates.
- Data transmission is reduced since subscribers only pull the data they need.
- This may reduce the load on the publisher should the broadcast only need to be done every change or so.

**Disadvantages:**
- Each subscriber would need to implement logic to  check the publisher for updates.
- The delay between change and retrieval introduces latency.
- This approach creates tighter dependency between subscribers and the publisher, increasing code complexity.

There may not be any inherent benefit from using the pull method.

3. What will happen to the program if we decide to not use multi-threading in the notification process?

Basically, the program will be very slow since it notifies each user sequentially so it will consume alot of time and be delayed if there are alot of users. This will make it both less responsive which may fail the need for a notification. Furthermore, this will be harder to scale since it grows in a linear way if multithread is not used then it will get slower as more subscribers join.