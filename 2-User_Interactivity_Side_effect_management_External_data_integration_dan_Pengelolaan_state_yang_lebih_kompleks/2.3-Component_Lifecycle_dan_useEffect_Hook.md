# BAB 2.3: Component Lifecycle dan useEffect Hook

## Tujuan Pembelajaran:

*   Memahami fase-fase *Component Lifecycle*.
*   Memahami pengenalan `useEffect` Hook.
*   Mampu mengelola *side effects* dalam *functional components*.
*   Memahami konsep *Cleanup functions*.
*   Memahami penggunaan *Dependency Arrays* dalam `useEffect`.
*   Mampu menggunakan beberapa `useEffect` Hooks dalam satu komponen.

## Materi Pembelajaran:

### 1. Component Lifecycle Phases

Setiap komponen React memiliki siklus hidup (lifecycle) yang terdiri dari beberapa fase. Meskipun `useEffect` menyatukan banyak konsep *lifecycle* dari *class components*, memahami fase-fase ini tetap penting untuk memahami kapan dan bagaimana komponen Anda di-*render* dan berinteraksi dengan DOM.

Secara umum, ada tiga fase utama dalam *lifecycle* komponen:

*   **Mounting (Pemasangan)**:
    *   Fase di mana komponen pertama kali dibuat dan dimasukkan ke dalam DOM.
    *   Ini adalah saat komponen pertama kali muncul di layar.
    *   Contoh aktivitas: *initial rendering*, *fetching* data awal, mengatur *event listeners*.

*   **Updating (Pembaruan)**:
    *   Fase di mana komponen di-*re-render* karena perubahan pada *props* atau *state*-nya.
    *   Ini terjadi setiap kali data yang relevan dengan komponen berubah.
    *   Contoh aktivitas: memperbarui UI berdasarkan data baru, melakukan *side effects* sebagai respons terhadap perubahan *props/state*.

*   **Unmounting (Pelepasan)**:
    *   Fase di mana komponen dihapus dari DOM.
    *   Ini terjadi ketika komponen tidak lagi diperlukan atau ketika navigasi ke halaman lain.
    *   Contoh aktivitas: membersihkan *event listeners*, membatalkan *network requests*, membersihkan *timers*.

### 2. useEffect Hook Introduction

`useEffect` Hook adalah cara React untuk memungkinkan *functional components* melakukan *side effects*. *Side effects* adalah operasi yang terjadi di luar alur *rendering* utama komponen, seperti:
*   *Fetching* data dari API.
*   Memanipulasi DOM secara langsung.
*   Mengatur atau membersihkan *event listeners*.
*   Mengatur *timers* (misalnya `setTimeout`, `setInterval`).
*   Berinteraksi dengan *browser API* (misalnya `localStorage`).

`useEffect` menerima dua argumen:
1.  **Fungsi *effect***: Fungsi yang akan dijalankan setelah setiap *render* (atau setelah *render* pertama dan setiap kali dependensi berubah).
2.  **Array dependensi (opsional)**: Array nilai yang akan dipantau oleh React. Jika salah satu nilai dalam *array* ini berubah antara *render*, fungsi *effect* akan dijalankan kembali.

**Sintaks Dasar:**

```jsx
import React, { useEffect } from 'react';

function MyComponent() {
  useEffect(() => {
    // Kode di sini akan dijalankan setelah setiap render komponen
    console.log('Komponen di-render atau di-update!');
  }); // Tanpa array dependensi, akan berjalan setelah setiap render

  return (
    <div>
      <h1>Contoh useEffect</h1>
      <p>Lihat konsol browser Anda.</p>
    </div>
  );
}

export default MyComponent;
```

### 3. Effect Dependencies Array

*Dependency array* adalah argumen kedua dari `useEffect` yang sangat penting untuk mengontrol kapan fungsi *effect* dijalankan.

*   **Tidak ada *dependency array***:
    *   Fungsi *effect* akan dijalankan setelah setiap *render* komponen.
    *   Ini jarang digunakan karena dapat menyebabkan *loop* tak terbatas atau masalah performa.

*   **Array kosong (`[]`)**:
    *   Fungsi *effect* hanya akan dijalankan sekali, setelah *render* pertama (mirip dengan `componentDidMount` di *class components*).
    *   Berguna untuk *fetching* data awal, mengatur *event listeners* global, atau inisialisasi yang hanya perlu dilakukan sekali.

    ```jsx
    import React, { useState, useEffect } from 'react';

    function Timer() {
      const [count, setCount] = useState(0);

      useEffect(() => {
        console.log('Timer dimulai!');
        const intervalId = setInterval(() => {
          setCount(prevCount => prevCount + 1);
        }, 1000);

        // Fungsi cleanup akan dijalankan saat komponen di-unmount
        return () => {
          clearInterval(intervalId);
          console.log('Timer dibersihkan!');
        };
      }, []); // Array kosong: hanya berjalan sekali saat mounting

      return (
        <div>
          <h2>Timer: {count} detik</h2>
        </div>
      );
    }

    export default Timer;
    ```

