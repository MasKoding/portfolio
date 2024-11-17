---
title: 'Inertia'
description: 'Inertia '
pubDate: 'Jul 22 2022'
heroImage: '/blog-placeholder-2.jpg'
---
## Inertia Js

Inertia merupakan suatu teknologi  untuk membangun single-page apps secara modern tanpa menggunakan API

Analogi :

Inertia js untuk merupakan Adapter yang menghubungkan sisi Client dan sisi Server.

Bahasa /Adapter yang di dukung dari sisi client yaitu
- React js
- Vue js
- Svelte js

Framework yang digunakan dari sisi server yaitu
- Semua back end framework (**Rails,phoenix dll**)
- Rekomendasi nya untuk fine-tuned gunakan **Laravel**


How it works


Instalasi:
1. Server-Side
2. Client-Side

**Server Side Setup**
Untuk memulai instalasi Inertia pada framework laravel , maka kita perlu membuat project laravel terlebih dahulu.

Jika sudah membuat project laravel maka  kita dapat melakukan installasi dependencies yaitu Inertia server-side adapter pada project laravel kita dengan perintah **Composer** seperti dibawah ini:

```
composer require inertiajs/inertia-laravel
```
**Konfigurasi Root Template**
Step selanjutnya, kita harus konfigurasi root template . Root template yaitu halaman awal ketika aplikasi / website kita di load.  Di bagian ini kita juga perlu menambahkan assets css dan javascript serta tag ``<div>`` utama untuk menjalankan aplikasi javascript. 

Untuk code berikut di masukkan pada halaman default web laravel ketika dijalankan. Misalnya secara default root template ini perlu kita tambahkan di ``app.blade.php``

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    @viteReactRefresh
    @vite('resources/js/app.jsx')
    @inertiaHead
  </head>
  <body>
    @inertia
  </body>
</html>
```
Keterangan :
- **@viteReactRefresh** yaitu fungsinya agar hot reload dijalankan atau tida perlu refresh halaman ketika ada perubahan
- **@inertia** digunakan untuk me-<i>render</i> setiap <i>component</i> yang menggunakan <i>template</i> ini.


**Optional**: Bisa mengganti root view dengan menggunakan fungsi  ``Inertia::setRootView()``


**Middleware**
Selanjutnya kita perlu membuat middleware untuk Inertia. Untuk membuat middleware lakukan perintah artisan seperti dibawah ini :
```
php artisan inertia:middleware
```

Daftarkan middleware yang sudah dibuat  pada middleware group aplikasi kamu  yaitu ada di file ``bootstrap/app.php``

```php
use App\Http\Middleware\HandleInertiaRequests;

->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        HandleInertiaRequests::class,
    ]);
})
```

**Konfigurasi Client Side**
Setelah berhasil untuk install Inertia secara server side maa langkah selanjutnya yaitu kita harus setup Inertia secara client side. 

Untuk adapter yang di support oleh Inertia ada 3 yaitu React,View dan Svelte.

**Install dependencies**
Pilih adapter yang digunakan misalnya React. Maka selanjutnya lakukan perintah npm seperti dibawah:

```bash
npm install
npm install @inertiajs/inertia@0.11.0
npm install @inertiajs/inertia-react@0.8.1
npm install @inertiajs/progress@0.2.7
npm install react@17.0.2 react-dom@17.0.2
```

**Inisialisasi Inertia App**
Buka file jsx yang sudah dipanggil pada root template dan lakukan inisialisasi pada inertia app seperti dibawah ini :
```javascript
import React from 'react';
import { render } from 'react-dom';
import { createInertiaApp } from '@inertiajs/inertia-react';
import { InertiaProgress } from '@inertiajs/progress';
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers';

createInertiaApp({
    resolve: (name) => resolvePageComponent(`./Pages/${name}.jsx`, import.meta.glob('./Pages/**/*.jsx')),
    setup({ el, App, props }) {
        return render(<App {...props} />, el);
    },
});

InertiaProgress.init();

```

**Konfigurasi Vite**
Sekarang , silahkan buka file vite.config.js , kemudian ubah code dibawah seperti berikut ini.
```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel(['resources/js/app.jsx']),
    ],
    resolve:{
        alias:{
            '@':'/resources/js'
        }
    }
});

```

**Jalankan Vite**
Untuk melakukan <i>compiling assets</i> . Jalankan vite dengan perintah pada terminal /CMD dibawah:
```
npm run dev
``` 


**Membuat Migration Data Product**
Buatlah perintah migration file untuk tabel product seperti dibawah ini .
```bash
php artisan make:migration create_products_table
```

Untuk tabel product dibuat seperti dibawah ini.
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
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->integer('price');
            $table->string('category');
            $table->integer('qty');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};

```


