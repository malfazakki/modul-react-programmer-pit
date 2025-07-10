# BAB 2.4: API Integration dan Async Operations

## Tujuan Pembelajaran:

*   Mampu melakukan *fetching* data dari API menggunakan `Fetch API` dan `Axios`.
*   Mampu menggunakan `async/await` dalam `useEffect` untuk operasi asinkron.
*   Mampu mengelola *loading states* dan *error handling* yang tepat.
*   Mampu menggunakan `AbortController` untuk *cleanup* permintaan API.
*   Mampu melakukan manipulasi data JSON.

## Materi Pembelajaran:

### 1. Fetch API dan Axios

Ada dua cara utama untuk melakukan permintaan HTTP (seperti mengambil data dari API) di JavaScript modern: `Fetch API` bawaan *browser* dan *library* pihak ketiga seperti `Axios`.

#### a. Fetch API

`Fetch API` adalah antarmuka bawaan *browser* untuk melakukan permintaan jaringan. Ini mengembalikan `Promise`, yang membuatnya mudah digunakan dengan `async/await`.

**Kelebihan:**
*   Bawaan *browser*, tidak perlu instalasi tambahan.
*   Sintaks berbasis `Promise` yang modern.

**Kekurangan:**
*   Tidak secara otomatis menangani respons kesalahan HTTP (misalnya, status 404 atau 500 tidak akan memicu `catch()` kecuali Anda memeriksanya secara manual).
*   Tidak memiliki fitur pembatalan permintaan bawaan (membutuhkan `AbortController`).
*   Tidak ada *progress indicator* bawaan.

**Contoh Fetch API:**

```jsx
import React, { useState, useEffect } from 'react';

function FetchDataComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts/1');
        if (!response.ok) { // Periksa jika respons tidak OK (misalnya, 404, 500)
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, []); // Array kosong, effect hanya berjalan sekali saat mounting

  if (loading) return <p>Memuat data...</p>;
  if (error) return <p>Terjadi kesalahan: {error.message}</p>;

  return (
    <div>
      <h2>Data dari Fetch API</h2>
      <h3>{data.title}</h3>
      <p>{data.body}</p>
    </div>
  );
}

export default FetchDataComponent;
```

#### b. Axios

`Axios` adalah *library* HTTP *client* berbasis `Promise` yang populer untuk *browser* dan Node.js.

**Kelebihan:**
*   Menangani respons kesalahan HTTP secara otomatis (status 4xx/5xx akan memicu `catch()`).
*   Fitur pembatalan permintaan bawaan.
*   Transformasi data JSON otomatis.
*   Interseptor permintaan dan respons.
*   Dukungan yang lebih baik untuk *progress indicator*.

**Instalasi Axios:**
```bash
npm install axios
# atau
yarn add axios
```

**Contoh Axios:**

```jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios'; // Import axios

function AxiosDataComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await axios.get('https://jsonplaceholder.typicode.com/users/1');
        setData(response.data); // Axios otomatis mengurai JSON
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  if (loading) return <p>Memuat data pengguna...</p>;
  if (error) return <p>Terjadi kesalahan: {error.message}</p>;

  return (
    <div>
      <h2>Data dari Axios</h2>
      <h3>{data.name}</h3>
      <p>Email: {data.email}</p>
      <p>Telepon: {data.phone}</p>
    </div>
  );
}

export default AxiosDataComponent;
```

### 2. Async/await dalam useEffect

Anda tidak dapat langsung membuat fungsi `useEffect` menjadi `async`. Namun, Anda dapat mendefinisikan fungsi `async` di dalam `useEffect` dan kemudian memanggilnya.

```jsx
import React, { useState, useEffect } from 'react';

function AsyncAwaitInEffect() {
  const [todos, setTodos] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchTodos = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/todos?_limit=5');
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        setTodos(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchTodos(); // Panggil fungsi async di dalam useEffect
  }, []);

  if (loading) return <p>Memuat daftar tugas...</p>;
  if (error) return <p>Terjadi kesalahan: {error.message}</p>;

  return (
    <div>
      <h2>Daftar Tugas (Async/Await)</h2>
      <ul>
        {todos.map(todo => (
          <li key={todo.id} style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
            {todo.title}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default AsyncAwaitInEffect;
```

### 3. Loading States dan Error Handling

Penting untuk memberikan umpan balik kepada pengguna saat data sedang dimuat (*loading state*) atau jika terjadi kesalahan (*error handling*).

*   **Loading State**: Gunakan variabel *state* (misalnya `loading`) untuk menunjukkan apakah data sedang diambil. Tampilkan indikator *loading* atau pesan yang sesuai.
*   **Error Handling**: Gunakan blok `try...catch` untuk menangkap kesalahan jaringan atau API. Simpan pesan kesalahan dalam *state* dan tampilkan kepada pengguna.

Contoh di atas untuk `FetchDataComponent` dan `AxiosDataComponent` sudah mengimplementasikan *loading states* dan *error handling* dasar.

### 4. AbortController untuk Cleanup

Ketika Anda melakukan permintaan API di `useEffect`, ada kemungkinan komponen di-*unmount* sebelum permintaan selesai. Ini dapat menyebabkan kesalahan "Can't perform a React state update on an unmounted component". Untuk mencegahnya, Anda dapat menggunakan `AbortController` untuk membatalkan permintaan saat komponen di-*unmount*.

**Catatan**: `AbortController` adalah bagian dari `Fetch API`. Untuk `Axios`, Anda bisa menggunakan `CancelToken` atau `AbortController` (Axios versi terbaru mendukung `AbortController`).

**Contoh AbortController dengan Fetch API:**

