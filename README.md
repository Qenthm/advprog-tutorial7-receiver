# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. **RwLock vs Mutex**:
   
   In this tutorial, we use `RwLock<Vec<Notification>>` to synchronize access to our notifications collection. `RwLock` (Read-Write Lock) is necessary because it allows multiple readers to access the data simultaneously, while ensuring exclusive access when writing. This is particularly important for our notification system where:
   
   - Reading notifications (listing them) happens frequently and can be done in parallel
   - Adding new notifications (writing) happens less frequently but needs exclusive access
   
   We don't use `Mutex` because it would be less efficient in this scenario. A `Mutex` provides exclusive access for both reading and writing operations, which means only one thread could read the notifications at a time. This would unnecessarily limit performance when multiple concurrent read requests come in, as they would have to wait for each other despite not modifying the data.

2. **Static Variables in Rust vs Java**:

   In Rust, static variables are handled differently from Java due to Rust's ownership and safety principles. In Java, we can freely mutate static variables through static methods without much concern. However, Rust strictly enforces memory safety and prevents data races even with static variables.
   
   The key difference is that Rust requires explicit synchronization mechanisms (like `RwLock` or `Mutex`) when modifying static mutable data to guarantee thread safety. This is why we use `lazy_static` combined with `RwLock` - it allows us to:
   
   - Have a "static" variable that can be initialized at runtime
   - Safely mutate its contents across multiple threads
   - Ensure thread-safe access to the data
   
   Without these mechanisms, Rust would not allow mutation of static variables because it could lead to data races and unsafe memory access in a multithreaded environment. This reflects Rust's philosophy of "fearless concurrency" where the compiler enforces thread safety at compile time rather than dealing with concurrency bugs at runtime.

#### Reflection Subscriber-2

1. **Exploring Beyond the Tutorial**:

   Yes, I explored beyond the tutorial steps, particularly `src/lib.rs`. This file serves as the entry point of the application and reveals how the Rocket framework is configured. Through this exploration, I learned about:
   
   - How Rocket's fairing system works for attaching additional functionality to the server
   - How environment variables are loaded and used throughout the application
   - How the application's architecture is structured with clear separation between controllers, services, and repositories
   
   This exploration helped me understand the bigger picture of how a Rust web application is organized and how the Observer pattern is implemented at an architectural level rather than just in isolated components.

2. **Observer Pattern and Scalability**:

   The Observer pattern significantly simplifies adding more subscribers to the system. With our implementation:
   
   - Each new Receiver instance can independently subscribe to product types it's interested in
   - The Publisher (Main app) doesn't need to know anything about new Receivers in advance
   - Adding a new Receiver is as simple as starting a new instance with its own port and name
   
   This decoupling between the subject and observers is the key strength of the pattern.
   
   However, scaling the Publisher (Main app) would be more complex. Running multiple instances of the Main app would require:
   
   - A shared database for product information across Publisher instances
   - A mechanism to synchronize subscription lists between Publisher instances
   - Load balancing for incoming requests
   
   The Observer pattern alone doesn't solve this complexity, as it's primarily designed for one-to-many notification systems. For a many-to-many system with multiple Publishers, we would need additional architectural patterns like a message broker or event bus.

3. **Tests and Documentation**:

   I enhanced the Postman collection with detailed examples and descriptions for each endpoint. This proved extremely valuable when testing the application across multiple instances. Documentation helped me:
   
   - Remember the purpose and requirements of each endpoint
   - Understand the expected response formats
   - Troubleshoot issues by comparing actual vs. expected responses
   
   For the tutorial work, having well-documented API calls made it easier to verify the Observer pattern was functioning correctly. For a group project, such documentation would be essential for team members to understand how to interact with the API without needing to review the code.
   
   I also wrote simple tests to verify the notification system's behavior under various scenarios. These tests helped catch edge cases early and ensured the notification delivery was reliable across all subscribed instances.