Buatlah table Orders dengan kode program seperti dibawah ini.
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
        Schema::create('orders', function (Blueprint $table) {
            $table->id();
            $table->foreignId('product_id')->constrained()->onDelete('cascade');
            $table->enum("status",["pending","cancel","processing","completed"])->default("pending");
            $table->string("user");
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('orders');
    }
};

```

Lakukan perintah migrate
```
php artisan migrate
```
**Membuat Model**
Langkah selanjutnya , kita buat model untuk tabel Products dan Orders seperti kode program dibawah.

```
php artisan make:model Products
```

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Products extends Model
{
    //
    protected $fillable = [
        'name',
        'price',
        'category',
        'qty'
    ];
}


```

```
php artisan make:model Orders
```

```php

<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Orders extends Model
{
    //
    protected $field =['product_id','status','user'];
}

```


Buatlah controller untuk menampilkan data product dengan perintah seperti dibawah ini .
```bash
php artisan make:controller ProductController
```

**Konfigurasi Routing**
Buka file routes/web.php dan rubah kode program seperti dibawah ini.

```php
<?php

use App\Http\Controllers\BlogPostController;
use App\Http\Controllers\ProductController;
use App\Http\Controllers\ProfileController;
use Illuminate\Support\Facades\Route;


Route::get("/blog/index",[BlogPostController::class,'index'])
->name('blog.index');

Route::post("/blog/store",[BlogPostController::class,'store'])
->name('blog.store');

Route::get("/blog/search/{title}",[BlogPostController::class,'search'])
->name('blog.search');

Route::get('/', function () {
    return redirect(route('blog.index'));
});
// Gunakan resource untuk membuat semua endpoint {GET,POST,PUT,PATCH,DELETE}
Route::resource("products",ProductController::class);


require __DIR__.'/auth.php';

```

**Konfigurasi Layout**
Langkah selanjutnya buat folder **Layouts** di **resources/js**. Kemudian buat file **Default.jsx**.

Jika sudah maka isi kode program seperti dibawah ini.
```javascript

//import React
import React from 'react';

//import Link
import { Link } from '@inertiajs/inertia-react';

function Layout({ children }) {

    return (
        <>
            <header>
                <nav className="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
                    <div className="container">
                        <Link className="navbar-brand" href="/">Shopping Yuk</Link>
                        <button className="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
                            <span className="navbar-toggler-icon"></span>
                        </button>
                        <div className="collapse navbar-collapse" id="navbarCollapse">
                            <ul className="navbar-nav me-auto mb-2 mb-md-0">
                                <li className="nav-item">
                                    <Link className="nav-link" href="/products/">Products</Link>
                                </li>

                            </ul>
                            <form className="d-flex">
                                <input className="form-control me-2" type="search" placeholder="Search" aria-label="Search"/>
                                <button className="btn btn-success" type="submit">Search</button>
                            </form>
                        </div>
                    </div>
                </nav>
            </header>

            <main className="container mt-5">
                { children }
            </main>
        </>
    )

}

export default Layout

```

**Product Controller**
Buka file ProductController lalu kita isi untuk semua method atau function nya agar kita dapat melakukan manipulasi data product.

Berikut syntax nya:
- **Menampilkan Data Products**
```php
   public function index(){
        $products = Products::latest()->get();

        return Inertia::render("Products/Index",[
            'products'=>$products
        ]);
    }
```
- **Membuka Halaman Create**
```php
   public function create(){
        return inertia("Products/Create");
    }
```

**Melakukan Insert Data Products**

```php
 public function store(Request $request){
        $request->validate([
            'name'=>'required',
            'price'=>'required',
            'category'=>'required',
            'qty'=>'required',


        ]);

        Products::create([
            'name'=>$request->name,
            'price'=>$request->price,
            'category'=>$request->category,
            'qty'=>$request->qty,
        ]);

        return redirect()->route("products.index")->with('success','Data berhasil di simpan!');


    }
```
**Membuka Halaman Edit**
```php
 public function edit(Products $product){
        return inertia("Products/Edit",[
            "product"=>$product
        ]);

    }
```
**Menjalankan Fungsi Update**
```php
 public function update(Request $request,Products $products){
        $request->validate([
            'name'=>'required',
            'price'=>'required',
            'category'=>'required',
            'qty'=>'required',


        ]);

        $products->update([
            'name'=>$request->name,
            'price'=>$request->price,
            'category'=>$request->category,
            'qty'=>$request->qty,
        ]);

        return redirect()->route("products.index")->with('success','Data berhasil di update!');

    }
```
**Menjalankan Fungsi Delete**
```php
public function destroy(Products $product){
        $product->delete();

        return redirect()->route("products.index")->with('success','Data berhasil di delete!');

    }
```

