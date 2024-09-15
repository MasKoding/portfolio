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
