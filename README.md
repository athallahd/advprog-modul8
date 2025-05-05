# Advance Programming - Modul 8

## Athallah Damar Jiwanto - B - 2306245024

> 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

#### 1. Unary RPC
- **Alur Komunikasi**: Klien mengirim satu permintaan, server mengirim satu respons.
- **Sifat**: Sinkron dan blocking (menunggu).
- **Struktur**: Paling sederhana, mirip dengan panggilan fungsi biasa.
- **Skenario Penggunaan**: Operasi CRUD sederhana, transaksi data kecil, atau API yang memerlukan satu respons lengkap.

#### 2. Server Streaming RPC
- **Alur Komunikasi**: Klien mengirim satu permintaan, server mengirim aliran respons (banyak respons).
- **Sifat**: Asinkron dari sisi server.
- **Struktur**: Server dapat mengirim data secara berkelanjutan tanpa perlu permintaan baru.
- **Skenario Penggunaan**: Pengiriman data besar (hasil pencarian, laporan), pemantauan sistem real-time, atau streaming file/konten media.

#### 3. Bi-directional Streaming RPC
- **Alur Komunikasi**: Klien dan server dapat saling mengirim aliran pesan secara independen.
- **Sifat**: Komunikasi asinkron dua arah (full-duplex).
- **Struktur**: Paling fleksibel tetapi juga paling kompleks.
- **Skenario Penggunaan**: Aplikasi chat real-time, game online multiplayer, kolaborasi dokumen real-time, atau sistem IoT dengan komunikasi berkelanjutan.

> 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

#### 1. Autentikasi
- **Gunakan TLS/SSL**: Pastikan komunikasi antara klien dan server dienkripsi menggunakan TLS/SSL untuk mencegah serangan man-in-the-middle.
- **Mekanisme Autentikasi**: Gunakan mekanisme autentikasi yang aman seperti OAuth2, token JWT, atau sertifikat klien untuk memverifikasi identitas klien.
- **Rotasi Kunci**: Pastikan kunci atau token autentikasi dapat diperbarui secara berkala untuk mengurangi risiko penyalahgunaan.

#### 2. Otorisasi
- **Kontrol Akses Berbasis Peran (RBAC)**: Implementasikan RBAC untuk memastikan hanya pengguna dengan hak akses tertentu yang dapat mengakses metode atau data tertentu.
- **Validasi Permintaan**: Pastikan setiap permintaan yang masuk divalidasi terhadap kebijakan otorisasi yang telah ditentukan.
- **Audit Log**: Catat semua aktivitas otorisasi untuk mendeteksi potensi pelanggaran keamanan.

#### 3. Enkripsi Data
- **Enkripsi Data dalam Transit**: Gunakan TLS untuk mengenkripsi data yang dikirimkan antara klien dan server.
- **Enkripsi Data di Server**: Jika data sensitif disimpan di server, pastikan data tersebut dienkripsi menggunakan algoritma yang kuat.
- **Hindari Data Sensitif dalam Log**: Jangan mencatat data sensitif seperti token atau kata sandi dalam log aplikasi.

#### 4. Pertimbangan Tambahan
- **Rate Limiting**: Terapkan pembatasan jumlah permintaan untuk mencegah serangan DDoS.
- **Validasi Input**: Pastikan semua input dari klien divalidasi untuk mencegah serangan injeksi.
- **Pembaruan Perangkat Lunak**: Selalu gunakan versi terbaru dari pustaka gRPC dan dependensi lainnya untuk mendapatkan perbaikan keamanan terbaru.

> 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Bidirectional streaming dalam Rust gRPC, terutama dalam skenario seperti aplikasi chat, dapat menghadirkan beberapa tantangan atau masalah berikut:

#### 1. Sinkronisasi Data
- **Masalah**: Klien dan server harus dapat menangani aliran data secara bersamaan tanpa kehilangan pesan.
- **Solusi**: Gunakan mekanisme sinkronisasi yang kuat seperti kanal asinkron (`tokio::sync::mpsc`) untuk memastikan pesan diterima dan diproses dengan benar.

#### 2. Penanganan Kesalahan
- **Masalah**: Kesalahan seperti koneksi terputus atau pesan yang tidak valid dapat mengganggu aliran komunikasi.
- **Solusi**: Implementasikan mekanisme retry dan validasi pesan untuk menangani kesalahan secara efektif.

#### 3. Manajemen Sumber Daya
- **Masalah**: Streaming dua arah dapat menghabiskan banyak sumber daya, terutama jika ada banyak klien yang terhubung secara bersamaan.
- **Solusi**: Gunakan pembatasan jumlah klien (rate limiting) dan optimalkan penggunaan memori serta CPU.

#### 4. Kompleksitas Implementasi
- **Masalah**: Bidirectional streaming memerlukan pengelolaan state yang kompleks, terutama untuk melacak status setiap klien.
- **Solusi**: Gunakan struktur data yang efisien dan library seperti `tokio` untuk menangani asinkronitas.

#### 5. Keamanan
- **Masalah**: Data yang dikirimkan melalui streaming dapat rentan terhadap serangan seperti injeksi atau penyadapan.
- **Solusi**: Pastikan semua data dienkripsi menggunakan TLS dan validasi input dilakukan sebelum diproses.

#### 6. Latensi
- **Masalah**: Latensi jaringan dapat memengaruhi pengalaman pengguna, terutama dalam aplikasi real-time seperti chat.
- **Solusi**: Optimalkan protokol komunikasi dan minimalkan overhead jaringan.

