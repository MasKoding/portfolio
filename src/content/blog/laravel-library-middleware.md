---
title: 'Laravel Middleware'
description: 't'
pubDate: '1 Desember 2024'
heroImage: '/blog-placeholder-4.jpg'
---
Berikut adalah langkah-langkah untuk membuat aplikasi Library Management menggunakan Laravel 11 dengan fitur role anggota dan admin, middleware role-based access control (RBAC), modul authentication, serta data dummy user menggunakan seeder:

1. Persiapan Project
Buat Laravel Project:

```bash
Copy code
laravel new library-management
cd library-management
Instalasi Laravel Breeze (untuk Authentication):
```
```
bash
Copy code
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
```
Tambahkan Library untuk RBAC (Spatie Laravel Permission):

```
bash
Copy code
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```
2. Setup Role dan Permission
Konfigurasi User Model: Tambahkan trait HasRoles pada model User di app/Models/User.php:
```
php
Copy code
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasRoles;
    // ...
}
```
Buat Seeder untuk Role dan User Dummy: Buat file seeder:
```
bash
Copy code
php artisan make:seeder RoleAndUserSeeder
```
Isi file database/seeders/RoleAndUserSeeder.php:
```
php
Copy code
use App\Models\User;
use Spatie\Permission\Models\Role;
use Illuminate\Database\Seeder;

class RoleAndUserSeeder extends Seeder
{
    public function run()
    {
        // Buat Roles
        $adminRole = Role::create(['name' => 'admin']);
        $anggotaRole = Role::create(['name' => 'anggota']);

        // Buat User Admin
        $admin = User::create([
            'name' => 'Admin User',
            'email' => 'admin@example.com',
            'password' => bcrypt('password'),
        ]);
        $admin->assignRole($adminRole);

        // Buat User Anggota
        $anggota = User::create([
            'name' => 'Anggota User',
            'email' => 'anggota@example.com',
            'password' => bcrypt('password'),
        ]);
        $anggota->assignRole($anggotaRole);
    }
}
```
Jalankan seeder:

```
bash
Copy code
php artisan db:seed --class=RoleAndUserSeeder
```
3. Middleware untuk Role
Buat Middleware:
```
bash
Copy code
php artisan make:middleware RoleMiddleware
```
Isi Middleware di app/Http/Middleware/RoleMiddleware.php:
```
php
Copy code
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class RoleMiddleware
{
    public function handle(Request $request, Closure $next, $role)
    {
        if (!auth()->check() || !$request->user()->hasRole($role)) {
            abort(403, 'Unauthorized.');
        }
        return $next($request);
    }
}
```
Daftarkan Middleware: Tambahkan di app/Http/Kernel.php:
```
php
Copy code
protected $routeMiddleware = [
    // ...
    'role' => \App\Http\Middleware\RoleMiddleware::class,
];
```
4. Model dan Migration
Buat Model dan Migration untuk Buku dan Peminjaman:
```
bash
Copy code
php artisan make:model Book -m
php artisan make:model Loan -m
```
Isi Migration Buku: Di database/migrations/*_create_books_table.php:
```
php
Copy code
public function up()
{
    Schema::create('books', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->string('author');
        $table->integer('quantity');
        $table->timestamps();
    });
}
```
Isi Migration Peminjaman: Di database/migrations/*_create_loans_table.php:
```
php
Copy code
public function up()
{
    Schema::create('loans', function (Blueprint $table) {
        $table->id();
        $table->foreignId('book_id')->constrained()->onDelete('cascade');
        $table->foreignId('user_id')->constrained()->onDelete('cascade');
        $table->date('borrowed_at');
        $table->date('returned_at')->nullable();
        $table->timestamps();
    });
}
```
Jalankan Migration:
```
bash
Copy code
php artisan migrate
```
5. Routing dan Controller
Buat Controller untuk Buku dan Peminjaman:
```
bash
Copy code
php artisan make:controller BookController --resource
php artisan make:controller LoanController --resource
```
Definisikan Routing di routes/web.php:
```
php
Copy code
use App\Http\Controllers\BookController;
use App\Http\Controllers\LoanController;

Route::middleware('auth')->group(function () {
    Route::middleware('role:admin')->group(function () {
        Route::resource('books', BookController::class);
    });

    Route::middleware('role:anggota')->group(function () {
        Route::resource('loans', LoanController::class);
    });
});
```
Implementasi Controller untuk CRUD Buku dan Peminjaman.

6. Data Dummy untuk Buku
Buat seeder untuk data buku:
```
bash
Copy code
php artisan make:seeder BookSeeder
```
Isi file database/seeders/BookSeeder.php:
```
php
Copy code
use App\Models\Book;
use Illuminate\Database\Seeder;

class BookSeeder extends Seeder
{
    public function run()
    {
        Book::create(['title' => 'Laravel 101', 'author' => 'John Doe', 'quantity' => 5]);
        Book::create(['title' => 'Mastering PHP', 'author' => 'Jane Smith', 'quantity' => 3]);
    }
}
```
Jalankan seeder:
```
bash
Copy code
php artisan db:seed --class=BookSeeder
```

Berikut adalah kode untuk membuat halaman front-end dengan Tailwind CSS untuk fitur anggota, daftar buku, dan proses peminjaman. Anda dapat menyalin kode berikut ke file Blade atau file HTML sesuai struktur Laravel Anda.

1. Instalasi Tailwind CSS di Laravel
Jika belum menginstal Tailwind CSS, ikuti langkah berikut:
```
bash
Copy code
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init
```
Edit file tailwind.config.js:
```javascript

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./resources/**/*.blade.php",
    "./resources/**/*.js",
    "./resources/**/*.vue",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```
Tambahkan Tailwind di file CSS resources/css/app.css:
```css