*   **Array dengan dependensi (`[dep1, dep2, ...]`)**:
    *   Fungsi *effect* akan dijalankan setelah *render* pertama, dan setiap kali salah satu nilai dalam *dependency array* berubah.
    *   Ini adalah skenario penggunaan yang paling umum.

    ```jsx
    import React, { useState, useEffect } from 'react';

    function UserProfile({ userId }) {
      const [user, setUser] = useState(null);
      const [loading, setLoading] = useState(true);

      useEffect(() => {
        setLoading(true);
        console.log(`Fetching user data for userId: ${userId}`);
        // Simulasi fetching data dari API
        fetch(`https://jsonplaceholder.typicode.com/users/${userId}`)
          .then(response => response.json())
          .then(data => {
            setUser(data);
            setLoading(false);
          })
          .catch(error => {
            console.error('Error fetching user:', error);
            setLoading(false);
          });
      }, [userId]); // Effect akan dijalankan ulang setiap kali userId berubah

      if (loading) return <p>Memuat data pengguna...</p>;
      if (!user) return <p>Pengguna tidak ditemukan.</p>;

      return (
        <div>
          <h2>Profil Pengguna</h2>
          <p>Nama: {user.name}</p>
          <p>Email: {user.email}</p>
        </div>
      );
    }

    function App() {
      const [currentUserId, setCurrentUserId] = useState(1);

      return (
        <div>
          <h1>Aplikasi Profil Pengguna</h1>
          <UserProfile userId={currentUserId} />
          <button onClick={() => setCurrentUserId(prevId => prevId + 1)}>
            Lihat Pengguna Berikutnya
          </button>
        </div>
      );
    }

    export default App;
    ```

### 4. Cleanup Functions

Beberapa *side effects* memerlukan pembersihan (cleanup) setelah komponen di-*unmount* atau sebelum *effect* dijalankan ulang. Contohnya termasuk:
*   Menghapus *event listeners*.
*   Membatalkan *subscriptions*.
*   Membersihkan *timers*.

Anda dapat mengembalikan fungsi dari `useEffect` yang akan bertindak sebagai fungsi *cleanup*. Fungsi ini akan dijalankan:
1.  Sebelum *effect* dijalankan ulang (jika dependensi berubah).
2.  Saat komponen di-*unmount*.

**Contoh Cleanup Function:**

```jsx
import React, { useState, useEffect } from 'react';

function WindowSizeTracker() {
  const [width, setWidth] = useState(window.innerWidth);
  const [height, setHeight] = useState(window.innerHeight);

  useEffect(() => {
    const handleResize = () => {
      setWidth(window.innerWidth);
      setHeight(window.innerHeight);
    };

    // Menambahkan event listener saat komponen mounting
    window.addEventListener('resize', handleResize);
    console.log('Event listener "resize" ditambahkan.');

    // Fungsi cleanup: akan dijalankan saat komponen unmounting
    // atau sebelum effect dijalankan ulang (jika ada dependensi)
    return () => {
      window.removeEventListener('resize', handleResize);
      console.log('Event listener "resize" dihapus.');
    };
  }, []); // Array kosong: effect hanya berjalan sekali saat mounting

  return (
    <div>
      <h2>Ukuran Jendela Browser</h2>
      <p>Lebar: {width}px</p>
      <p>Tinggi: {height}px</p>
    </div>
  );
}

function App() {
  const [showTracker, setShowTracker] = useState(true);

  return (
    <div>
      <h1>Aplikasi Pelacak Ukuran Jendela</h1>
      <button onClick={() => setShowTracker(prev => !prev)}>
        {showTracker ? 'Sembunyikan' : 'Tampilkan'} Pelacak
      </button>
      {showTracker && <WindowSizeTracker />}
    </div>
  );
}

export default App;
```

### 5. Multiple useEffect Hooks

Anda dapat menggunakan beberapa `useEffect` Hooks dalam satu komponen. Ini adalah praktik yang baik untuk memisahkan *concerns* (kekhawatiran) yang berbeda. Misalnya, satu `useEffect` untuk *fetching* data, satu lagi untuk mengatur *event listener*, dan satu lagi untuk *logging*.

```jsx
import React, { useState, useEffect } from 'react';

function MultiEffectComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  // Effect 1: Berjalan setiap kali count berubah
  useEffect(() => {
    console.log(`Count telah berubah menjadi: ${count}`);
    document.title = `Count: ${count}`;
  }, [count]);

  // Effect 2: Berjalan setiap kali name berubah
  useEffect(() => {
    console.log(`Nama telah berubah menjadi: ${name}`);
  }, [name]);

  // Effect 3: Berjalan hanya sekali saat mounting
  useEffect(() => {
    console.log('Komponen ini pertama kali dimuat!');
    // Contoh: Mengatur event listener global yang hanya perlu sekali
    const handleKeyDown = (e) => {
      console.log(`Tombol ${e.key} ditekan.`);
    };
    window.addEventListener('keydown', handleKeyDown);

    return () => {
      window.removeEventListener('keydown', handleKeyDown);
      console.log('Event listener keydown dihapus.');
    };
  }, []);

  return (
    <div>
      <h2>Komponen dengan Multiple Effects</h2>
      <p>Count: {count}</p>
      <button onClick={() => setCount(prev => prev + 1)}>Tambah Count</button>
      <br /><br />
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Ketik nama Anda"
      />
      <p>Nama: {name}</p>
    </div>
  );
}

export default MultiEffectComponent;
```

Dengan memahami `useEffect` Hook dan cara mengelola *side effects* serta *cleanup functions*, Anda akan dapat membangun komponen React yang lebih kuat dan efisien, yang dapat berinteraksi dengan dunia luar aplikasi Anda dengan cara yang terkontrol.

---

## Rangkuman

### Apa yang Telah Dipelajari

Pada modul **BAB 2.3: Component Lifecycle dan useEffect Hook**, kita telah mempelajari siklus hidup komponen React dan cara mengelola efek samping pada functional components:

#### 1. **Component Lifecycle Phases**
- Tiga fase utama: Mounting (pemasangan), Updating (pembaruan), Unmounting (pelepasan)
- Penting untuk memahami kapan komponen di-render, di-update, dan dihapus dari DOM

#### 2. **useEffect Hook Introduction**
- `useEffect` digunakan untuk mengelola side effects di functional components
- Efek samping: fetching data, manipulasi DOM, event listeners, timers, dsb
- useEffect menerima dua argumen: fungsi effect dan dependency array

#### 3. **Effect Dependencies Array**
- Tanpa dependency array: effect dijalankan setiap render
- Array kosong (`[]`): effect hanya dijalankan sekali saat mounting
- Array dengan dependensi: effect dijalankan ulang saat dependensi berubah

#### 4. **Cleanup Functions**
- Fungsi cleanup dijalankan sebelum effect dijalankan ulang atau saat komponen unmount
- Digunakan untuk membersihkan event listeners, timers, subscriptions, dsb

#### 5. **Multiple useEffect Hooks**
- Dapat menggunakan beberapa useEffect dalam satu komponen
- Memisahkan efek samping berdasarkan concern (misal: satu untuk data, satu untuk event listener)

### Poin-Poin Penting

✅ **Component lifecycle** - pahami mounting, updating, unmounting  
✅ **useEffect** - solusi utama side effects di functional components  
✅ **Dependency array** - kontrol kapan effect dijalankan  
✅ **Cleanup function** - pastikan resource dibersihkan dengan benar  
✅ **Multiple useEffect** - pisahkan efek samping untuk kode yang rapi dan terkontrol  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari kesembilan:

1. **Membuat Komponen dengan useEffect Sederhana**
   - Buatlah komponen React yang menggunakan `useEffect` untuk menampilkan pesan di konsol setiap kali komponen di-render atau di-update.

2. **Menggunakan useEffect dengan Dependency Array**
   - Buatlah komponen yang menampilkan data pengguna (misal: nama, email) berdasarkan ID.
   - Gunakan `useEffect` dengan dependency array agar data hanya di-*fetch* ulang saat ID berubah.

3. **Menerapkan Cleanup Function**
   - Buatlah komponen yang melacak ukuran jendela browser (window width & height) dan memperbarui state saat ukuran berubah.
   - Pastikan event listener dibersihkan dengan benar menggunakan cleanup function di `useEffect`.

4. **Menggunakan Multiple useEffect dalam Satu Komponen**
   - Buatlah komponen yang memiliki dua state berbeda (misal: count dan name).
   - Gunakan dua atau lebih `useEffect` untuk melakukan efek samping yang berbeda (misal: update document title saat count berubah, log ke konsol saat name berubah).

5. **Simulasi Fetch Data dengan useEffect**
   - Buatlah komponen yang melakukan simulasi fetch data (misal: menggunakan `setTimeout` untuk menunda pengambilan data) saat komponen pertama kali di-mount.
   - Tampilkan loading state saat data sedang diambil, dan tampilkan data setelah selesai.

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.