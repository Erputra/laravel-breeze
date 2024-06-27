# Siklus hidup Request Laravel
## Introduction
    Ketika menggunakan tool apapun di real word, kamu merasa lebih percaya diri jika kamu paham bagaimana cara tool itu bekerja, Development aplikasi juga tidak berbeda. Ketika kamu memahami bagamana Aplikasi Development mu bekerja, kamu akan merasa lebih nyaman dan percaya ketika menggunakannya.

## Lifecycle Overview

### Langkah Pertama
        Entri point untuk semua request ke Aplikasi Laravel adalah file Public/index.php. Semua request diarahkan ke file tersebut oleh konfigurasi web server anda (Apache/ Nginx). File index.php tidak berisi banyak kode, Sebaliknya, Ini adalah titik awal untuk memuat bagian lain dari framework. File index.php memuat definisi autoloader yang dihasilkan oleh Composer, dan kemudian mengambil Instance dari Aplikasi Laravel dari file *bootstrap/app.php*. Langkah pertama yang diambil oleh laravel itu sendiri adalah membuat sebuah instance dari Aplikasi atau Service Container. Service Container adalah komponen inti dari Laravel yang bertanggung jawab untuk mengelola dependensi dan melakukan injeksi dependensi. Ini memungkinkan Object dan Class yang digunakan dalam aplikasi dengan cara yang lebih fleksibel dan terogranisir.

### HTTP / COnsole Kernels
Selanjutnya, permintaan yang masuk dikirim ke kernel HTTP atau kernel console, menggunakan metode *handleRequest* atau *handleCommand* dari Instance aplikasi, Tergantung pada jenis permintaan yang masuk ke Aplikasi Laravel. Kedua kernel ini berfungsi sebagai pusat lokasi dimana semua permintaan mengalir. 