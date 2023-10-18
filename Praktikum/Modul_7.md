# Relasi One-to-Many dan Many-to-Many 
## Tujuan 
Setelah mengikuti praktikum ini, mahasiswa diharapkan dapat:
1. Memahami relasi dan foreign key
2. Mengimplementasikan relasi one-to-many
3. Mengimplementasikan relasi many-to-many

## Dasar Teori 
### Relasi
Hubungan antar tabel yang dilakukan dengan pencocokan primary key dengan foreign
key untuk mengombinasikan data dari satu tabel dengan tabel lainnya.

### Foreign Key
Properti yang digunakan untuk menandai hubungan dua tabel atau lebih. Foreign key
pada tabel anak (child) akan menunjuk tabel induk (parent) yang menjadi referensinya (reference).

### One-to-Many
Relasi yang menunjukkan hubungan antar tabel dimana baris pada tabel induk dapat
terhubung dengan satu atau lebih baris di tabel anak. Sementara baris pada tabel anak
hanya dapat terhubung dengan satu baris di tabel induk.<br>
Contoh penerapan one-to-many : 

* Satu tutorial dapat memiliki banyak komentar, namun satu komentar hanya dapat
berada di satu tutorial
* Satu dosbing dapat memiliki banyak mahasiswa, namun mahasiswa hanya dapat
dibimbing satu dosen

![](../Screenshot_7/contoh_gambar_1.png) <br><br>

### Many-to-Many
Relasi yang menunjukkan hubungan antar tabel dimana baris pada tabel induk dapat
terhubung dengan satu atau lebih baris di tabel anak. Berlaku sebaliknya pada tabel
anak yang dapat terhubung dengan satu atau lebih baris di tabel induk.<br>
Contoh penerapan many-to-many : 

* Satu mahasiswa dapat mengambil banyak mata kuliah, namun satu mata kuliah
dapat diambil banyak mahasiswa
* Postingan dapat memiliki banyak tag, namun satu tag dapat dimiliki banyak
postingan.

![](../Screenshot_7/contoh_gambar_2.png) <br><br>

### Junction Table
Tabel yang digunakan untuk mengatur kombinasi baris pada relasi many-to-many.
Junction table berisi foreign key dari kedua tabel yang memiliki relasi many-to-many.
Contoh penerapan junction table adalah tabel Enrollment pada relasi many-to-many di
atas

## Langkah Percobaan 
### Pembuatan Tabel 

(tabel 1)

1. Sebelum membuat migrasi database atau membuat tabel pastikan server database
aktif kemudian pastikan sudah membuat database dengan nama ```lumenpost```

![](../Screenshot_7/1.png) <br><br>

2. Kemudian ubah konfigurasi database pada file .env menjadi seperti berikut :
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=lumenpost
DB_USERNAME=root
DB_PASSWORD=
```

![](../Screenshot_7/2.png) <br><br>

3. Setelah mengubah konfigurasi pada file .env, kita juga perlu menghidupkan
beberapa library bawaan dari lumen dengan membuka file app.php pada folder
bootstrap dan mengubah baris ini : 
```
// $app->withFacades();
// $app->withEloquent();
```
menjadi : 
```
$app->withFacades();
$app->withEloquent();
```

![](../Screenshot_7/3.png) <br><br>

4. Setelah itu jalankan command berikut untuk membuat file migration :
```
php artisan make:migration create_posts_table
php artisan make:migration create_comments_table
php artisan make:migration create_tags_table
php artisan make:migration create_post_tag_table
```

![](../Screenshot_7/4.png) <br><br>

5. Ubah fungsi ```up()``` pada file ```migrasi create_post_table``` : 
```
#sebelumnya
...
public function up()
  {
    Schema::create('posts', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
    });
  }
...

#diubah menjadi
...
public function up()
  {
    Schema::create('posts', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
      $table->string('content');
    });
  }
...
```

![](../Screenshot_7/5.png) <br><br>

6. Ubah fungsi ```up()``` pada file ```create_comments_table``` : 
```
#sebelumnya
...
public function up()
  {
    Schema::create('comments', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
    });
  }
...

#diubah menjadi
...
public function up()
  {
    Schema::create('comments', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
      $table->string('review');
      $table->foreignId('postId')->unsigned();
    });
  }
...
```

![](../Screenshot_7/6.png) <br><br>

7. Ubah fungsi ```up()``` pada file ```create_tags_table``` :
```
#sebelumnya
...
public function up()
  {
    Schema::create('tags', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
    });
  }
