# Siklus hidup Request Laravel
## Introduction
    Ketika menggunakan tool apapun di real word, kamu merasa lebih percaya diri jika kamu paham bagaimana cara tool itu bekerja, Development aplikasi juga tidak berbeda. Ketika kamu memahami bagamana Aplikasi Development mu bekerja, kamu akan merasa lebih nyaman dan percaya ketika menggunakannya.

## Lifecycle Overview

### Langkah Pertama
        Entri point untuk semua request ke Aplikasi Laravel adalah file Public/index.php. Semua request diarahkan ke file tersebut oleh konfigurasi web server anda (Apache/ Nginx). File index.php tidak berisi banyak kode, Sebaliknya, Ini adalah titik awal untuk memuat bagian lain dari framework. File index.php memuat definisi autoloader yang dihasilkan oleh Composer, dan kemudian mengambil Instance dari Aplikasi Laravel dari file *bootstrap/app.php*. Langkah pertama yang diambil oleh laravel itu sendiri adalah membuat sebuah instance dari Aplikasi atau Service Container. Service Container adalah komponen inti dari Laravel yang bertanggung jawab untuk mengelola dependensi dan melakukan injeksi dependensi. Ini memungkinkan Object dan Class yang digunakan dalam aplikasi dengan cara yang lebih fleksibel dan terogranisir.

### HTTP / COnsole Kernels
Selanjutnya, permintaan yang masuk dikirim ke kernel HTTP atau kernel console, menggunakan metode *handleRequest* atau *handleCommand* dari Instance aplikasi, Tergantung pada jenis permintaan yang masuk ke Aplikasi Laravel. Kedua kernel ini berfungsi sebagai pusat lokasi dimana semua permintaan mengalir. Untuk sekarang mari cukup fokus ke HTTP Kernels, Yang mana instance dari Illuminate\Foundation\Htpp\Kernel.

HTTP Kernel mendefinisikan sebuah array dari bootstrappers yang akan di jalankan sebelum request di eksekusi. Bootstrappers ini mengatur penangganan error, pengaturan sistem log, mendeteksi environment aplikasi s (seperti local, production, testing) agar konfigurasi yang tepat dapat diterapkan dan melaksanakan tugas lain yang butuh di selesaikan sebelum request benar-benar di handel. Biasanya, class-class ini menghandel Configurasi Internal laravel itu tidak perlu kamu khawatirkan.

Berikut adalah beberapa contoh kelas bootstrap yang biasa digunakan:
*Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables: Memuat variabel lingkungan dari file .env.
*Illuminate\Foundation\Bootstrap\LoadConfiguration: Memuat konfigurasi aplikasi dari file konfigurasi.
*Illuminate\Foundation\Bootstrap\HandleExceptions: Mengonfigurasi penanganan kesalahan dan pengecualian.
*Illuminate\Foundation\Bootstrap\RegisterFacades: Mendaftarkan facades yang digunakan oleh aplikasi.
*Illuminate\Foundation\Bootstrap\RegisterProviders: Mendaftarkan semua service providers yang dibutuhkan aplikasi.
*Illuminate\Foundation\Bootstrap\BootProviders: Menjalankan metode boot pada semua service providers.

Http Kernal juga bertanggung jawab untuk meneruskan request melalui stack middleware aplikasi. Middleware ini menangani pembacaan dan penulisan HTTP Session, Menentukan jika aplikasi sedang dalam mode maintenance, memverifikasi CSRF token, dan yang lain. Kita akan bicara lebih dalam tentang hal itu nanti.

Method signature untuk HTTP Kernels mengangani *method* cukup simple: ia menerima sebuah *request* dan mengembalikan sebuah *response*. Pikirkan kernel sebagai sebuah Black Box yang mewakili seluruh aplikasi anda. Berikan Request HTTP kepadanya dan itu akan mengembalikan Response HTTP.

### Service Providers
Salah satu tugas penting Kernel Bootstrapping adalah memuat *Service Providers* untuk aplikasi. Service Providers bertanggung jawab untuk bootstrapping semua komponen dari framework, Seperti database, queue, validasi, dan routing komponen.

