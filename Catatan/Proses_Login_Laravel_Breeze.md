
# Proses Login Laravel Breeze

Ketika pengguna login menggunakan Laravel Breeze, Laravel melakukan serangkaian langkah dan proses yang terorganisir dengan baik. Berikut adalah penjelasan dari langkah-langkah tersebut dan lokasi di mana proses tersebut terjadi:

### 1. Menampilkan Halaman Login
- **Lokasi**: `resources/views/auth/login.blade.php`
- Ketika pengguna mengunjungi halaman login, Laravel Breeze menampilkan template Blade yang telah disiapkan. Template ini berisi formulir login dengan field untuk email dan password.

### 2. Mengirim Data Login
- **Lokasi**: `resources/views/auth/login.blade.php`
- Ketika pengguna mengisi formulir login dan mengirimkannya, data tersebut dikirim melalui metode POST ke rute yang ditentukan untuk login.

### 3. Menangani Permintaan Login
- **Lokasi**: `app/Http/Controllers/Auth/AuthenticatedSessionController.php`
- Rute login diarahkan ke metode `store` di `AuthenticatedSessionController`. Metode ini menangani logika login pengguna.

### 4. Validasi Data Login
- **Lokasi**: `app/Http/Controllers/Auth/AuthenticatedSessionController.php`
- Di dalam metode `store`, data login (email dan password) divalidasi menggunakan Laravel Validator untuk memastikan bahwa data yang dikirim memenuhi kriteria yang diperlukan.

### 5. Mencoba Login Pengguna
- **Lokasi**: `app/Http/Controllers/Auth/AuthenticatedSessionController.php`
- Setelah validasi berhasil, Laravel menggunakan facade `Auth` untuk mencoba login pengguna dengan data yang diberikan:
  ```php
  if (Auth::attempt($credentials)) {
      // Login berhasil
  }
  ```

### 6. Memeriksa Status Verifikasi Email
- **Lokasi**: `app/Http/Controllers/Auth/AuthenticatedSessionController.php`
- Jika login berhasil, Laravel memeriksa apakah email pengguna telah diverifikasi (jika fitur verifikasi email diaktifkan):
  ```php
  if ($user->hasVerifiedEmail()) {
      // Email telah diverifikasi
  }
  ```

### 7. Menerapkan Middleware
- **Lokasi**: `app/Http/Kernel.php` dan rute yang didefinisikan
- Middleware `auth` dan `verified` diterapkan pada rute-rute tertentu untuk memastikan bahwa hanya pengguna yang telah login dan memverifikasi email yang dapat mengaksesnya:
  ```php
  Route::middleware(['auth', 'verified'])->group(function () {
      Route::get('/dashboard', [DashboardController::class, 'index'])->name('dashboard');
  });
  ```

### 8. Mengatur Session dan Redirect
- **Lokasi**: `app/Http/Controllers/Auth/AuthenticatedSessionController.php`
- Jika login berhasil dan pengguna telah diverifikasi, Laravel mengatur session pengguna dan mengarahkan mereka ke halaman yang ditentukan, seperti dashboard:
  ```php
  return redirect()->intended('dashboard');
  ```

### Dokumentasi Terkait
Untuk informasi lebih lanjut tentang proses ini, kamu bisa merujuk ke dokumentasi resmi Laravel:

- [Laravel Breeze Documentation](https://laravel.com/docs/10.x/starter-kits#laravel-breeze)
- [Authentication Documentation](https://laravel.com/docs/10.x/authentication)
- [Email Verification Documentation](https://laravel.com/docs/10.x/verification)

Dengan mengikuti langkah-langkah di atas, Laravel memastikan bahwa proses login pengguna berjalan dengan lancar dan aman.
