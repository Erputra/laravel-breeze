Berikut adalah contoh kode dalam format Markdown dari contoh penggunaan Service Provider di Laravel untuk aplikasi dengan kebutuhan CRUD sederhana:

### Contoh CRUD Sederhana dengan Service Provider

#### Buat Service Provider Baru

Anda bisa membuat service provider baru dengan menggunakan Artisan command:

```sh
php artisan make:provider ExampleServiceProvider
```

Ini akan membuat file baru `ExampleServiceProvider.php` di direktori `app/Providers`.

#### Registrasi Service Provider

Tambahkan service provider yang baru dibuat ke dalam file `config/app.php` dengan menambahkannya ke array `providers`:

```php
'providers' => [
    // ...
    App\Providers\ExampleServiceProvider::class,
],
```

#### Konfigurasi Service Provider

Buka file `app/Providers/ExampleServiceProvider.php` dan tambahkan konfigurasi layanan di dalamnya. Misalnya, kita akan mengonfigurasi repository untuk model `Post`.

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Repositories\PostRepository;
use App\Repositories\EloquentPostRepository;

class ExampleServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        // Binding PostRepository interface to EloquentPostRepository implementation
        $this->app->bind(PostRepository::class, EloquentPostRepository::class);
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        //
    }
}
```

#### Buat Repository Interface dan Implementasi

**Interface** (`app/Repositories/PostRepository.php`):

```php
namespace App\Repositories;

interface PostRepository
{
    public function all();
    public function find($id);
    public function create(array $data);
    public function update($id, array $data);
    public function delete($id);
}
```

**Implementasi** (`app/Repositories/EloquentPostRepository.php`):

```php
namespace App\Repositories;

use App\Models\Post;

class EloquentPostRepository implements PostRepository
{
    public function all()
    {
        return Post::all();
    }

    public function find($id)
    {
        return Post::find($id);
    }

    public function create(array $data)
    {
        return Post::create($data);
    }

    public function update($id, array $data)
    {
        $post = $this->find($id);
        $post->update($data);
        return $post;
    }

    public function delete($id)
    {
        $post = $this->find($id);
        return $post->delete();
    }
}
```

#### Menggunakan Repository di Controller

Sekarang Anda dapat menggunakan repository di controller Anda dengan cara dependency injection. Contoh controller untuk CRUD:

```php
namespace App\Http\Controllers;

use App\Repositories\PostRepository;
use Illuminate\Http\Request;

class PostController extends Controller
{
    protected $postRepository;

    public function __construct(PostRepository $postRepository)
    {
        $this->postRepository = $postRepository;
    }

    public function index()
    {
        $posts = $this->postRepository->all();
        return view('posts.index', compact('posts'));
    }

    public function show($id)
    {
        $post = $this->postRepository->find($id);
        return view('posts.show', compact('post'));
    }

    public function create()
    {
        return view('posts.create');
    }

    public function store(Request $request)
    {
        $this->postRepository->create($request->all());
        return redirect()->route('posts.index');
    }

    public function edit($id)
    {
        $post = $this->postRepository->find($id);
        return view('posts.edit', compact('post'));
    }

    public function update(Request $request, $id)
    {
        $this->postRepository->update($id, $request->all());
        return redirect()->route('posts.index');
    }

    public function destroy($id)
    {
        $this->postRepository->delete($id);
        return redirect()->route('posts.index');
    }
}
```

### Kesimpulan

Dengan menggunakan service provider, Anda dapat dengan mudah mengelola dependensi dan mengatur inisialisasi berbagai layanan dalam aplikasi Laravel Anda. Meskipun kebutuhan Anda hanya untuk CRUD sederhana, menggunakan service provider membantu menjaga kode tetap terstruktur dan mudah diskalakan di masa depan. Ini juga memudahkan dalam melakukan perubahan dan integrasi dengan layanan eksternal atau fitur baru.