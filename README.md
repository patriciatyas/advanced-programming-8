# Module 8 Reflection
Patricia Herningtyas - 2306152241 


### What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

#### Perbedaan antara Unary, Server Streaming, dan Bi-Directional Streaming RPC

**Unary RPC** 
- Klien mengirim satu permintaan dan menerima satu respons dari server.

**Server Streaming RPC**
- Klien mengirim satu permintaan, kemudian server mengirimkan stream respons secara bertahap.

**Bi-Directional Streaming RPC**
- Klien dan server dapat saling mengirim pesan secara independen dalam stream yang sama.

#### Kapan Digunakan?

**Unary RPC** 
- Cocok untuk operasi sederhana seperti mengambil data spesifik dan otentikasi user.

**Server Streaming RPC**
-  Ideal untuk streaming data pasar saham, pembaruan cuaca, dan video streaming

**Bi-Directional Streaming RPC**
- Cocok untuk aplikasi chat, analitik real-time, dan permainan interaktif

### What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?
Pertama, authentication harus diterapkan untuk memverifikasi identitas klien maupun server. Rust dapat memanfaatkan TLS (Transport Layer Security) untuk mengenkripsi koneksi dan mencegah intersepsi data oleh pihak ketiga. Selain itu, penggunaan token seperti JWT (JSON Web Token) atau OAuth2 menjadi metode umum untuk memastikan bahwa hanya klien yang sah yang bisa mengakses layanan. Kedua, authentication harus diberlakukan guna memastikan bahwa hanya pengguna atau layanan dengan hak akses tertentu yang dapat melakukan operasi tertentu. Ini bisa dicapai melalui role-based access control (RBAC) atau skema otorisasi berbasis atribut (ABAC). Terakhir, enkripsi data selama transmisi wajib dilakukan menggunakan TLS minimal versi 1.2 untuk menjaga kerahasiaan data. Jika data yang dikirimkan sangat sensitif, pertimbangan untuk menggunakan enkripsi ujung-ke-ujung (end-to-end encryption) menjadi penting agar data tidak dapat diakses di tengah jalur komunikasi, bahkan oleh server perantara.

### What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?
Salah satu tantangan utama adalah pengelolaan sinkronisasi dalam konteks asynchronous programming. Rust menggunakan model asynchronous yang ketat berbasis async/await, sehingga mengelola dua aliran data (dari klien ke server dan sebaliknya) secara paralel dapat memicu race condition atau deadlock jika tidak ditangani dengan benar. Selain itu, manajemen state pengguna juga menjadi kompleks, misalnya dalam melacak pengguna yang sedang online, siapa yang harus menerima pesan, dan bagaimana menangani pesan yang tertunda. Penanganan error juga menjadi penting, karena stream dapat terputus sewaktu-waktu akibat jaringan tidak stabil, sehingga perlu ada mekanisme reconnect dan retry yang andal. Terakhir, bi-directional streaming rentan terhadap masalah backpressure, di mana salah satu pihak mengirim data lebih cepat daripada pihak lain dapat memprosesnya. Tanpa mekanisme flow control yang tepat, hal ini dapat menyebabkan penumpukan data di memori dan akhirnya memicu crash atau penurunan performa.

### What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

**Keuntungan:**
- Pemrosesan asynchronous mempercepat penanganan data.
- Integrasi yang mudah dengan modul tokio lainnya.

**Kerugian:**
- Kompleksitas karena pemrograman asynchronous.
- Authentication dan authorization perlu dilakukan untuk setiap fragmen data


### In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

gRPC Rust menyediakan cara untuk meningkatkan maintainability dan extensibility karena perubahan dan penambahan fitur dapat dengan mudah dilakukan mengingat proto akan membuat sebuah interface yang dapat diimplementasikan oleh kelas Rust, sehingga memfasilitasi code extensibility.


### In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Dapat dibuat menjadi server streaming alih-alih unary untuk memungkinkan transmisi data kompleks yang lebih cepat dan mengurangi overhead dalam membuat koneksi antara client dan server.

### What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
Dengan menggunakan gRPC, kita tidak perlu lagi khawatir tentang bagaimana modul dapat diakses dengan metode HTTP karena gRPC akan secara otomatis menghubungkan method calls yang kita inginkan karena ada shared understanding melalui file proto, di mana client seolah-olah dapat memanggil fungsi dari server, sehingga memfasilitasi connectivity dan operations di berbagai teknologi, platform, dan distributed systems.

### What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Keuntungan HTTP/2 adalah memungkinkan banyak request dan response dibuat dalam satu koneksi tanpa perlu mengakhiri koneksi tersebut. Kerugian dibandingkan dengan HTTP/1.1 adalah karena HTTP/2 dapat mempertahankan satu koneksi untuk banyak request dan response, akan ada biaya overhead yang lebih tinggi dalam hal performance dan memory, di mana bahkan untuk transfer data kecil, HTTP/2 mungkin menimbulkan biaya lebih tinggi dibandingkan HTTP/1.1, meskipun lebih efisien untuk data besar.

### How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Perbedaan antara REST API dan gRPC terlihat jelas dalam cara kerjanya, di mana REST API akan memakan waktu lebih lama dibandingkan gRPC untuk real-time communication karena untuk REST API, setiap transmisi request memerlukan pembentukan koneksi baru dari client ke server, sementara gRPC hanya memerlukan satu koneksi sampai semua request selesai. Ini membuat gRPC lebih optimal untuk real-time communication dan responsiveness dibandingkan REST API karena kecepatannya yang lebih tinggi, sehingga lebih "real-time".

### What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Dengan pendekatan Protocol Buffer, client tidak perlu lagi khawatir apakah JSON berisi informasi yang benar karena dengan gRPC dan Protocol Buffer-nya, sebuah interface akan dibuat untuk membuat kode client dan server, dan selama runtime, secara otomatis akan melakukan serialization dan deserialization pesan masuk dan keluar. Dengan demikian, setiap data masuk dan keluar dijamin dapat digunakan dan tidak bertipe salah. Sedangkan dengan JSON di REST API, sangat mungkin untuk mengirim data yang berisi informasi yang tidak dapat digunakan oleh satu pihak karena tidak ada batasan tentang apa yang perlu dikirim.
