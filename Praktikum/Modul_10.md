# Integrasi Frontend
## Tujuan 
Setelah mengikuti praktikum ini, mahasiswa diharapkan dapat:
1. Memahami cara mengubah primary key pada model
2. Memahami cara mengambil user dengan token
3. Memahami cara mengatasi CORS block
4. Memahami cara mengambil semua data berikut relasinya
5. Memahami permasalahan drilling data
6. Memahami permasalahan urutan route
7. Mengimplementasikan integrasi ke React menggunakan axios
8. Mengimplementasikan integrasi ke Laravel menggunakan Guzzle

## Tips 
### Mengubah Primary Key
1. Tambahkan →primary() di kolom yang dituju pada migration
```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateProduct extends Migration
{
    /**
    * Run the migrations.
    *
    * @return void
    */
    public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->string('productId')->primary();
            $table->string('name');
            $table->timestamps();
        });
    }
    /**
    * Reverse the migrations.
    *
    * @return void
    */
    public function down()
    {
        Schema::dropIfExists('products');
    }
}
```
2. Tambahkan protected $primaryKey pada model
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    protected $primaryKey = 'productId';
    
    /**
    * The attributes that are mass assignable.
    *
    * @var array
    */
    protected $fillable = [
        'nama',
    ];
    /**
    * The attributes excluded from the model's JSON form.
    *
    * @var array
    */
    protected $hidden = [];
}
```

### Mengambil user menggunakan token 
1. Karena $request→user sudah ditempel dari middleware, buatlah controller yang langsung menampelkan request
```
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function getUserByToken(Request $request)
    {
        return response()->json([
            'success' => true,
            'message' => 'grabbed user by token',
            'user' => $request->user,
        ], 200);
    }
}
```

### Mengatasi CORS block
1. Buatlah CorsMiddleware.php pada folder middleware
```
<?php

namespace App\Http\Middleware;

use Closure;

class CorsMiddleware
{
    /**
    * Handle an incoming request.
    *
    * @param \Illuminate\Http\Request $request
    * @param \Closure $next
    * @return mixed
    */
    public function handle($request, Closure $next)
    {
        $headers = [
            'Access-Control-Allow-Origin' => '*',
            'Access-Control-Allow-Methods' => 'POST, GET, OPTIONS, PUT, DELETE',
            'Access-Control-Allow-Credentials' => 'true',
            'Access-Control-Max-Age' => '86400',
            'Access-Control-Allow-Headers' => 'Content-Type, Authorization, X-Requested-With, token'
        ];

        if ($request->isMethod('OPTIONS'))
        {
            return response()->json('{"method":"OPTIONS"}', 200, $headers);
        }

        $response = $next($request);
        foreach($headers as $key => $value)
        {
            $response->header($key, $value);
        }

        return $response;
    }
}
```
2. Daftarkan middleware di atas routeMiddleware agar berdampak bagi semua route yang ada
```
<?php

...

$app->middleware([ // disini
    App\Http\Middleware\CorsMiddleware::class
]);

$app->routeMiddleware([ // bukan disini
    'jwt' => App\Http\Middleware\JwtMiddleware::class,
]);

...

