---
title: 'Advance Route & Migration'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Aug 8 2024'
heroImage: '/blog-placeholder-4.jpg'
---

## Advance Route
Sebelumnya kalian sudah belajar mengenai apa itu route dan bagaimana implementasi nya secara dasar.

Namun dalam konteks aplikasi yang kompleks. Method Routes yang di didaftarkan pada laravel berdasarkan http verbnya antara lain yaitu :
```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

nah bagaimana jika kita ingin mendaftarkan route yang bisa merespon atau menerima request dari multiple verb?

Kalian bisa menggunakan <i>match</i> dan <i>any</i>
```php
    Route::match(['get', 'post'], '/', function () {
    // ...
});
 
Route::any('/', function () {
    // ...
});
```

## Redirect Routes
Jika kalian ingin membuat route yang berisi <i>redirect</i> ke halaman lain atau melempar ke halaman tujuan. Kita bisa menggunakan syntax ```Route::redirect()``` seperti contoh dibawah ini:
```php
Route::redirect('/here', '/there');

```

## Route View
Kita bisa membuat route yang langsung mengembalikan nilai berupa view. Caranya kita bisa gunakan 
```php
Route::view('/welcome', 'welcome');
```

## Route Paramater (Required *)
Terkadang pada URI terdapat segment atau parameter , parameter ini berupa Id user ,maupun text seperti title,post dll.

Kalian bisa define route parameter dengan cara dibawah:
```php
Route::get('/user/{id}', function (string $id) {
    return 'User '.$id;
});
```

## Opsional Parameter 
terkadang parameter yang terdapat pada URI kita ingin definisikan dengan null value atau tidak wajib di isi . Maka kita bisa berikan mark ? pada parameter tersebut. Contohnya seperti dibawah:
```php
        Route::get('/user/{name?}', function (?string $name = null) {
    return $name;
});
 
Route::get('/user/{name?}', function (?string $name = 'John') {
    return $name;
});
```
## Database: Migrations
Migration merupakan version control system yang digunakan untuk menyimpan file yang digunakan untuk generate schema table dalam database yang memungkinkan kita bisa sharing file nya ke team atau pengguna lain.

## Generate Migration
Kita bisa gunakan perintah ``make:migration`` <a href="https://laravel.com/docs/11.x/artisan">Artisan Command</a>
untuk generate migration file ke database. Migration file terdapat tersimpan di folder database/migrations.

Perintah command untuk membuat migration file yaitu :
```
php artisan make:migration nama_table
```

## Struktur Migration File
Pada migration file terdapat 2 fungsi yaitu <span style="color:red;">up()</span>
dan  <span style="color:red;">down()</span>

Fungsi <span style="color:red;">up()</span> berisi syntax untuk buat atau ubah table,kolom,atau index.

Sedangkan fungsi <span style="color:red;">down()</span> berisi syntax untuk drop atau kebalikan dari fungsi up yaitu drop table.

Berikut contoh isi migration file :
```php
<?php
 
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('nama_table', function (Blueprint $table) {
            // definisikan kolom
            $table->id(); //primary key
            $table->timestamps(); // berisi created_at dam updated_at
        });
    }
 
    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::drop('flights');
    }
};
```

## Studi Kasus

Kita akan belajar membuat blog menggunakan framework laravel . Lalu apa yang harus disiapkan? yuk kita lanjut ke installasi

## Installasi Project Laravel
Ikuti perintah dibawah pada terminal :
``laravel new belajar_laravel_part1``

## Setup Database di .env file
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=blogpost
DB_USERNAME=root
DB_PASSWORD=
```

## Migration File
Untuk generate file lakukan perintah artisan dibawah:
```php artisan make:migration create_articles_table  ```

Tabel artikel yang akan kita buat dengan kolom seperti dibawah:
<table>
<tr>
<th>Kolom</th>
<th>Tipe Data</th>
<th>Length</th>
<th>Keterangan</th>
</tr>
<tr>
<td>Title</td>
<td>string</td>
<td>255</td>
<td></td>
</tr>
<tr>
<td>Slug</td>
<td>string</td>
<td>255</td>
<td></td>
</tr>
<tr>
<td>Description</td>
<td>string</td>
<td>255</td>
<td></td>
</tr>
<tr>
<td>Cover Image</td>
<td>string</td>
<td>255</td>
<td></td>
</tr>
<tr>
<td>Created By</td>
<td>string</td>
<td>255</td>
<td></td>
</tr>
<tr>
<td>Updated By</td>
<td>string</td>
<td>255</td>
<td></td>
</tr>
<tr>
<td>Is Published</td>
<td>boolean</td>
<td></td>
<td></td>
</tr>
</table>

Dari table diatas kita dapat buat schema dalam bentuk migration file seperti dibawah:
```php
    <?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('articles', function (Blueprint $table) {
            $table->id();
            $table->string("title");
            $table->string("slug");
            $table->string("description");
            $table->string("cover_image");
            $table->string("created_by");
            $table->string("updated_by");
            $table->boolean("is_published");
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('articles');
    }
};

```
Untuk generate file nya  lakukan perintah migrate
```php artisan migrate ```

