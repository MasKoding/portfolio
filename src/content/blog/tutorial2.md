---
title: 'Introduction React JS : Component'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Aug 8 2024'
heroImage: '/blog-placeholder-4.jpg'
---
## Pengenalan React
**React** adalah Pustaka JavaScript yang digunakan untuk membangun antarmuka pengguna. Pustaka ini dikembangkan oleh Facebook.

Pustaka ini menyederhanakan pembuatan aplikasi web yang dinamis dan interaktif dengan bantuan arsitektur berbasis komponen, dapat digunakan kembali, dan rendering yang efisien dengan **JSX** dan **virtualDOM**

## Fitur React
1. JSX (Ekstensi Sintaks JavaScript) :

JSX menggabungkan HTML dan JavaScript, yang memungkinkan Anda untuk menanamkan objek JavaScript dalam elemen HTML.
Ini meningkatkan keterbacaan kode dan menyederhanakan pengembangan UI.

```javascript
 const name = "Steve"
 const element = "<h1>Welcome {name}</h1>"
```

variable name akan di render dalam syntax JSX menjadi
output:
``Welcome Steve``

2. Virtual DOM
 React menggunakan DOM virtual, yang merupakan salinan persis dari DOM asli.
Ketika terjadi modifikasi pada aplikasi web, React memperbarui DOM virtual terlebih dahulu dan kemudian menghitung perbedaan antara DOM nyata dan DOM virtual.

![Alt text](Browser-DOM-Virtual-DOM-React.png)



## Your First Component
Components are one of the core concepts of React. They are the foundation upon which you build user interfaces (UI), which makes them the perfect place to start your React journey!

**Defining a component**
a React component is a JavaScript function that you can sprinkle with markup. Here’s what that looks like (you can edit the example below):

```javascript
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3Am.jpg"
      alt="Katherine Johnson"
    />
  )
}

```

output:
 <img
      src="https://i.imgur.com/MK3eW3Am.jpg"
      alt="Katherine Johnson"
    />

**Step 1: Export the component** 
The export default prefix is a standard JavaScript syntax (not specific to React). It lets you mark the main function in a file so that you can later import it from other files
Example:
``Component in Profile.js export to File Galery.js``

**Step 2: Define the function** 
With function Profile() { } you define a JavaScript function with the name Profile.
```
    function Profile(){
        return (<></>)
    }
    function Galeri(){
        return (<></>)
    }

```

**Step 3: Add markup**
The component returns an ``<img />`` tag with src and alt attributes. ``<img />`` is written like HTML, but it is actually JavaScript under the hood! This syntax is called JSX, and it lets you embed markup inside JavaScript.
```javascript
return <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />;
```
inside paranthesis if element or markup more than one line
```javascript
return (<>
    <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />
    </>);
```

## Practicing
How to Using a component
Now that you’ve defined your Profile component, you can nest it inside other components. For example, you can export a Gallery component that uses multiple Profile components:

```javascript
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}

```
Output:
Amazing Scientis
<div>
<img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
</div>


