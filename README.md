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
-   [V] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [V] Commit: `Create Subscriber model struct.`
    -   [V] Commit: `Create Notification model struct.`
    -   [V] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [V] Commit: `Implement add function in Subscriber repository.`
    -   [V] Commit: `Implement list_all function in Subscriber repository.`
    -   [V] Commit: `Implement delete function in Subscriber repository.`
    -   [V] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [V] Commit: `Create Notification service struct skeleton.`
    -   [V] Commit: `Implement subscribe function in Notification service.`
    -   [V] Commit: `Implement subscribe function in Notification controller.`
    -   [V] Commit: `Implement unsubscribe function in Notification service.`
    -   [V] Commit: `Implement unsubscribe function in Notification controller.`
    -   [V] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [V] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [V] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [V] Commit: `Implement publish function in Program service and Program controller.`
    -   [V] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [V] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### **Reflection Publisher-1**

1. >In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

   
   Penggunaan satu struct model sudah cukup untuk mengimplementasikan Observer Patterrn, kecuali jika nantinya akan dibutuhkan polimorfisme yang lebih kompleks sehingga trait sebagai interface akan lebih dibutuhkan.

2. > id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

   
   Karena id di Program dan url di Subscriber harus unik, penggunaan DashMap lebih tepat daripada Vec karena DashMap memungkinkan pencarian dan penghapusan dengan efisiensi yang lebih baik melalui akses langsung berdasarkan key yg diberikan.

3. >When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?
   
   Ya, DashMap tetap diperlukan meskipun kita mengimplementasikan Singleton pattern, karena Singleton hanya memastikan bahwa hanya ada satu instance global, tetapi tidak menjamin thread safety. Alternatif seperti menggunakan Mutex<HashMap<...>> atau RwLock<HashMap<...>> cenderung menimbulkan bottleneck akibat adanya global lock, sehingga menghambat akses concurrent. Sementara itu, DashMap menggunakan teknik sharding, yang memungkinkan beberapa thread mengakses bagian-bagian berbeda dari map secara bersamaan tanpa saling mengganggu, yg dapat menjadikan solusi yang lebih efisien untuk mengelola data SUBSCRIBERS secara thread-safe.

#### **Reflection Publisher-2**

1. >In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model? 
 
    Dalam arsitektur MVC, pemisahan Service dan Repository dari Model didasarkan pada prinsip Separation of Concerns dan Single Responsibility Principle. Model hanya mewakili data dan aturan dasar, sedangkan Repository bertanggung jawab atas operasi database seperti insert, delete, dan query, dan Service mengelola logika yg kompleks seperti validasi dan pemrosesan data. Jika semua fungsi ini digabungkan dalam Model, maka akan tercipta God Object yang kompleks sehingga sulit untuk diuji, dipelihara, dan dikembangkan.

2. >What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

    Jika kita hanya menggunakan Model tanpa Service dan Repository, setiap model harus menangani penyimpanan data dan logika sekaligus, yang menyebabkan tight coupling antara entitas seperti Program, Subscriber, dan Notification. Hal ini membuat kode menjadi sulit dibaca, di-debug, dan diuji secara unit karena logika yang sama dapat terduplikasi di berbagai tempat, sehingga setiap perubahan kecil dalam satu model bisa berdampak luas pada model lainnya dan menambah technical debt.

3. > Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

    Postman sangat membantu saya dalam pengujian API dengan memungkinkan pengiriman HTTP request (GET, POST, PUT, DELETE) dan melihat response JSON secara langsung. Fitur seperti Collections, Environment Variables, Automated Testing, dan Collection Runner memudahkan pengorganisasian request, pengujian otomatis, dan dokumentasi API. Hal ini dapat membuat proses debugging dan validasi endpoint menjadi lebih cepat dan efisien, yang sangat berguna untuk proyek kelompok maupun pengembangan API backend di nantinya.

#### Reflection Publisher-3

1. > Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?
   
   Pada tutorial kali ini, kita menggunakan Push Model dari Observer Pattern, di mana publisher langsung mengirim notifikasi lengkap kepada setiap subscriber melalui pemanggilan method update() yang dipicu oleh NotificationService.notify().

2. > What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

    Jika menggunakan Pull Model, keuntungan utamanya adalah subscriber memiliki kontrol penuh atas kapan dan data apa yang diambil. Subscriber dapat memilih untuk menarik data sesuai kebutuhan, yang memungkinkan pengambilan data secara selektif dan efisien, terutama ketika sistem mengirimkan banyak notifikasi yang tidak selalu relevan bagi semua subscriber. Di sisi lain, kekurangan yang akan dirasakan adalah jika subscriber tidak melakukan polling secara rutin, mereka bisa saja mendapatkan data yang terlambat atau bahkan kehilangan update terbaru. Selain itu, penerapan logika untuk polling data pada tiap subscriber akan menambah kompleksitas implementasi, sehingga dapat meningkatkan potensi terjadinya misinformasi dan ketidakefisienan dibandingkan dengan pendekatan Push Model di mana update langsung dikirimkan.

3. > Explain what will happen to the program if we decide to not use multi-threading in the notification process.

    Jika kita tidak menggunakan multi-threading, pengiriman notifikasi ke setiap subscriber akan dilakukan secara sekuensial, yang berarti satu notifikasi harus selesai diproses sebelum notifikasi berikutnya dikirim. Akibatnya adalah apabila terdapat banyak subscriber atau jika proses pengiriman ke satu subscriber memakan waktu yang cukup lama, maka seluruh proses notifikasi akan menjadi lambat dan tidak efisien sehingga berpotensi menyebabkan bottleneck dan timeout pada API request.