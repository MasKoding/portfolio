---
title: 'Introduction React JS : Practice Component'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Aug 8 2024'
heroImage: '/blog-placeholder-4.jpg'
---

**Latihan Component dan State React**

## Latihan 1
Pada latihan1 kita akan membuat beberapa component untuk menampilkan image,mengatur event click dan mempelajari state pada React Hooks.

Berikut syntax yang akan kita buat:
```javascript
import { useState } from 'react'
import './App.css'
import ProductList from './ProductList'

function App() {
  


  return (
    <>
    <section>
      
    <Header/>
      <Profile/>
      <Profile/>
      <Profile/>
      <CountImage/> 
    </section>
      
    </>
  )
}


function Profile(){
  return (
    <>
      <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
    </>
  )
}

function Header(){
  const name = "Steve"
  return (<>
      <h1>Welcome {name}</h1>
  </>)

}

function CountImage()
{

  const [count,setCount] = useState(0)
  const handleClick =()=>setCount(count+1)
  return (
    <>
     <button onClick={handleClick}>Click</button>
      <p>Total Images : {count}</p>
    </>
  )
}
export default App
```

Penjelasan:
-  Fungsi Komponen App():
``App()`` adalah komponen utama dalam aplikasi ini. Ini bertugas untuk mengatur layout dan menampilkan komponen lain seperti ``Header, Profile, dan CountImage``.
Komponen ini mengembalikan elemen-elemen JSX yang akan dirender ke dalam DOM.
- Fungsi Komponen Profile():
Komponen ini bertanggung jawab untuk menampilkan gambar Katherine Johnson.
- Fungsi Komponen Header():
Komponen ini menampilkan judul dengan nama yang disisipkan menggunakan template literal dalam JSX.
- const name = "Steve": Variabel name menyimpan string "Steve", dan digunakan untuk mempersonalisasi teks di dalam tag ```<h1>```.
Pada output, akan ditampilkan "Welcome Steve" di halaman.
- ``const [count, setCount] = useState(0):``.Ini adalah penggunaan hook useState. count adalah variabel state yang menyimpan nilai awal (0), dan setCount adalah fungsi untuk memperbarui nilai count.
Setiap kali tombol diklik, count akan bertambah 1.
``handleClick:``

Fungsi ini digunakan untuk menangani event klik. Saat tombol diklik, fungsi handleClick dipanggil, yang akan memperbarui count dengan nilai baru (count + 1).

## Latihan 2

Kita mengimpor React dan useState dari pustaka react. 
**useState** adalah hook yang digunakan untuk mendeklarasikan state lokal dalam komponen fungsional.

State untuk Daftar Produk:

- products adalah state yang menyimpan daftar produk. Pada awalnya, daftar ini kosong ([]).
- productName adalah state untuk menyimpan input dari nama produk.
- productPrice adalah state untuk menyimpan input dari harga produk.

```javascript
const [products, setProducts] = useState([]);
const [productName, setProductName] = useState('');
const [productPrice, setProductPrice] = useState('');
```

Fungsi addProduct:

Fungsi ini digunakan untuk menambahkan produk baru ke dalam daftar.
Pertama, kita memeriksa apakah productName atau productPrice kosong. Jika iya, fungsi akan berhenti (return) dan tidak menambahkan produk.

Jika tidak kosong, produk akan ditambahkan ke products dengan menyalin produk yang ada sebelumnya (menggunakan spread operator ...) dan menambahkan objek baru ``{ name: productName, price: parseFloat(productPrice) }``.
Setelah produk ditambahkan, input form untuk nama dan harga akan di-reset menjadi kosong.

```javascript
const addProduct = () => {
  if (productName.trim() === '' || productPrice.trim() === '') return;
  setProducts([...products, { name: productName, price: parseFloat(productPrice) }]);
  setProductName('');
  setProductPrice('');
};
```

Fungsi removeProduct:

Fungsi ini digunakan untuk menghapus produk dari daftar berdasarkan indeks produk yang dihapus.

setProducts akan mem-filter produk yang tidak dihapus dengan menggunakan metode filter. Indeks produk yang dihapus tidak akan dimasukkan kembali ke dalam daftar.

```javascript
const removeProduct = (indexToRemove) => {
  setProducts(products.filter((_, index) => index !== indexToRemove));
};
```

Render Komponen:

Bagian ini menangani bagaimana elemen-elemen HTML akan ditampilkan:
Input untuk memasukkan nama produk dan harga produk.
Tombol untuk menambahkan produk ke dalam daftar.
Daftar produk yang akan ditampilkan menggunakan elemen <ul> dan di-loop dengan map untuk menampilkan setiap produk. Di setiap produk, ada tombol "Remove" yang dapat menghapus produk dari daftar.

```javascript
return (
  <div>
    <h2>Daftar Produk</h2>

    <div>
      <input
        type="text"
        value={productName}
        onChange={(e) => setProductName(e.target.value)}
        placeholder="Masukkan nama produk"
      />
      <input
        type="number"
        value={productPrice}
        onChange={(e) => setProductPrice(e.target.value)}
        placeholder="Masukkan harga produk"
      />
      <button onClick={addProduct}>Tambah Produk</button>
    </div>

    <ul>
      {products.map((product, index) => (
        <li key={index}>
          {product.name} - Rp{product.price.toFixed(2)}
          <button onClick={() => removeProduct(index)}>Hapus</button>
        </li>
      ))}
    </ul>
  </div>
);
```




