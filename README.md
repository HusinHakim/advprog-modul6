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
