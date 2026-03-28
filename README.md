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
1. Dalam kasus ini, sebuah single Model struct sudah cukup karena subscriber diperlakukan sebagai entitas data yang seragam (memiliki `url` dan `name`). Trait belum dibutuhkan karena belum ada kebutuhan untuk memiliki berbagai jenis subscriber dengan cara update yang berbeda-beda. Trait akan dibutuhkan nanti apabila ingin menggunakan berbagai cara notifikasi. Sehingga, sebuah single struct Subscriber sudah cukup.

2. Karena `id` dan `url` harus unik, maka program sering melakukan pencarian untuk mengambil data spesifik. Jika menggunakan `Vec`, untuk mencari data tertentu, program harus melakukan iterasi dari awal sampai akhir (O(n)), sehingga semakin banyak subscriber, semakin lambat proses pencariannya. Jika menggunakan `DashMap`, pencarian, penyisipan, dan penghapusan dilakukan dalam waktu konstan (O(1)). Karena `id` dan `url` unik, maka sistem dapat langsung mengakses subscriber tertentu tanpa perlu mengiterasi seluruh isi list. `DashMap` lebih efisien dan diperlukan di kasus ini.

3. Singleton memastikan hanya ada satu instance di program, tetapi tidak menjamin thread safety. Sedangkan DashMap memastikan thread safety untuk mengelola akses konkuren ke data tersebut. `DashMap` tetap dibutuhkan di program ini untuk memastikan thread safety.

#### Reflection Publisher-2
1. Pemisahan service dan repository dari model dilakukan untuk menerapkan Single Reponsibility Principle. Jika kita menggabungkan semuanya ke dalam Model, maka Model tersebut akan menjadi bloated karena memiliki terlalu banyak tanggung jawab. Repository hanya focus pada data access dan berhubungan dengan database, Service focus pada logika bisnis, dan Model focus pada mendefinisikan struktur data. Dengan memisahkan Service dan Repository, kode menjadi lebih mudah dimaintain, dan memodifikasi kode menjadi lebih mudah apabila ingin mengubah logika bisnis atau mengubah database. Selain itu, pemisahan ini membuat unit testing jauh lebih mudah.

2. Jika kita memaksakan semua logika hanya di dalam Model (tanpa pemisahan service dan repository), maka model Program harus tahu cara menyimpan dirinya sendiri, mencari Subscriber, dan membuat Notification. Setiap file model akan penuh dengan `use` dari model lain. Jika seandainya kita mengubah struktur Notification, maka mungkin kita perlu mengubah kode di dalam Program dan Subscriber juga. Lalu, file bisa berisi menjadi ratusan baris kode yang mencampuradukkan tugas. Kode menjadi sangat sulit dipisahkan (tightly coupled). Perubahan kecil pada skema database akan memaksa kita menulis ulang logika bisnis di seluruh file Model, dan menyebabkan sulit di-testing dan rentan terhadap bug.

3. Dengan Postman, kita bisa langsung menguji API yang dibuat apakah benar-benar menyimpan data tanpa harus membuat halaman UI terlebih dahulu. Fitur yang cukup menarik adalah environment & variables, kita bisa menyimpan URL server sebagai variabel. Lalu, fitur collections & sharing juga sangat berguna untuk proyek kelompok karena kita bisa mengekspor semua request API ke dalam satu file dan membagikannya ke kelompok.

#### Reflection Publisher-3
1. Di program ini, kita menggunakan Push model, dimana Publisher mengirimkan data kepada para pelanggan setelah terjadi perubahan status. Dalam program ini, Publisher memegang kendali penuh untuk mendistribukan data secara real-time, sehingga Subscriber tidak perlu melakukan request manual (pull) untuk mendapatkan informasi terbaru.

2. Dalam pull model, Publisher hanya mengirimkan sinyal notifikasi ada perubahan, lalu Subscriber harus melakukan request Kembali ke Publisher untuk mengambil detail data yang dibutuhkan. Keuntungan dari pull model adalah hanya mengirimkan data yang dibutuhkan, subscriber bisa memilih untuk mengabaikan data yang tidak relevan. Sedangkan kerugian dari pull model adalah beban server meningkat, karena setiap kali ada notifikasi ke semua subscriber, maka setiap subscriber harus melakukan request balik. Ini juga membuat proses menjadi lebih lama karena ada Langkah tambahan, sehingga ini tidak se-real time push model.

3. Tanpa multi-threading, setiap kali fungsi notify dipanggil, program akan menunggu satu proses update selesai sebelum melanjutkan ke subscriber berikutnya. Apalagi jika koneksi lambat, maka server akan terkunci pada fungsi tersebut dan tidak bisa memproses request lain dengan cepat. Jika tidak menggunakan multi-threading, proses pengiriman notifikasi akan menjadi synchronous (sequential). Dengan multi-threading, notifikasi bisa dikirimkan secara serentak tanpa mempengaruhi performa.