@tailwind base;
@tailwind components;
@tailwind utilities;
```
Jalankan build:

```bash
npm run dev
```

2. Halaman Anggota
File: resources/views/anggota/index.blade.php

```php
@extends('layouts.app')

@section('content')
<div class="container mx-auto px-4">
    <h1 class="text-2xl font-bold text-gray-700 mb-4">Daftar Anggota</h1>
    <table class="min-w-full bg-white border rounded-lg shadow-lg">
        <thead>
            <tr>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Nama</th>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Email</th>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Role</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($members as $member)
            <tr>
                <td class="py-2 px-4 border-t">{{ $member->name }}</td>
                <td class="py-2 px-4 border-t">{{ $member->email }}</td>
                <td class="py-2 px-4 border-t capitalize">{{ $member->roles->first()->name }}</td>
            </tr>
            @endforeach
        </tbody>
    </table>
</div>
@endsection
```
3. Halaman Buku
File: resources/views/books/index.blade.php

```php
@extends('layouts.app')

@section('content')
<div class="container mx-auto px-4">
    <h1 class="text-2xl font-bold text-gray-700 mb-4">Daftar Buku</h1>
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        @foreach ($books as $book)
        <div class="bg-white border rounded-lg shadow-lg p-4">
            <h2 class="text-lg font-semibold text-gray-800">{{ $book->title }}</h2>
            <p class="text-gray-600">Penulis: {{ $book->author }}</p>
            <p class="text-gray-600">Jumlah: {{ $book->quantity }}</p>
            <form action="{{ route('loans.store') }}" method="POST" class="mt-4">
                @csrf
                <input type="hidden" name="book_id" value="{{ $book->id }}">
                <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
                    Pinjam Buku
                </button>
            </form>
        </div>
        @endforeach
    </div>
</div>
@endsection
```
4. Halaman Peminjaman
File: resources/views/loans/index.blade.php

```php
@extends('layouts.app')

@section('content')
<div class="container mx-auto px-4">
    <h1 class="text-2xl font-bold text-gray-700 mb-4">Daftar Peminjaman</h1>
    <table class="min-w-full bg-white border rounded-lg shadow-lg">
        <thead>
            <tr>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Nama Buku</th>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Tanggal Pinjam</th>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Tanggal Kembali</th>
                <th class="py-2 px-4 bg-gray-100 text-left text-sm font-semibold text-gray-600">Aksi</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($loans as $loan)
            <tr>
                <td class="py-2 px-4 border-t">{{ $loan->book->title }}</td>
                <td class="py-2 px-4 border-t">{{ $loan->borrowed_at }}</td>
                <td class="py-2 px-4 border-t">
                    {{ $loan->returned_at ?? 'Belum Dikembalikan' }}
                </td>
                <td class="py-2 px-4 border-t">
                    @if (is_null($loan->returned_at))
                    <form action="{{ route('loans.update', $loan->id) }}" method="POST">
                        @csrf
                        @method('PUT')
                        <button type="submit" class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600">
                            Kembalikan Buku
                        </button>
                    </form>
                    @endif
                </td>
            </tr>
            @endforeach
        </tbody>
    </table>
</div>
@endsection
```
5. Layout File
File: resources/views/layouts/app.blade.php

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Library Management</title>
    @vite('resources/css/app.css')
</head>
<body class="bg-gray-100 font-sans leading-normal tracking-normal">
    <nav class="bg-blue-500 text-white px-4 py-2">
        <div class="container mx-auto flex justify-between">
            <a href="{{ route('home') }}" class="text-lg font-bold">Library Management</a>
            <div>
                <a href="{{ route('books.index') }}" class="mx-2">Buku</a>
                <a href="{{ route('loans.index') }}" class="mx-2">Peminjaman</a>
                <a href="{{ route('logout') }}" class="mx-2">Logout</a>
            </div>
        </div>
    </nav>
    <div class="py-6">
        @yield('content')
    </div>
</body>
</html>
```

