## Langkah Percobaan
1. GET <br><br>
   Untuk menambahkan endpoint dengan method GET pada aplikasi kita, kita dapat mengunjungi file     web.php pada folder routes. Kemudian tambahkan baris ini pada akhir file <br><br>
```
...
$router->get('/get', function () {
  return 'GET';
});
```
![](../Screenshot_4/1.png) <br><br>
  Setelah itu coba jalankan aplikasi dengan command,
```
php -S localhost:8000 -t public
```
![](../Screenshot_4/2.png) <br><br>
  ***Note : Pastikan buka cmd pada folder aplikasi*** <br>
  
Setelah aplikasi berhasil dijalankan, kita dapat membuka browser dengan url, 
```
http://localhost:8000/get
```
![](../Screenshot_4/3.png) <br><br>
path yang akan kita akses akan berbentuk demikian, ```http://{BASE_URL}{PATH}``` , jika BASE_URL kita adalah ```localhost:8000``` dan PATH kita adalah ```/get``` , maka url akan berbentuk seperti diatas.

2. POST, PUT, PATCH, DELETE, dan OPTIONS <br><br>
Sama halnya saat menambahkan method GET, kita dapat menambahkan methode POST, PUT, PATCH, DELETE, dan OPTIONS pada file web.php dengan code seperti ini : 
```
...
$router->post('/post', function () {
    return 'POST';
});
$router->put('/put', function () {
    return 'PUT';
});
$router->patch('/patch', function () {
    return 'PATCH';
});
$router->delete('/delete', function () {
    return 'DELETE';
});
$router->options('/options', function () {
    return 'OPTIONS';
});
```
![](../Screenshot_4/4.png) <br><br>
Setelah selesai menambahkan route untuk method POST, PUT, PATCH, DELETE, dan OPTIONS, kita dapat menjalankan server seperti pada saat percobaan GET. Setelah server berhasil menyala, kita dapat membuka aplikasi Postman atau Insomnia atau kita juga dapat menggunakan PowerShell (Windows) / Terminal (Linux atau Mac) untuk melakukan request ke server. Namun, pada percobaan kali ini kita akan menggunakan extensions pada VSCode yaitu Thunder Client. <br><br>

- Kita dapat menginstall ekstensi dengan membuka panel extensions lalu mencari thunder client <br><br>
![](../Screenshot_4/5.png) <br><br>
- Setelah menginstall Thunder Client, kita akan melihat logo seperti petir pada activity bar kita (sebelah kiri) <br><br>
![](../Screenshot_4/6.png) <br><br>
- Kita dapat membuat request dengan menekan "New Request" pada ekstensi <br><br>
![](../Screenshot_4/7.png) <br><br>
- Setelah itu kita dapat memasukkan method dan url yang dituju <br><br>
![](../Screenshot_4/8.png) <br><br>
- Akses url yang baru saja ditambahkan pada aplikasi dengan methodnya <br><br>
![](../Screenshot_4/8_1.png) <br><br>
![](../Screenshot_4/8_2.png) <br><br>
![](../Screenshot_4/8_3.png) <br><br>
![](../Screenshot_4/8_4).png) <br><br>
![](../Screenshot_4/8_5.png) <br><br>
![](../Screenshot_4/8_6.png) <br><br>
3. Migrasi Database <br><br>
   -  Sebelum melakukan migrasi database pastikan server database aktif kemudian pastikan sudah membuat database dengan nama ```lumenapi```
   ![](../Screenshot_4/9.png) <br><br>
   -  Kemudian ubah konfigurasi database pada file .env menjadi seperti ini
      ```
      DB_CONNECTION=mysql
      DB_HOST=127.0.0.1
      DB_PORT=3306
      DB_DATABASE=lumenapi
      DB_USERNAME=root
      DB_PASSWORD=<<password masing-masing>>
      ```
      <br>
     ![](../Screenshot_4/10.png) <br><br>
   -  Setelah mengubah konfigurasi pada file .env, kita juga perlu menghidupkan beberapa library bawaan dari lumen dengan membuka file app.php pada folder bootstrap dan mengubah baris ini :
      ```
      //$app->withFacades();
      //$app->withEloquent();
      ```
       Menjadi : 
      ```
      $app->withFacades();
      $app->withEloquent();
      ```
     ![](../Screenshot_4/11.png) <br><br>
    - Setelah itu jalankan command berikut untuk membuat file migration :
      ```
      php artisan make:migration create_users_table # membuat migrasi untuk tabel users
      php artisan make:migration create_products_table # membuat migrasi untuk tabel products
      ```
     ![](../Screenshot_4/13.png) <br><br>
    - Ubah fungsi up pada file migrasi ```create_users_table```
      ```
      # sebelumnya
      ...
      public function up()
      {
      Schema::create('users', function (Blueprint $table) {
          $table->id();
          $table->timestamps();
      });
      }
      ...
      # diubah menjadi
      ...
      public function up()
      {
      Schema::create('users', function (Blueprint $table) {
          $table->id();
          $table->timestamps();
          $table->string('name');
          $table->string('email');
          $table->string('password');
      }); 
      }
      ...
      ```
    ![](../Screenshot_4/13.png) <br><br>
    - Ubah fungsi up pada file migrasi ```create_products_table```
      ```
      # sebelumnya
      ...
      public function up()
      {
      Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
      });
      }
      ...
      # diubah menjadi
      ...
      public function up()
      {
      Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
            $table->string('name');
            $table->integer('category_id');
            $table->string('slug');
            $table->integer('price');
            $table->integer('weight');
            $table->text('description');
            });
      }
      ...
      ```
    ![](../Screenshot_4/14.png) <br><br>
    - Kemudian jalankan command :
      ```
      php artisan migrate
      ```
    ![](../Screenshot_4/15.png) <br><br>
    ![](../Screenshot_4/16.png) <br><br>
    
