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

1. Perbandingan RwLock dan Mutex
Dalam tutorial ini, kita menggunakan RwLock<Vec<Notification>> untuk menyinkronkan akses ke daftar notifikasi. RwLock (Read-Write Lock) sangat berguna karena memungkinkan beberapa thread membaca data secara bersamaan, tetapi tetap membatasi akses hanya satu thread saat melakukan penulisan. Hal ini penting untuk sistem notifikasi karena:

Pembacaan notifikasi (misalnya, menampilkan daftar notifikasi) terjadi sering dan dapat dilakukan secara paralel.

Penambahan notifikasi baru (penulisan) terjadi lebih jarang, tetapi memerlukan akses eksklusif untuk menjaga konsistensi data.

Kita tidak menggunakan Mutex karena kurang efisien dalam skenario ini. Mutex memberikan akses eksklusif baik untuk membaca maupun menulis, yang berarti hanya satu thread yang dapat membaca data dalam satu waktu. Hal ini akan menghambat kinerja, terutama saat ada banyak permintaan pembacaan secara bersamaan, karena mereka harus menunggu satu sama lain meskipun tidak mengubah data.

2. Perbedaan Variabel Statis di Rust dan Java
Rust memiliki pendekatan berbeda dalam menangani variabel statis dibandingkan dengan Java, karena Rust menerapkan prinsip kepemilikan dan keamanan memori secara ketat.

Di Java, variabel statis dapat dimodifikasi dengan mudah melalui metode statis tanpa banyak kendala. Namun, Rust melarang modifikasi langsung terhadap variabel statis tanpa mekanisme sinkronisasi untuk mencegah race condition dan memastikan keamanan thread.

Untuk mengatasi keterbatasan ini, kita menggunakan lazy_static yang dikombinasikan dengan RwLock, yang memungkinkan kita untuk:

Mendeklarasikan variabel statis yang dapat diinisialisasi saat runtime.

Mengubah isinya secara aman dalam lingkungan multithreaded.

Menjamin akses thread-safe terhadap data.

Tanpa mekanisme ini, Rust tidak akan mengizinkan perubahan pada variabel statis karena dapat menyebabkan race condition dan akses memori tidak aman. Pendekatan ini sejalan dengan filosofi Rust tentang "konkurensi tanpa rasa takut", di mana kompiler memastikan keamanan thread sejak tahap kompilasi, sehingga mencegah bug konkurensi yang biasanya baru terdeteksi saat runtime di bahasa lain seperti Java.

#### Reflection Subscriber-2

1. Eksplorasi di Luar Langkah-Langkah Tutorial
Ya, saya melangkah lebih jauh dari instruksi dalam tutorial, terutama dengan mengeksplorasi src/lib.rs. File ini berperan sebagai titik masuk aplikasi, yang mengatur bagaimana framework Rocket dikonfigurasi. Dari eksplorasi ini, saya memahami:

Cara kerja fairing Rocket dalam menambahkan fitur tambahan ke server.

Cara environment variable dimuat dan diterapkan di seluruh aplikasi.

Bagaimana arsitektur aplikasi dipisahkan secara jelas antara controller, service, dan repository untuk meningkatkan modularitas.

2. Pola Observer dan Skalabilitas
Pola Observer mempermudah proses menambahkan lebih banyak subscriber ke dalam sistem. Dalam implementasi kita:

Setiap instance Receiver baru dapat secara independen berlangganan ke jenis produk yang diminatinya.

Publisher (aplikasi utama) tidak perlu mengetahui keberadaan Receiver baru sebelumnya.

Menambahkan Receiver baru cukup dengan menjalankan instance baru dengan port dan nama yang berbeda.

Keunggulan utama dari pola ini adalah pemisahan yang jelas antara subject (Publisher) dan observer (Receiver).

Namun, jika ingin meningkatkan skala Publisher (aplikasi utama), ada tantangan tambahan, seperti:

Menggunakan database bersama untuk menyimpan informasi produk di seluruh instance Publisher.

Menyinkronkan daftar subscription agar setiap instance Publisher memiliki informasi yang sama.

Menerapkan load balancing untuk menangani permintaan masuk secara efisien.

Pola Observer sendiri tidak cukup untuk menangani kompleksitas ini, karena dirancang untuk notifikasi satu-ke-banyak. Untuk sistem dengan banyak Publisher dan banyak Subscriber, kita perlu pola arsitektur tambahan, seperti message broker atau event bus.

3. Pengujian dan Dokumentasi
Saya meningkatkan koleksi Postman dengan contoh serta deskripsi terperinci untuk setiap endpoint. Ini sangat membantu dalam pengujian, terutama saat menjalankan beberapa instance aplikasi. Dokumentasi ini membantu saya:

Mengidentifikasi tujuan dan kebutuhan setiap endpoint.

Memahami format respons yang diharapkan.

Mendiagnosis masalah dengan membandingkan respons aktual dan yang seharusnya.

Dalam konteks tutorial ini, dokumentasi API yang baik sangat membantu untuk memverifikasi apakah pola Observer berfungsi dengan benar. Selain itu, dalam proyek tim, dokumentasi ini akan mempermudah anggota tim dalam berinteraksi dengan API tanpa harus membaca seluruh kode sumber.