Berikut adalah implementasi controller dan routing untuk fitur anggota, buku, dan peminjaman pada proyek Laravel:

1. Controller
AnggotaController
File: app/Http/Controllers/AnggotaController.php

```php
Copy code
<?php

namespace App\Http\Controllers;

use App\Models\User;

class AnggotaController extends Controller
{
    public function index()
    {
        $members = User::whereHas('roles', function ($query) {
            $query->where('name', 'anggota');
        })->get();

        return view('anggota.index', compact('members'));
    }
}
```
BookController
File: app/Http/Controllers/BookController.php

```php
Copy code
<?php

namespace App\Http\Controllers;

use App\Models\Book;
use Illuminate\Http\Request;

class BookController extends Controller
{
    public function index()
    {
        $books = Book::all();
        return view('books.index', compact('books'));
    }
}
```
LoanController
File: app/Http/Controllers/LoanController.php

```php
Copy code
<?php

namespace App\Http\Controllers;

use App\Models\Book;
use App\Models\Loan;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class LoanController extends Controller
{
    public function index()
    {
        $loans = Loan::where('user_id', Auth::id())->with('book')->get();
        return view('loans.index', compact('loans'));
    }

    public function store(Request $request)
    {
        $request->validate(['book_id' => 'required|exists:books,id']);

        $book = Book::findOrFail($request->book_id);

        // Check if the book is available
        if ($book->quantity <= 0) {
            return back()->with('error', 'Buku tidak tersedia.');
        }

        // Create loan record
        Loan::create([
            'book_id' => $book->id,
            'user_id' => Auth::id(),
            'borrowed_at' => now(),
        ]);

        // Decrease book quantity
        $book->decrement('quantity');

        return back()->with('success', 'Buku berhasil dipinjam.');
    }

    public function update($id)
    {
        $loan = Loan::findOrFail($id);

        // Mark the loan as returned
        $loan->update(['returned_at' => now()]);

        // Increase book quantity
        $loan->book->increment('quantity');

        return back()->with('success', 'Buku berhasil dikembalikan.');
    }
}
```
2. Routing
File: routes/web.php

```php
Copy code
use App\Http\Controllers\AnggotaController;
use App\Http\Controllers\BookController;
use App\Http\Controllers\LoanController;

// Middleware untuk autentikasi
Route::middleware('auth')->group(function () {
    // Route untuk anggota
    Route::get('/anggota', [AnggotaController::class, 'index'])->name('anggota.index');

    // Route untuk buku
    Route::get('/buku', [BookController::class, 'index'])->name('books.index');

    // Route untuk peminjaman
    Route::get('/peminjaman', [LoanController::class, 'index'])->name('loans.index');
    Route::post('/peminjaman', [LoanController::class, 'store'])->name('loans.store');
    Route::put('/peminjaman/{id}', [LoanController::class, 'update'])->name('loans.update');
});
```
3. Models
Book Model
File: app/Models/Book.php

```php
Copy code
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'author', 'quantity'];
}
```
Loan Model
File: app/Models/Loan.php

```php
Copy code
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Loan extends Model
{
    use HasFactory;

    protected $fillable = ['book_id', 'user_id', 'borrowed_at', 'returned_at'];

    public function book()
    {
        return $this->belongsTo(Book::class);
    }
}
```
4. Seeder untuk Data Dummy
Seeder User
File: database/seeders/UserSeeder.php

```php
Copy code
<?php

namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\Hash;

class UserSeeder extends Seeder
{
    public function run()
    {
        // Dummy admin
        User::create([
            'name' => 'Admin',
            'email' => 'admin@example.com',
            'password' => Hash::make('password'),
        ])->assignRole('admin');

        // Dummy anggota
        User::create([
            'name' => 'Anggota 1',
            'email' => 'anggota1@example.com',
            'password' => Hash::make('password'),
        ])->assignRole('anggota');

        User::create([
            'name' => 'Anggota 2',
            'email' => 'anggota2@example.com',
            'password' => Hash::make('password'),
        ])->assignRole('anggota');
    }
}
```
Seeder Book
File: database/seeders/BookSeeder.php

```php
Copy code
<?php

namespace Database\Seeders;

use App\Models\Book;
use Illuminate\Database\Seeder;

class BookSeeder extends Seeder
{
    public function run()
    {
        Book::create(['title' => 'Buku Laravel', 'author' => 'John Doe', 'quantity' => 5]);
        Book::create(['title' => 'Buku Tailwind CSS', 'author' => 'Jane Doe', 'quantity' => 3]);
        Book::create(['title' => 'Buku Vue.js', 'author' => 'Alice', 'quantity' => 7]);
    }
}
```
5. Jalankan Seeder
Jalankan perintah berikut untuk mengisi data dummy:

```bash

php artisan db:seed --class=UserSeeder
php artisan db:seed --class=BookSeeder
```

jalankan aplikasi



