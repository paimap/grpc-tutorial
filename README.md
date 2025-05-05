# Reflection
1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
 - Unary memiliki alur komunikasi klien mengirim satu permintaan ke server, dan server mengirim kembali satu respons. Unary bisa digunakan dalam skenario seperti operasi yang membutuhkan satu input dan menghasilkan satu output yang dapat dikirimkan sekaligus. 
- Server Streaming memiliki alur komunikasi klien mengirim satu permintaan ke server, dan server merespons dengan sequence (urutan) dari banyak pesan. Server Streaming bisa digunakan dalam skenario seperti mengembalikan sejumlah besar data yang tidak efisien atau tidak mungkin dikirimkan dalam satu respons.
- Bi-directional memiliki alur komunikasi klien dan server dapat mengirimkan sequence dari banyak pesan secara bolak-balik dalam satu panggilan RPC yang sedang berlangsung. Bi-directional bisa digunakan dalam skenario seperti Komunikasi interaktif berkelanjutan antara klien dan server.

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Dalam hal autentikasi, gRPC tidak secara eksplisit memaksakan mekanisme otentikasi tertentu sehingga perlu mengimplementasikannya menggunakan fitur-fitur yang disediakan oleh framework atau library terkait (seperti Tonic di Rust). Sedangkan untuk autorisasi, biasanya diimplementasikan di lapisan aplikasi server setelah identitas klien berhasil diautentikasi. Kita dapat menggunakan interceptor atau logika di dalam implementasi layanan untuk memeriksa izin pengguna sebelum memproses permintaan dengan Tonic. Untuk enkripsi data, Tonic mendukung konfigurasi TLS menggunakan pustaka seperti tokio-rustls atau native-tls.

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

- Manajemen state koneksi
- Konkurensi dan sinkronisasi
- Backpressure dan flow control
- Skalabilitas

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Keuntungan: 
- Integrasi yang mudah dengan tokio::sync::mpsc
- Penanganan konkurensi yang baik
- Backpressure implisit (dengan Kapasitas Channel)
- Abstraksi dari detail transport gRPC

Kerugian:
- Overhead dari channel mpsc
- Potensi buffering yang tidak diinginkan
- Kurang fleksibel untuk kontrol streaming tingkat lanjut
- Tidak ideal untuk streaming berbasis pull

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Definisi layanan dan pesan gRPC sebaiknya dipisahkan dalam file .proto khusus, dengan kode Rust yang dihasilkan ditempatkan dalam modul terpisah menggunakan tonic-build. Selain itu, implementasi layanan gRPC dapat diatur ke dalam modul-modul Rust yang jelas berdasarkan fitur atau domain aplikasi, dengan setiap layanan diimplementasikan sebagai struct terpisah yang mengimplementasikan trait server yang dihasilkan.

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Validasi permintaan terhadap PaymentRequest untuk memastikan data yang diterima valid sebelum diproses lebih lanjut. manajemen state transaksi yang tepat, penanganan kesalahan gateway, dan implementasi logika bisnis tambahan sesuai kebutuhan aplikasi juga merupakan pertimbangan penting.

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Menggunakan gRPC dapat memfasilitasi komunikasi yang efisien dan terstruktur antar layanan yang dibangun dengan berbagai teknologi. Interoperabilitas antar layanan yang menggunakan gRPC menjadi lebih mulus berkat dukungan berbagai bahasa melalui code generation. Namun, integrasi dengan sistem yang tidak mendukung gRPC secara native memerlukan solusi seperti gateway atau proxy. Meskipun demikian, efisiensi, kontrak yang jelas, dan dukungan multi-bahasa gRPC seringkali menjadi pendorong utama dalam membangun sistem terdistribusi modern yang terstruktur dan berkinerja tinggi.

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Untuk gRPC, penggunaan HTTP/2 memberikan keuntungan signifikan dalam hal performa, efisiensi, dan kemampuan streaming dibandingkan HTTP/1.1. Meskipun memerlukan implementasi yang lebih kompleks dan umumnya menggunakan TLS, keunggulannya sangat sesuai untuk komunikasi antar layanan mikro yang berkinerja tinggi. Sementara HTTP/1.1 dengan WebSocket menawarkan solusi untuk komunikasi dua arah dalam konteks web. HTTP/1.1 dengan WebSocket tidak memiliki optimasi tingkat protokol dan struktur kontrak yang ditawarkan oleh gRPC dan HTTP/2, membuatnya kurang ideal untuk membangun API yang terstruktur dan efisien di antara layanan.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Model permintaan-respons REST API bersifat satu arah dan sinkron, memerlukan permintaan baru untuk setiap interaksi, sehingga kurang ideal untuk komunikasi real-time. Sebaliknya, bidirectional streaming gRPC memungkinkan komunikasi full-duplex berkelanjutan melalui satu koneksi. Klien dan server dapat mengirim serta menerima urutan pesan secara bersamaan, menghasilkan latensi yang jauh lebih rendah dan responsivitas yang lebih baik untuk aplikasi real-time.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan berbasis skema gRPC dengan Protocol Buffers meminimalkan kesalahan parsing dan meningkatkan interoperabilitas. Skema yang jelas juga memungkinkan validasi data yang lebih efisien. Sebaliknya, fleksibilitas tanpa skema JSON dalam REST API menawarkan kemudahan pengembangan awal dan adaptasi cepat terhadap perubahan data. Namun, kurangnya kontrak formal pada JSON berpotensi menimbulkan masalah interoperabilitas dan pemeliharaan jangka panjang.