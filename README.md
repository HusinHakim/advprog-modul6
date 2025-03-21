# advprog-modul6

# Catatan Refleksi Commit 1

## Analisis Implementasi Handle Connection

Fungsi `handle_connection` dalam implementasi web server kita mendemonstrasikan beberapa konsep kunci dalam pengembangan Rust dan web server:

1. **Penanganan Stream**:
   - Menerima parameter TcpStream yang mutable, memungkinkan kita untuk membaca dan menulis ke koneksi jaringan
   - Keyword `mut` diperlukan karena membaca dari stream mengubah state internalnya

2. **Pembacaan Buffer**:
   - Menggunakan `BufReader` untuk membaca input stream secara efisien
   - Melakukan buffering input, mengurangi jumlah system calls yang diperlukan

3. **Parsing Request**:
   - Menggunakan method `lines()` untuk membaca HTTP request baris per baris
   - `map()` digunakan untuk membuka setiap Result dari baris-baris tersebut
   - `take_while()` membaca sampai menemukan baris kosong (pembatas request HTTP)
   - `collect()` mengumpulkan semua baris ke dalam Vec

4. **Penanganan Error**:
   - Menggunakan `unwrap()` untuk tujuan demonstrasi, meskipun dalam kode produksi kita akan membutuhkan penanganan error yang lebih baik
   - Setiap baris diproses dengan aman melalui tipe Result

5. **Pemahaman Protokol HTTP**:
   - Request HTTP berbasis teks dan diakhiri dengan baris kosong
   - Kode menangkap seluruh header request HTTP
   - Format request mengikuti standar protokol HTTP/1.1

Implementasi ini menyediakan dasar untuk menangani request HTTP, meskipun saat ini hanya mencetak request tanpa mengirim response. Pengembangan selanjutnya akan mencakup penanganan response yang tepat dan manajemen error yang lebih baik.

# Catatan Refleksi Commit 2

## Analisis Implementasi Returning HTML

Pada commit kedua ini, kita telah mengembangkan web server untuk dapat mengirimkan respons HTML. Berikut adalah analisis dari perubahan yang dilakukan:

1. **Pembacaan File HTML**:
   - Menggunakan `fs::read_to_string()` untuk membaca konten file HTML
   - File HTML disimpan secara terpisah untuk memisahkan konten dari logika server
   - Penanganan error masih menggunakan `unwrap()` untuk kesederhanaan

2. **Pembentukan Response HTTP**:
   - Membuat status line "HTTP/1.1 200 OK" untuk mengindikasikan respons sukses
   - Menghitung panjang konten untuk header Content-Length
   - Menggunakan `format!` macro untuk menyusun respons lengkap dengan format yang benar

3. **Pengiriman Response**:
   - Menggunakan `write_all()` untuk mengirim respons ke client
   - Mengkonversi string response menjadi bytes dengan `as_bytes()`
   - Response dikirim sesuai format HTTP standar dengan headers dan body

4. **Struktur Response HTTP**:
   - Status line menunjukkan versi HTTP dan kode status
   - Headers memberikan informasi tentang konten
   - Baris kosong memisahkan headers dan body
   - Body berisi konten HTML yang akan ditampilkan di browser

5. **Pengembangan dari Commit Sebelumnya**:
   - Tidak hanya mencetak request, tapi memberikan respons yang berarti
   - Implementasi lebih mendekati web server yang fungsional
   - Mampu mengirimkan halaman web yang dapat dirender browser

![Commit 2 screen capture](/assets/images/commit2.png)

Implementasi ini menunjukkan bagaimana web server dapat mengirimkan konten statis ke browser. Langkah selanjutnya bisa mencakup penanganan berbagai jenis request dan konten dinamis.

# Catatan Refleksi Commit 3

## Analisis Implementasi Validasi Request dan Respons Selektif

Pada commit ketiga ini, kita telah mengembangkan web server untuk dapat memvalidasi request dan memberikan respons yang sesuai. Berikut adalah analisis dari perubahan dan pengembangan yang dilakukan:

