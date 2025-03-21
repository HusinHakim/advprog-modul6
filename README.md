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