```jsx
import React, { useState, useEffect } from 'react';

function AbortFetchComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const abortController = new AbortController(); // Buat AbortController
    const signal = abortController.signal; // Dapatkan signal

    const fetchData = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts/1', { signal }); // Teruskan signal
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const result = await response.json();
        setData(result);
      } catch (err) {
        if (err.name === 'AbortError') {
          console.log('Fetch dibatalkan');
        } else {
          setError(err);
        }
      } finally {
        setLoading(false);
      }
    };

    fetchData();

    // Cleanup function: batalkan permintaan saat komponen di-unmount
    return () => {
      abortController.abort();
      console.log('Permintaan dibatalkan saat cleanup.');
    };
  }, []);

  if (loading) return <p>Memuat data (dengan pembatalan)...</p>;
  if (error) return <p>Terjadi kesalahan: {error.message}</p>;

  return (
    <div>
      <h2>Data dari Fetch API (dengan AbortController)</h2>
      <h3>{data.title}</h3>
      <p>{data.body}</p>
    </div>
  );
}

export default AbortFetchComponent;
```

### 5. JSON Data Manipulation

Setelah Anda mengambil data JSON dari API, Anda sering perlu memanipulasinya sebelum menampilkannya di UI. JavaScript menyediakan banyak metode untuk bekerja dengan objek dan *array*.

**Contoh Manipulasi Data JSON:**

Misalkan kita mengambil daftar pengguna dan ingin menampilkan hanya nama dan email mereka, serta memfilter pengguna tertentu.

```jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function UserListManipulated() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get('https://jsonplaceholder.typicode.com/users');
        // Manipulasi data: hanya ambil nama dan email, lalu filter
        const processedUsers = response.data
          .map(user => ({
            id: user.id,
            name: user.name,
            email: user.email,
            city: user.address.city // Mengambil data nested
          }))
          .filter(user => user.city === 'Gwenborough'); // Filter berdasarkan kota

        setUsers(processedUsers);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <p>Memuat daftar pengguna...</p>;
  if (error) return <p>Terjadi kesalahan: {error.message}</p>;
  if (users.length === 0) return <p>Tidak ada pengguna yang ditemukan di Gwenborough.</p>;

  return (
    <div>
      <h2>Daftar Pengguna (Manipulasi Data)</h2>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            <strong>{user.name}</strong> ({user.email}) - {user.city}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default UserListManipulated;
```

Dengan menguasai integrasi API dan operasi asinkron, Anda akan dapat membangun aplikasi React yang dapat berinteraksi dengan *backend* dan menampilkan data dinamis dari sumber eksternal.

---

## Rangkuman

### Apa yang Telah Dipelajari

Kita telah mempelajari cara mengambil data dari API, mengelola operasi asinkron, dan memanipulasi data di React:

#### 1. **Fetch API dan Axios**
- Fetch API: native browser, berbasis Promise, perlu pengecekan error manual
- Axios: library populer, error handling otomatis, fitur tambahan seperti cancel request dan transformasi data

#### 2. **Async/await dalam useEffect**
- Fungsi async tidak bisa langsung di useEffect, harus didefinisikan di dalam lalu dipanggil
- Memudahkan penulisan kode asynchronous yang lebih bersih dan mudah dibaca

#### 3. **Loading States dan Error Handling**
- Gunakan state untuk loading dan error agar user mendapat feedback saat data diambil atau terjadi error
- Tampilkan pesan loading/error sesuai kondisi

#### 4. **AbortController untuk Cleanup**
- Gunakan AbortController untuk membatalkan request jika komponen unmount sebelum fetch selesai
- Mencegah error update state pada komponen yang sudah unmount

#### 5. **JSON Data Manipulation**
- Setelah fetch data, gunakan fungsi array seperti map, filter, slice untuk memanipulasi data sebelum ditampilkan
- Hanya tampilkan data yang relevan sesuai kebutuhan UI

### Poin-Poin Penting

✅ **Fetch API & Axios** - dua cara utama fetch data di React  
✅ **Async/await di useEffect** - penulisan kode async yang rapi  
✅ **Loading & error state** - selalu tampilkan feedback ke user  
✅ **AbortController** - cleanup request untuk mencegah memory leak  
✅ **Manipulasi data JSON** - gunakan fungsi array untuk data yang relevan  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari kesepuluh:

1. **Mengambil Data dari API dengan Fetch API**
   - Buatlah komponen React yang mengambil data dari API publik (misal: https://jsonplaceholder.typicode.com/posts/1) menggunakan Fetch API.
   - Tampilkan data yang diambil di UI.

2. **Mengambil Data dari API dengan Axios**
   - Buatlah komponen yang mengambil data pengguna dari API publik (misal: https://jsonplaceholder.typicode.com/users/1) menggunakan Axios.
   - Tampilkan nama, email, dan nomor telepon pengguna.

3. **Mengelola Loading State dan Error Handling**
   - Tambahkan state untuk loading dan error pada salah satu komponen di atas.
   - Tampilkan pesan loading saat data sedang diambil, dan tampilkan pesan error jika terjadi kesalahan.

4. **Menggunakan AbortController untuk Cleanup**
   - Modifikasi komponen Fetch API agar menggunakan AbortController untuk membatalkan permintaan jika komponen di-unmount sebelum data selesai diambil.

5. **Manipulasi Data JSON**
   - Buatlah komponen yang mengambil daftar data (misal: daftar users atau posts) dari API, lalu tampilkan hanya sebagian data (misal: filter berdasarkan kriteria tertentu, atau hanya tampilkan nama dan email).
   - Gunakan fungsi array seperti `map`, `filter`, atau `slice` untuk memanipulasi data sebelum ditampilkan.

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.