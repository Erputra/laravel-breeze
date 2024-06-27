# Bootstrapping dalam Laravel

Istilah "bootstrap" dalam konteks perangkat lunak memiliki makna yang berbeda dibandingkan dengan "Bootstrap" yang merupakan framework CSS.

## Bootstrap dalam Perangkat Lunak

"Bootstrap" dalam konteks perangkat lunak merujuk pada proses inisialisasi dan pengaturan awal dari sebuah sistem atau aplikasi sebelum aplikasi tersebut siap untuk digunakan. Konsep ini berasal dari ungkapan "pulling oneself up by one's bootstraps," yang berarti memulai sesuatu dari awal dengan sedikit atau tanpa bantuan.

## Bootstrapping dalam Laravel

Dalam konteks Laravel, "bootstrapping" merujuk pada proses di mana framework mempersiapkan dirinya sendiri untuk dijalankan. Ini mencakup inisialisasi berbagai komponen dan layanan yang diperlukan oleh aplikasi Laravel untuk berfungsi dengan benar. Proses bootstrapping ini terjadi sebelum aplikasi mulai menerima dan memproses permintaan dari pengguna.

## Proses Bootstrapping Laravel

1. **Load Configuration**:
    - Laravel memuat file konfigurasi dari direktori `config`. Ini termasuk pengaturan untuk database, mail, services, dll.

2. **Register Service Providers**:
    - Laravel mendaftarkan semua service providers yang tercantum dalam file `config/app.php`.

    ```php
    'providers' => [
        Illuminate\Auth\AuthServiceProvider::class,
        Illuminate\Broadcasting\BroadcastServiceProvider::class,
        Illuminate\Bus\BusServiceProvider::class,
        // Lainnya...
    ],
    ```

3. **Register Container Bindings**:
    - Masing-masing service provider kemudian mendaftarkan binding layanan ke dalam kontainer layanan.

4. **Boot Service Providers**:
    - Setelah semua layanan didaftarkan, metode `boot` dari setiap service provider dipanggil untuk menyelesaikan inisialisasi yang diperlukan.

5. **Load Routes**:
    - Laravel memuat rute aplikasi dari file di direktori `routes`.

6. **Handle Request**:
    - Setelah semua komponen diinisialisasi, Laravel siap untuk menerima dan memproses permintaan HTTP.

## Service Providers

Service providers adalah komponen yang paling penting dalam proses bootstrapping Laravel. Mereka menginisialisasi dan mengkonfigurasi fitur utama framework.

### Membuat Service Provider Sendiri

Selain menggunakan service providers bawaan Laravel, Anda juga bisa membuat service provider sendiri. Berikut adalah langkah-langkahnya:

1. **Membuat Service Provider**:
    - Anda bisa menggunakan Artisan command untuk membuat service provider baru.

    ```sh
    php artisan make:provider MyServiceProvider
    ```

    - Ini akan membuat file baru di `app/Providers/MyServiceProvider.php`.

2. **Mendaftarkan Service Provider**:
    - Setelah membuat service provider, Anda harus mendaftarkannya di file `config/app.php`.

    ```php
    'providers' => [
        // Daftar service providers
        App\Providers\MyServiceProvider::class,
    ],
    ```

3. **Mengisi Metode Register dan Boot**:
    - Implementasikan metode `register` dan `boot` dalam service provider Anda.

    ```php
    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;

    class MyServiceProvider extends ServiceProvider
    {
        public function register()
        {
            // Mendaftarkan layanan ke dalam kontainer
            $this->app->singleton('MyService', function ($app) {
                return new \App\Services\MyService();
            });
        }

        public function boot()
        {
            // Inisialisasi setelah semua layanan terdaftar
        }
    }
    ```

## Daftar Service Providers yang Digunakan

Anda dapat menemukan daftar service provider yang didefinisikan oleh pengguna atau pihak ketiga yang digunakan oleh aplikasi Anda di file `bootstrap/cache/services.php`. File ini berisi semua service provider yang telah dikompilasi untuk mempercepat proses bootstrapping.

```php
return [
    'providers' => [
        // Daftar service providers
        App\Providers\AuthServiceProvider::class,
        App\Providers\EventServiceProvider::class,
        App\Providers\RouteServiceProvider::class,
        // Lainnya...
    ],

    'eager' => [
        // Service providers yang di-load secara eager
    ],

    'deferred' => [
        // Service providers yang di-load secara lazy
    ],
];