# Membuat Controler
Untuk membuat controller kita lakukan perintah artisan dibawah:
``php artisan make:controller BlogPostController `` 

berikut ini isi file dari BlogPostController
```php
    <?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class BlogPostController extends Controller
{
    //
    public function index(){
        $title = 'Blog Post';
        return view('blog.index',compact('title'));
    }
}

```
Penjelasan:
- view: Fungsi yang digunakan untuk menunjukkan halaman view dan data yang di passing / kirim ke halaman website kita.
- compact: Fungsi yang digunakan untuk mengirim variable menjadi array 
 ```php 
    compact($title) => ["title"=>"Membuat Blog"]
```
## Template Engine: Blade
Laravel menggunakan template engine blade . Dimana kita bisa melakukan syntax php dalam tag html.

Disini kita akan membuat folder layout yang berisi file <a style="color:red;">header.blade.php</a> yaitu:
```php
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{{ $title }}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"
        integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous">
    </script>

    <link rel="stylesheet" href="{{ asset('../css/app.css') }}">
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.8/dist/umd/popper.min.js"
        integrity="sha384-I7E8VVD/ismYTF4hNIPjVp/Zjvgyol6VFvRkX/vR+Vc4jQkC+hVqc2pM8ODewa9r" crossorigin="anonymous">
    </script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.min.js"
        integrity="sha384-0pUGZvbkm6XF6gxjEnlmuGrJXVbNuzT9qBBavbLwCsOGabYfZo0T0to5eqruptLy" crossorigin="anonymous">
    </script>

<script src="https://kit.fontawesome.com/71597189e3.js" crossorigin="anonymous"></script>
<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
  </head>

<body>
    <div class="container">
        @yield('content')
    </div>

</body>
<script type="text/javascript" src="{{ asset('js/app.js') }}"></script>

</html>

```
Penjelasan:
- @yield('content'): fungsi pada blade yang digunakan untuk menghubungkan bagian konten pada fungsi section di halaman lain
- asset : fungsi pada blade yang digunakan untuk mengakses folder public pada laravel dimana dalam folder public berisi image,css,js file

### View Blog
Buat folder blog pada view dan buat file <a style="color:red">index.blade.php </a> yang berisi main atau kontent utama blog kita.

Isi filenya yaitu:
```php
@extends('layout.header')
@section('content')
    <div class="row no-gutters d-flex justify-content-center align-items-center">
        <div class="col-md-8 mt-2">
            <div class="form-group">
                <div class="input-group flex-nowrap">
                    <span class="input-group-text" id="addon-wrapping">
                        <i class="fas fa-search"></i>
                    </span>
                    <input type="text" class="form-control" placeholder="Search post" aria-label="SearchPost" aria-describedby="addon-wrapping">
                  </div>
            </div>
        </div>

        <div class="col-md-8 mt-2">
           <div class="card card-custom">
            <div class="card card-header">
                <div class="d-flex justify-content-start">

                <img src="{{ asset("image/image1.jpg")}}" class="img-rounded"/>
                <div class="d-flex flex-column creator">
                    <h5 style="font-size: 1rem;">Clara</h5>
                    <span style="font-size: 0.8rem;">5h ago</span>
                </div>

                </div>
            </div>
            <div class="card-body ">
                <div class="card-cover-image d-flex justify-content-center">
                    <img src="https://picsum.photos/600/300" class="cover-image img-thumbnail w-100 border-none" style="border:none;"/>
                </div>
                <div class="main-description">
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quaerat qui exercitationem quos ex architecto, totam necessitatibus fugiat rem atque maiores similique ullam voluptas provident quae a eaque consectetur, impedit deleniti.
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quaerat qui exercitationem quos ex architecto, totam necessitatibus fugiat rem atque maiores similique ullam voluptas provident quae a eaque consectetur, impedit deleniti.

                </div>

            </div>
            <div class="card-footer">
                <div class="d-flex justify-content-center align-items-center">
                    <button class="btn btn-like">
                        <i class="fa-regular fa-heart"></i>  10.5k
                    </button>
                    <button class="btn btn-like">
                        <i class="fa-regular fa-comment"></i>  15k
                    </button>
                    <button class="btn btn-like">
                        <i class="fa-regular fa-eye"></i>  50k
                    </button>
                </div>

            </div>
            <hr class="w-100" style="width:80%;border-top:1px solid #ccc;margin-left:-3px;"/>
            <div class="comment-section">
                <h5 class="ms-3 mb-3">Comments</h5>
                <div class="card-comment-custom">

                    <div class="d-flex justify-content-between w-100 align-items-center">
                    <div class="d-flex justify-content-start ms-3">
                        <img src="https://picsum.photos/id/64/100/100" class="img-rounded"/>
                        <div class="d-flex flex-column creator ml-2">
                            <h5 style="font-size: 1rem;">Kellyn</h5>
                            <span style="font-size: 0.8rem;">1m ago</span>
                            <div class="d-block">
                                <p> Nice Articles. Love it</p>
                            </div>
                        </div>

                        </div>
                        <span class="fas fa-heart me-3 text-danger"></span>
                    </div>
                </div>

            </div>
           </div>
        </div>
    </div>
    </div>

@endsection

```