1. **Refactoring Kode**:
   - Mengubah cara membaca request dari membaca seluruh headers menjadi hanya membaca baris pertama
   - Menggunakan pattern matching untuk menentukan respons yang sesuai
   - Memisahkan logika antara penanganan request yang valid dan invalid

2. **Validasi Request**:
   - Memeriksa baris pertama request untuk menentukan jenis request
   - Menggunakan pattern matching untuk membedakan antara request ke root path ("/") dan request lainnya
   - Implementasi sederhana yang hanya menangani GET request

3. **Respons Selektif**:
   - Mengembalikan halaman yang berbeda berdasarkan validitas request
   - Status 200 OK untuk request yang valid ke root path
   - Status 404 NOT FOUND untuk request yang tidak dikenali
   - Menggunakan file HTML terpisah untuk setiap jenis respons

4. **Pengembangan Struktur Proyek**:
   - Penambahan file 404.html untuk menangani error
   - Pemisahan konten antara successful response dan error response
   - Peningkatan modularitas dengan memisahkan konten statis

5. **Manajemen Response**:
   - Penggunaan tuple untuk mengelompokkan status line dan filename
   - Konsistensi dalam format response HTTP
   - Penanganan error yang lebih baik dengan memberikan feedback yang bermakna

![Commit 3 screen capture](/assets/images/commit3.png)

Implementasi ini menunjukkan bagaimana web server dapat memberikan respons yang berbeda berdasarkan request yang diterima. Meskipun masih sederhana, konsep ini adalah dasar dari routing dan penanganan request yang lebih kompleks dalam web server production. Pengembangan selanjutnya bisa mencakup:
- Penanganan berbagai jenis HTTP method (POST, PUT, DELETE, dll)
- Implementasi routing yang lebih kompleks
- Penanganan query parameters dan request body
- Implementasi middleware untuk validasi dan transformasi request

# Catatan Refleksi Commit 4

## Analisis Implementasi Simulasi Slow Response

Pada commit keempat ini, kita telah menambahkan simulasi slow response untuk mendemonstrasikan masalah dengan server single-threaded. Berikut adalah analisis mendalam dari perubahan dan implikasinya:

1. **Simulasi Beban Server**:
   - Menambahkan endpoint `/sleep` yang mensimulasikan proses yang memakan waktu
   - Menggunakan `thread::sleep` untuk mensimulasikan operasi yang berat
   - Delay 10 detik ditambahkan untuk menunjukkan dampak blocking

2. **Masalah Single-Thread**:
   - Server hanya memiliki satu thread untuk menangani semua request
   - Ketika satu request sedang diproses, request lain harus menunggu
   - Request ke `/sleep` memblokir seluruh server selama 10 detik
   - Request normal ke `/` harus menunggu sampai request `/sleep` selesai

3. **Implikasi pada Performa**:
   - Throughput server sangat terbatas
   - Tidak ada konkurensi dalam penanganan request
   - Response time meningkat secara signifikan saat ada request yang lambat
   - Server tidak dapat memanfaatkan multiple CPU cores

4. **Dampak pada User Experience**:
   - Pengguna harus menunggu lebih lama untuk mendapatkan response
   - Semua request terdampak oleh request yang lambat
   - Tidak ada perbedaan prioritas antara request cepat dan lambat
   - Potensi timeout pada client side untuk request yang terlalu lama menunggu

5. **Kebutuhan untuk Multi-threading**:
   - Single-threaded server tidak cocok untuk production
   - Perlu implementasi concurrent request handling
   - Thread pool bisa menjadi solusi untuk menangani multiple request
   - Perlu pertimbangan untuk asynchronous I/O

6. **Pembelajaran dari Simulasi**:
   - Pentingnya concurrent processing dalam web server
   - Dampak blocking operations pada performa server
   - Kebutuhan untuk arsitektur yang lebih scalable
   - Trade-off antara simplicity dan performance

![Commit 4 screen capture](/assets/images/commit4.png)

Simulasi ini dengan jelas menunjukkan keterbatasan server single-threaded dan mengapa multi-threading atau asynchronous processing diperlukan dalam web server production. Pengembangan selanjutnya akan fokus pada implementasi concurrent request handling untuk mengatasi masalah ini.