...
#diubah menjadi
...
public function up()
  {
    Schema::create('tags', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
      $table->string('name');
    });
  }
...
```

![](../Screenshot_7/7.png) <br><br>

8. Ubah fungsi ```up()``` pada file ```create_post_tag_table``` :
```
#sebelumnya
...
public function up()
  {
    Schema::create('post_tag', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
    });
  }
...
#diubah menjadi
...
public function up()
  {
    Schema::create('post_tag', function (Blueprint $table) {
      $table->id();
      $table->timestamps();
      $table->foreignId('postId')->unsigned();
      $table->foreignId('tagId')->unsigned();
    });
  }
...
```

![](../Screenshot_7/8.png) <br><br>

9. Kemudian jalankan command : 
```
php artisan migrate
```

![](../Screenshot_7/9.png) <br><br>

### Pembuatan Model 
1. Buatlah file dengan nama Post.php dan isi dengan baris kode berikut : 
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
  /**
  * The attributes that are mass assignable.
  *
  * @var string[]
  */
  protected $fillable = [
  'content'
  ];
/**
  * The attributes excluded from the model's JSON form.
  *
  * @var string[]
  */
  protected $hidden = [];
}
```

![](../Screenshot_7/10.png) <br><br>

2. Buatlah file dengan nama Comment.php dan isi dengan baris kode berikut : 
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
  /**
  * The attributes that are mass assignable.
  *
  * @var string[]
  */
  protected $fillable = [
  'review'
  ];
  /**
  * The attributes excluded from the model's JSON form.
  *
  * @var string[]
  */
  protected $hidden = [];
}
```

![](../Screenshot_7/11.png) <br><br>

3.Buatlah file dengan nama Tag.php dan isi dengan baris kode berikut : 
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Tag extends Model
{
  /**
  * The attributes that are mass assignable.
  *
  * @var string[]
  */
  protected $fillable = [
  'name'
  ];
  /**
  * The attributes excluded from the model's JSON form.
  *
  * @var string[]
  */
  protected $hidden = [];
}
```

![](../Screenshot_7/12.png) <br><br>

### Relasi One-to-Many 
1. Tambahkan fungsi comments() pada file Post.php : 
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
...
// fungsi comments
public function comments()
  {
    return $this->hasMany(Comment::class, 'postId');
  }
}
```

![](../Screenshot_7/13.png) <br><br>

2. Tambahkan fungsi post() dan atribut postId pada $fillable pada file Comment.php : 
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
...
  protected $fillable = [
    'review',
    'postId' // atribut postId
  ];
  /**
  * The attributes excluded from the model's JSON form.
  *
  * @var string[]
  */
  protected $hidden = [];
  public function post()
    {
      return $this->belongsTo(Post::class, 'postId');
    }
}
```

![](../Screenshot_7/14.png) <br><br>

3. Buatlah file PostController.php dan isilah dengan baris kode berikut : 
```
<?php

namespace App\Http\Controllers;
use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
  /**
  * Create a new controller instance.
  *
  * @return void
  */
  public function __construct()
  {
      //
  }

//
public function createPost(Request $request)
{
  $post = Post::create([
    'content' => $request->content,
  ]);
  return response()->json([
    'success' => true,
    'message' => 'New post created',
    'data' => [
      'post' => $post
    ]
  ]);
}
public function getPostById(Request $request)
{
  $post = Post::find($request->id);
  return response()->json([
  'success' => true,
  'message' => 'All post grabbed',
  'data' => [
      'post' => [
          'id' => $post->id,
          'content' => $post->content,
          'comments' => $post->comments,
      ]
    ]
  ]);
}
}
```

![](../Screenshot_7/15.png) <br><br>

4. Buatlah file CommentController.php dan isilah dengan baris kode berikut :
```
<?php

namespace App\Http\Controllers;
use App\Models\Comment;
use Illuminate\Http\Request;

class CommentController extends Controller
{
  /**
  * Create a new controller instance.
  *
  * @return void
  */
  public function __construct()
  {
      //
  }
  //
  public function createComment(Request $request)
  {
    $comment = Comment::create([
      'review' => $request->review,
      'postId' => $request->postId,
    ]);
  return response()->json([
      'success' => true,
      'message' => 'New comment created',
      'data' => [
          'comment' => $comment
      ]
    ]);
  }
}
```

![](../Screenshot_7/16.png) <br><br>