Laravel akan mengiterasi daftar provider ini dan membuat instance masing-masing dari mereka. Setelah membuat instance Provider, method *register* akan di panggil pada semua providers. Kemudian, Ketika semua provider telah terdaftar, Method *boot* akan di panggil pada tiap provider. Hal ini agar service provider dapat bergantung pada setiap binding *Container* yang telah terdaftar dan tersedia saat metode *boot* mereka jalankan.

Pada dasarnya setiap fitur utama yang di tawarkan oleh laravel di inisialisasi dan di konfigurasi oleh Service Provider. Karena mereka menginisialisasi dan mengkonfigurasi banyak fitur yang di tawarkan oleh framework, Service provider adalah aspek paling penting dari seluruh proses bootstrap laravel. Meskipun framework secara internal menggunakan puluhan service provider, Anda juga memiliki opsi untuk membuat service provider anda sendiri. Anda dapat menemukan daftar service provider yang di tentukan pengguna atau pihak ketiga yang di gunakan oleh aplikasi anda di file *bootstrap/providers.php*.

### Routing
Saat aplikasi sudah selesai di inisialisai(bootstrapped) dan semua service provider sudah di daftarkan, Request akan diserahkan kepada router yang di dispatching (outer mengarahkan permintaan HTTP ke tujuan). Router akan mengarahkan request ke route atau controller, Serta menjalankan middleware khusus route apapun.

Middleware menyediakan mekanisme yang nyaman untuk menyaring atau memeriksa HTTP Reqeust yang masuk ke aplikasi. Sebagai Contoh, Laravel menyertakan sebuah middleware yang memverifikasi apakah pengguna aplikasi telah terautentikasi. Jika pengguna tidak terautentikasi, Middleware akan mengarahkan pengguna ke halaman login. Namun, Jika pengguna sudah terautentikasi maka middleware akan mengizinkan Request untuk di proses lebih lanjut ke aplikasi. Beberapa middleware di tugaskan untuk semua rute dalam aplikasi, Seperti *PreventRequestsDuringMaintenance*, Sementara beberapa hanya ditugaskan ke route spesifik atau group route. Anda bisa belajar lebih tetang middleware dengan membaca dokumentasi middleware documentation.

Jika Request melewati semua middleware yang ditugaskan pada route yang cocok, method Route atau controller akan di eksekusi dan Response dikembalikan oleh route atau controller melalui rantai middleware dari route tersebut.

### Finishing Up
Ketika route atau controller mengembalikan sebuah response, Response akan melakukan perjalanan kembali melalui middleware rute tersebut, memberi aplikasi kesempatan untuk memodifikasi atau meneliti response keluar.

Akhirnya, ketika response melakukan perjalanan kembali melalui middleware, Method *handle* dari HTTP Kernel mengembalikan object response ke method *handleRequest* dari Instance aplikasi, dan method ini memanggil method *send* pada response yang di kembalikan. Method *send* mengirimkan Response Content ke penggunga web browser. Kita sekarang sudah menyelesaikan perjalanan kita melalui seluruh siklus __Request Lifecycle__.

### Focus on Service Providers
Service providers benar-benar kunci dalam proses bootstrapping aplikasi laravel. Instance aplikasi di buat, Service provider di daftarkan, dan request di tanggani ke bootstrapped aplikasi. Ini benar-benar sesimple itu.

Memiliki pemahaman dasar dari bagaimana sebuah aplikasi laravel di bangun dan bootrapped melalui service providers sangat berharga. Service provider yang di definisikan oleh Pengguna di simpan di direktori *app/Providers*.

Seracara default, *AppServiceProvider* itu kosong. Provider ini adalah tempat yang bagus untuk menambahkan bootstrapping aplikasi anda sendiri dan service container binding. Untuk Aplikasi besar, Anda mungkin ingin membuat beberapa service providers, masing-masing dengan bootstrapping yang lebih terperinci untuk service spesifik yang digunakan oleh aplikasi anda. 