> 4. What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?

#### Kelebihan
1. **Integrasi Mudah**: `ReceiverStream` menyediakan cara yang sederhana untuk mengubah kanal asinkron (`tokio::sync::mpsc`) menjadi stream yang dapat digunakan oleh gRPC.
2. **Asinkronitas**: Mendukung operasi asinkron penuh, memungkinkan server untuk mengirim data secara bertahap tanpa memblokir thread.
3. **Fleksibilitas**: Memungkinkan pengiriman data secara dinamis berdasarkan logika aplikasi, seperti streaming data real-time atau respons batch.
4. **Kompatibilitas**: Mudah diintegrasikan dengan pustaka lain dalam ekosistem `tokio`.

#### Kekurangan
1. **Manajemen Buffer**: Jika buffer kanal penuh, pengiriman pesan akan tertunda hingga ada ruang kosong, yang dapat menyebabkan latensi tambahan.
2. **Kesalahan Tidak Tertangani**: Kesalahan dalam pengiriman pesan melalui kanal dapat sulit dideteksi jika tidak ditangani dengan baik.
3. **Overhead**: Menggunakan kanal untuk setiap stream dapat menambah overhead memori, terutama jika ada banyak klien yang terhubung secara bersamaan.
4. **Kompleksitas Debugging**: Debugging aliran data asinkron dapat menjadi lebih kompleks, terutama jika terjadi deadlock atau kebocoran memori.

> 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

1. **Pisahkan Logika Bisnis**: Tempatkan logika bisnis di modul terpisah agar tidak tercampur dengan logika gRPC.
2. **Gunakan Traits**: Definisikan traits untuk abstraksi layanan sehingga implementasi dapat diganti atau diperluas dengan mudah.
3. **Organisasi Modul**: Strukturkan kode dalam modul seperti `client`, `server`, dan `models` untuk memisahkan tanggung jawab.
4. **Gunakan Protobuf Secara Efisien**: Simpan file `.proto` di direktori khusus seperti `proto/` dan hasilkan kode secara otomatis untuk menghindari duplikasi.
5. **Library Utilitas**: Buat library utilitas untuk fungsi umum seperti validasi data atau logging.

> 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

#### Langkah Tambahan untuk Logika Pemrosesan Pembayaran yang Lebih Kompleks
1. **Validasi Data**: Pastikan semua data pembayaran divalidasi sebelum diproses.
2. **Integrasi Gateway Pembayaran**: Tambahkan integrasi dengan gateway pembayaran eksternal.
3. **Manajemen Transaksi**: Implementasikan mekanisme rollback untuk menangani kegagalan transaksi.
4. **Keamanan**: Enkripsi data sensitif seperti informasi kartu kredit.
5. **Logging dan Monitoring**: Catat semua aktivitas pembayaran untuk audit dan deteksi anomali.
6. **Dukungan Multi-Mata Uang**: Tambahkan dukungan untuk berbagai mata uang jika diperlukan.

> 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

#### Dampak Adopsi gRPC pada Arsitektur Sistem Terdistribusi
1. **Interoperabilitas**: gRPC mendukung berbagai bahasa pemrograman, memudahkan integrasi lintas platform.
2. **Efisiensi**: Menggunakan HTTP/2 untuk komunikasi yang lebih cepat dan efisien dibandingkan REST.
3. **Schema-Based**: Protobuf memastikan kontrak yang kuat antara klien dan server.
4. **Kompleksitas**: Membutuhkan alat tambahan untuk debugging dan monitoring dibandingkan REST.
5. **Real-Time**: Mendukung streaming dua arah untuk komunikasi real-time.

> 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

#### Kelebihan dan Kekurangan HTTP/2 dibandingkan HTTP/1.1 atau WebSocket

**Kelebihan**:
1. **Multiplexing**: HTTP/2 memungkinkan beberapa permintaan dalam satu koneksi, mengurangi latensi.
2. **Header Compression**: Mengurangi overhead data dengan kompresi header.
3. **Streaming**: Mendukung streaming data secara native.

**Kekurangan**:
1. **Kompleksitas**: Lebih kompleks untuk diimplementasikan dibandingkan HTTP/1.1.
2. **Kompatibilitas**: Tidak semua klien atau server mendukung HTTP/2.

> 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

#### Perbandingan Model Request-Response REST dengan Streaming Dua Arah gRPC
1. **REST (Request-Response)**:
   - **Kelebihan**: Sederhana dan mudah diimplementasikan.
   - **Kekurangan**: Tidak ideal untuk komunikasi real-time karena setiap permintaan membutuhkan respons terpisah.

2. **gRPC (Bidirectional Streaming)**:
   - **Kelebihan**: Mendukung komunikasi real-time dengan latensi rendah.
   - **Kekurangan**: Lebih kompleks untuk diimplementasikan dan membutuhkan protokol yang lebih canggih.

> 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

#### Implikasi Pendekatan Schema-Based gRPC dibandingkan JSON Schema-Less
1. **gRPC (Schema-Based)**:
   - **Kelebihan**: Kontrak yang kuat antara klien dan server, mengurangi kesalahan parsing.
   - **Kekurangan**: Kurang fleksibel untuk perubahan skema.

2. **REST (JSON Schema-Less)**:
   - **Kelebihan**: Fleksibel untuk perubahan data.
   - **Kekurangan**: Rentan terhadap kesalahan parsing dan kurang efisien dalam ukuran payload.