**Client Side**
- Component Index
```javascript
//import React
import React from 'react';

//import layout
import Layout from '../../Layouts/Default';

//import Link
import { Link } from '@inertiajs/react';
import { Inertia } from '@inertiajs/inertia';

export default function ProductsIndex({ products, session }) {

    const deleteProduct = async (id) => {
        Inertia.delete(`/products/${id}`);
    }
  return (
    <Layout>
        <div style={{ marginTop: '100px' }}>

            <Link href="/products/create" className="btn btn-success btn-md mb-3">Add Product</Link>

            {session.success && (
                <div class="alert alert-success alert-dismissible fade show" role="alert">
                <strong>Success!</strong> Your operation was completed successfully.
                <button
                  type="button"
                  class="btn-close"
                  data-bs-dismiss="alert"
                  aria-label="Close"
                ></button>
              </div>

            )}

            <div className="card border-0 rounded shadow-sm">
                <div className="card-body">
                    <table className="table table-bordered table-striped">
                        <thead>
                            <tr>
                                <th scope="col">Name</th>
                                <th scope="col">Price</th>
                                <th scope="col">Category</th>
                                <th scope="col">Qty</th>
                                <th scope="col">ACTIONS</th>
                            </tr>
                        </thead>
                        <tbody>
                        { products.map((product, index) => (
                            <tr key={ index }>
                                <td>{ product.name }</td>
                                <td>{ product.price }</td>
                                <td>{ product.category }</td>
                                <td>{ product.qty }</td>
                                <td className="text-center">
                                <Link href={`/products/${product.id}/edit`} className="btn btn-sm btn-primary me-2">EDIT</Link>
                                <button onClick={()=>deleteProduct(product.id)} className='btn btn-danger'>Delete</button>
                                </td>
                            </tr>
                        )) }
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </Layout>
  )
}

```

- Component Edit
```javascript
import React, { useState }  from "react";
import Layout from "../../Layouts/Default";
import { Inertia } from "@inertiajs/inertia";


export default function Edit({errors,product}){
    const [name,setName] = useState(product.name)
    const [category,setCategory] = useState(product.category)
    const [price,setPrice] = useState(product.price)
    const [qty,setQty] = useState(product.qty)
    const [total,setTotal]  =useState(product.price*product.qty)

    const data = {
        'name':name,
        'category':category,
        'price':price,
        'qty':qty,

    };
    const updateProduct = async(e)=>{
        e.preventDefault()
        Inertia.put(`/products/${product.id}`,data)

    }
    const handleQty = (e)=>{
        const newQty = e.target.value;
        setQty(newQty)
        setTotal(newQty*price)

    }
    const handlePrice = (e)=>{
        const newPrice = e.target.value
        setPrice(newPrice)
        setTotal(qty*newPrice)

    }
    return (
        <Layout>
            <div className="row" style={{ marginTop: '100px' }}>
                <div className="col-12">
                    <div className="card border-0 rounded shadow-sm border-top-success">
                        <div className="card-header">
                            <span className="font-weight-bold">TAMBAH PRODUCTS</span>
                        </div>
                        <div className="card-body">
                            <form onSubmit={updateProduct}>

                                <div className="mb-3">
                                    <label className="form-label fw-bold">Name</label>
                                    <input type="text" className="form-control" value={name} onChange={(e) => setName(e.target.value)} placeholder="Masukkan Product Name" />
                                </div>
                                {errors.name && (
                                    <div className="alert alert-danger">
                                        {errors.name}
                                    </div>
                                )}

<div className="mb-3">
                                    <label className="form-label fw-bold">Category</label>
                                    <input type="text" className="form-control" value={category} data-role="tags-input" onChange={(e) => setCategory(e.target.value)} placeholder="Masukkan Category " />
                                </div>
                                {errors.category && (
                                    <div className="alert alert-danger">
                                        {errors.category}
                                    </div>
                                )}
                                <div className="mb-3">
                                    <label className="form-label fw-bold">Price</label>
                                    <input type="text" className="form-control" value={price} onChange={handlePrice} placeholder="Masukkan Price " />
                                </div>
                                {errors.price && (
                                    <div className="alert alert-danger">
                                        {errors.price}
                                    </div>
                                )}



                                <div className="mb-3">
                                    <label className="form-label fw-bold">Quantity</label>
                                    <input type="number" className="form-control" value={qty} onChange={handleQty} placeholder="Masukkan Quantity " />
                                </div>
                                {errors.qty && (
                                    <div className="alert alert-danger">
                                        {errors.qty}
                                    </div>
                                )}
                                <div className="mb-3">
                                    <label className="form-label fw-bold">Total</label>
                                    <input type="number" disabled={true} value={total}  className="form-control"  placeholder="Total " />
                                </div>
                                <div>
                                </div>


                                <div>
                                    <button type="submit" className="btn btn-md btn-success me-2"><i className="fa fa-save"></i> SAVE</button>
                                    <button type="reset" className="btn btn-md btn-warning"><i className="fa fa-redo"></i> RESET</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </Layout>
    )
}



```