return $app;
```

### Mengambil semua data berikut relasinya
1. Gunakan ::with(”nama-relasi”)→get(). Nama relasi diambil dari fungsi yang telah dibuat pada model
```
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    //
    public function getAllPost()
    {
      $posts = Post::with('author')->get();

      return response()->json([
        'success' => true,
        'message' => 'grabbed all posts',
        'posts' => $posts,
      ], 200);
    }
}
```
2. Fungsi ::with dapat digunakan untuk dua relasi atau lebih
```
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function getPostById(Request $request)
    {
        $post = Post::with('comments', 'author')->find($request->id);

        return response()->json([
          'success' => true,
          'message' => 'grabbed one post',
          'post' => $post,
        ], 200);
    }
}
```

### Permasalahan drilling data
1. Karena axios menyimpan response dalam property data. Gunakan pendekatan di bawah agar
tidak perlu terlalu dalam mengambil data
```
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    // res.data.posts
    public function getAllPost()
    {
        $posts = Post::with('author')->get();

        return response()->json([
            'success' => true,
            'message' => 'grabbed all posts',
            'posts' => $posts, // gud
        ], 200);
    }
    // res.data.data.posts
    public function getAllMahasiswa()
    {
    $posts = Post::with('author')->get();

    return response()->json([
      'success' => true,
      'message' => 'grabbed all posts',
      'data' => [ // not gud
        'posts' => $posts,
      ],
    ], 200);
}
```

### Permasalahan urutan route
1. Route /{id} akan dibaca terlebih dahulu sehingga /profile tidak akan masuk dalam route
```
$router->group(['prefix' => 'user'], function () use ($router) {
    $router->get('/', ['uses' => 'UserController@getAll']);
    $router->get('/{id}', ['uses' => 'UserController@getById']);
    $router->get('/profile', ['uses' => 'UserController@getByToken']);
});
```
2. Route /profile akan dibaca terlebih dahulu sebelum masuk ke route /{id}
```
$router->group(['prefix' => 'user'], function () use ($router) {
    $router->get('/', ['uses' => 'UserController@getAll']);
    $router->get('/profile', ['uses' => 'UserController@getByToken']);
    $router->get('/{id}', ['uses' => 'UserController@getById']);
});
```

## Tugas 
### Ketentuan Kelompok
1. Beranggotakan 1-4 orang
2. Dapat dilakukan individu atau kelompok
3. Daftar nama dituliskan ke spreadsheet yang akan dibagikan oleh asisten

### Ketentuan Pengerjaan
1. Asisten akan menyediakan frontend berbentuk Next.JS, praktikan membuat API menyesuaikan
frontend yang diberikan
2. Backend yang digunakan dibebaskan sesuai kreativitas praktikan
3. Deployment tidak diwajibkan, namun dipersilahkan bagi yang ingin.
4. Asisten akan memberikan waktu untuk mencoba environtment frontend yang diberikan,
memastikan dapat berjalan di salah satu anggota kelompok
5. Memilih satu dari keempat tema yang disediakan
6. Keempat tema wajib memiliki fitur register, login, dan verifikasi JWT
7. Keempat tema memiliki perbedaan di implementasi tabel dan relasi (akan dijelaskan di bawah)
8. Keempat tema memiliki nilai yang berbeda

## Tema
### Mahasiswa (Easy)
1. Menggunakan satu tabel
<table>
 	<tr>
 		<td> mahasiswas </td>
 	</tr>
 	<tr>
 		<td> nim (PK, string) </td>
 	</tr>
  <tr>
 		<td> nama (string) </td>
 	</tr>
  <tr>
 		<td> angkatan (int) </td>
 	</tr>
  <tr>
 		<td> password (string) </td>
 	</tr><tr>
 		<td> password </td>
 	</tr>
 </table>

2. Tidak ada relasi antar tabel
2. Mahasiswa dapat melakukan register dan login
3. Mahasiswa yang berhasil login dapat melihat mahasiswa lainnya dalam bentuk tabel

### Mahasiswa dan Prodi (Medium)
1. Menggunakan dua tabel
2. Data yang digunakan
3. Terdapat relasi one prodi to many mahasiswa
    a. Satu prodi dapat memiliki banyak mahasiswa
    b. Satu mahasiswa hanya dapat berada di satu prodi
4. Mahasiswa dapat melakukan register dan login
5. Mahasiswa memilih prodi dalam bentuk dropdown pada saat register
6. Mahasiswa yang berhasil login dapat melihat mahasiswa lainnya dalam bentuk tabel

### Mahasiswa dan Mata Kuliah (Hard)
1. Menggunakan tiga tabel
2. Data yang digunakan
3. Terdapat relasi many mahasiswa to many mata kuliah <br>
    a. Satu mahasiswa dapat memiliki banyak mata kuliah <br>
    b. Satu mata kuliah dapat dimiliki banyak mahasiswa
4. Mahasiswa dapat melakukan register dan login
5. Mahasiswa memilih mata kuliah di menu “Tambah Mata Kuliah” dengan klik tombol
6. Mahasiswa yang berhasil login dapat melihat mahasiswa lainnya dalam bentuk tabel
7. Mahasiswa dapat melihat daftar mata kuliah yang diambil setelah klik tombol “Detail Mahasiswa”

### Mahasiswa, Prodi, dan Mata Kuliah (Expert)
1. Menggunakan empat tabel
2. Data yang digunakan
3. Terdapat relasi one prodi to many mahasiswa <br>
    a. Satu prodi dapat memiliki banyak mahasiswa <br>
    b. Satu mahasiswa hanya dapat berada di satu prodi
4. Terdapat relasi many mahasiswa to many mata kuliah <br>
    a. Satu mahasiswa dapat memiliki banyak mata kuliah <br>
    b. Satu mata kuliah dapat dimiliki banyak mahasiswa
5. Mahasiswa dapat melakukan register dan login
6. Mahasiswa memilih prodi dalam bentuk dropdown pada saat register
7. Mahasiswa memilih mata kuliah di menu “Tambah Mata Kuliah” dengan klik tombol
8. Mahasiswa yang berhasil login dapat melihat mahasiswa lainnya dalam bentuk tabel
9. Mahasiswa dapat melihat daftar mata kuliah yang diambil setelah klik tombol “Detail Mahasiswa”

## Pengumpulan
1. Pekerjaan di push ke github salah satu anggota kelompok.
2. Link Github dikumpulkan di spreadsheet
3. Melampirkan link deployment (jika ada)

## Presentasi
1. Presentasi dapat dilakukan ketika kelompok telah menyelesaikan tugas
2. Presentasi dilakukan di laptop masing-masing praktikan.
3. Diwajibkan semua anggota hadir saat presentasi (kecuali izin tertentu)
4. Presentasi dapat disampaikan oleh perwakilan atau bergantian
5. Pilihan pelaksanaan presentasi <br>
    a. Luring di kelas <br>
    b. Luring di luar kelas (sesuai waktu dan tempat yang dijanjikan) <br>
    c. Daring
6. Asisten berhak memberikan pertanyaan
7. Asisten akan memberikan feedback di akhir jika ada

## Penilaian
1. Berdasarkan tema yang dipilih
2. Penilaian setiap anggota kelompok disamakan
3. Anggota yang mengerjakan proyek secara individu memiliki penilaian berbeda

## Endpoint