5. Tambahkan baris berikut pada routes/web.php : 
```
<?php
...
$router->group(['prefix' => 'posts'], function () use ($router) {
    $router->post('/', ['uses' => 'PostController@createPost']);
    $router->get('/{id}', ['uses' => 'PostController@getPostById']);
});
$router->group(['prefix' => 'comments'], function () use ($router) {
    $router->post('/', ['uses' => 'CommentController@createComment']);
});
```

![](../Screenshot_7/17.png) <br><br>

6. Buatlah satu post menggunakan Postman

![](../Screenshot_7/18.png) <br><br>

7. Buatlah satu comment menggunakan Postman

![](../Screenshot_7/19.png) <br><br>

8. Tampilkan post menggunakan Postman

![](../Screenshot_7/20.png) <br><br>

### Relasi Many-to-Many 
1. Tambahkan fungsi tags() pada file Post.php :
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
  ...
  public function tags()
  {
    return $this->belongsToMany(Tag::class, 'post_tag', 'postId', 'tagId');
  }
}
```

![](../Screenshot_7/21.png) <br><br>

2. Tambahkan fungsi posts() pada file Tag.php :
```
<?php

namespace App\Models;
use Illuminate\Database\Eloquent\Model;
class Tag extends Model
{
  ...
  public function posts()
  {
    return $this->belongsToMany(Post::class, 'post_tag', 'tagId', 'postId');
  }
}
```

![](../Screenshot_7/22.png) <br><br>

3. Buatlah file TagController.php dan isilah dengan baris kode berikut :
```
<?php

namespace App\Http\Controllers;
use App\Models\Tag;
use Illuminate\Http\Request;

class TagController extends Controller
{
  /**
  * Create a new controller instance.
  *
  * @return void
  */
  public function __construct()
  {
      //
  }
  //
  public function createTag(Request $request)
  {
      $tag = Tag::create([
        'name' => $request->name
      ]);
      return response()->json([
          'success' => true,
          'message' => 'New tag created',
          'data' => [
              'tag' => $tag
          ]
      ]);
  }
}
```

![](../Screenshot_7/23.png) <br><br>

4. Tambahkan fungsi addTag dan response tags pada PostController.php :
```
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    ...
    public function getPostById(Request $request)
    {
        $post = Post::find($request->id);
        return response()->json([
            'success' => true,
            'message' => 'All post grabbed',
            'data' => [
                'post' => [
                    'id' => $post->id,
                    'content' => $post->content,
                    'comments' => $post->comments,
                    'tags' => $post->tags, //response tags
                ]
            ]
        ]);
    }
    public function addTag(Request $request)
    {
      $post = Post::find($request->id);
      $post->tags()->attach($request->tagId);
      return response()->json([
          'success' => true,
          'message' => 'Tag added to post',
      ]);
    }
}
```

![](../Screenshot_7/24.png) <br><br>

5. Tambahkan baris berikut pada routes/web.php :
```
$router->group(['prefix' => 'posts'], function () use ($router) {
    $router->post('/', ['uses' => 'PostController@createPost']);
    $router->get('/{id}', ['uses' => 'PostController@getPostById']);
    $router->put('/{id}/tag/{tagId}', ['uses' => 'PostController@getPostById']); //
});
...
$router->group(['prefix' => 'tags'], function () use ($router) {
    $router->post('/', ['uses' => 'TagController@createTag']);
});
```

![](../Screenshot_7/25.png) <br><br>

6. Buatlah satu tag menggunakan Postman

![](../Screenshot_7/26.png) <br><br>

7. Tambahkan tag “jadul” pada post “disana engkau berdua”

![](../Screenshot_7/27.png) <br><br>

8. Tampilkan post “disana engkau berdua” menggunakan Postman

![](../Screenshot_7/28.png) <br><br>

9. Buatlah postingan “tanpamu apa artinya” menggunakan Postman

![](../Screenshot_7/29.png) <br><br>

10. Tambahkan tag “jadul” pada postingan “tanpamu apa artinya”

![](../Screenshot_7/30.png) <br><br>

11. Buatlah tag “lagu” menggunakan Postman

![](../Screenshot_7/31.png) <br><br>

12. Tambahkan tag “lagu” pada postingan “tanpamu apa artinya”

![](../Screenshot_7/32.png) <br><br>

13. Tampilkan post pertama

![](../Screenshot_7/33.png) <br><br>

14. Tampilkan post kedua

![](../Screenshot_7/34.png) <br><br>
