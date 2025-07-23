# BAB 2.2: Conditional Rendering dan Lists

## Tujuan Pembelajaran

*   Memahami berbagai teknik *Conditional Rendering* di React.
*   Mampu menggunakan `if/else`, *ternary operator*, dan `&&` *operator* untuk *conditional rendering*.
*   Mampu menggunakan *switch statements* dalam JSX (secara tidak langsung).
*   Mampu me-*render* daftar (*lists*) menggunakan `map()`.
*   Memahami konsep `key` dan pentingnya dalam me-*render* daftar.
*   Mampu menangani *empty states* saat me-*render* daftar.
*   Memahami dasar-dasar optimasi *rendering* React terkait dengan *keys*.

## Materi Pembelajaran:

### 1. If/else, Ternary Operator, dan && Operator

*Conditional rendering* adalah kemampuan untuk me-*render* elemen atau komponen yang berbeda berdasarkan kondisi tertentu. React menyediakan beberapa cara untuk melakukan ini.

#### a. Menggunakan `if/else` Statement (di luar JSX)

Ini adalah cara paling dasar untuk melakukan *conditional rendering*. Anda menulis logika `if/else` di luar *return* dari komponen, dan mengembalikan JSX yang berbeda berdasarkan kondisi.

```jsx
import React from 'react';

function UserGreeting() {
  return <h1>Selamat datang kembali!</h1>;
}

function GuestGreeting() {
  return <h1>Silakan masuk.</h1>;
}

function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

function App() {
  return (
    <div>
      <h2>Conditional Rendering dengan if/else</h2>
      <Greeting isLoggedIn={true} />
      <Greeting isLoggedIn={false} />
    </div>
  );
}

export default App;
```

#### b. Menggunakan Ternary Operator (`condition ? trueExpression : falseExpression`)

Operator ternary adalah cara yang ringkas untuk melakukan *conditional rendering* di dalam JSX. Ini sangat berguna untuk kondisi sederhana di mana Anda perlu memilih antara dua ekspresi.

```jsx
import React from 'react';

function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      {props.isLoggedIn ? 'Logout' : 'Login'}
    </button>
  );
}

function App() {
  const [isLoggedIn, setIsLoggedIn] = React.useState(false);

  const handleLoginClick = () => {
    setIsLoggedIn(true);
  };

  const handleLogoutClick = () => {
    setIsLoggedIn(false);
  };

  return (
    <div>
      <h2>Conditional Rendering dengan Ternary Operator</h2>
      {isLoggedIn ? (
        <p>Anda sudah login.</p>
      ) : (
        <p>Anda belum login.</p>
      )}
      <LoginButton
        isLoggedIn={isLoggedIn}
        onClick={isLoggedIn ? handleLogoutClick : handleLoginClick}
      />
    </div>
  );
}

export default App;
```

#### c. Menggunakan Operator Logika `&&` (AND)

Operator `&&` sangat berguna ketika Anda ingin me-*render* sesuatu hanya jika kondisi tertentu benar, dan tidak me-*render* apa pun jika kondisi salah. Jika kondisi pertama (`condition`) adalah `true`, ekspresi setelah `&&` akan dievaluasi dan di-*render*. Jika `condition` adalah `false`, React akan mengabaikan ekspresi setelah `&&`.

```jsx
import React from 'react';

function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h2>Conditional Rendering dengan && Operator</h2>
      <h1>Halo!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          Anda memiliki {unreadMessages.length} pesan belum dibaca.
        </h2>
      }
      {unreadMessages.length === 0 &&
        <p>Tidak ada pesan baru.</p>
      }
    </div>
  );
}

function App() {
  const messages = ['React', 'Re: React', 'Re:Re: React'];
  return (
    <div>
      <Mailbox unreadMessages={messages} />
      <Mailbox unreadMessages={[]} />
    </div>
  );
}

export default App;
```

### 2. Switch Statements dalam JSX

Meskipun Anda tidak dapat menggunakan `switch` *statement* secara langsung di dalam JSX, Anda dapat menggunakannya di luar JSX untuk mengembalikan elemen yang berbeda berdasarkan nilai variabel. Ini berguna ketika Anda memiliki banyak kondisi yang berbeda.

```jsx
import React from 'react';

function WarningBanner(props) {
  if (!props.warn) {
    return null; // Tidak me-render apa-apa
  }

  return (
    <div style={{ backgroundColor: 'yellow', padding: '10px', border: '1px solid orange' }}>
      Peringatan!
    </div>
  );
}

function PageStatus({ status }) {
  let content;
  switch (status) {
    case 'loading':
      content = <p>Memuat data...</p>;
      break;
    case 'success':
      content = <p style={{ color: 'green' }}>Data berhasil dimuat!</p>;
      break;
    case 'error':
      content = <p style={{ color: 'red' }}>Terjadi kesalahan saat memuat data.</p>;
      break;
    default:
      content = <p>Status tidak diketahui.</p>;
  }
  return content;
}

function App() {
  const [showWarning, setShowWarning] = React.useState(true);
  const [dataStatus, setDataStatus] = React.useState('loading');

  const toggleWarning = () => {
    setShowWarning(prev => !prev);
  };

  const changeStatus = () => {
    const statuses = ['loading', 'success', 'error', 'unknown'];
    const currentIndex = statuses.indexOf(dataStatus);
    const nextIndex = (currentIndex + 1) % statuses.length;
    setDataStatus(statuses[nextIndex]);
  };

  return (
    <div>
      <h2>Conditional Rendering dengan Switch Statement</h2>
      <WarningBanner warn={showWarning} />
      <button onClick={toggleWarning}>
        {showWarning ? 'Sembunyikan' : 'Tampilkan'} Peringatan
      </button>

      <hr />

      <h3>Status Data:</h3>
      <PageStatus status={dataStatus} />
      <button onClick={changeStatus}>Ubah Status</button>
    </div>
  );
}

export default App;
```

### 3. Rendering Lists dengan map()

Me-*render* daftar elemen adalah tugas umum lainnya di React. Cara paling umum dan direkomendasikan adalah menggunakan fungsi `map()` pada *array* JavaScript untuk membuat daftar elemen JSX.

```jsx
import React from 'react';

function ProductList() {
  const products = [
    { id: 1, name: 'Laptop', price: 1200 },
    { id: 2, name: 'Mouse', price: 25 },
    { id: 3, name: 'Keyboard', price: 75 },
    { id: 4, name: 'Monitor', price: 300 }
  ];

  return (
    <div>
      <h2>Daftar Produk</h2>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            {product.name} - ${product.price}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default ProductList;
```

### 4. Keys dan Pentingnya dalam Rendering Lists

Ketika Anda me-*render* daftar elemen, React membutuhkan cara untuk mengidentifikasi setiap item dalam daftar secara unik. Inilah mengapa atribut `key` sangat penting.

*   **Apa itu `key`?**
    `key` adalah *prop* khusus yang harus Anda sertakan saat membuat daftar elemen. `key` membantu React mengidentifikasi item mana yang telah berubah, ditambahkan, atau dihapus.

*   **Mengapa `key` Penting?**
    *   **Optimasi Performa**: `key` memungkinkan React untuk secara efisien memperbarui UI. Ketika daftar berubah, React menggunakan `key` untuk menentukan elemen mana yang perlu di-*re-render* dan mana yang dapat dipertahankan. Tanpa `key` yang tepat, React mungkin akan me-*re-render* seluruh daftar, yang tidak efisien.
    *   **Stabilitas UI**: `key` memastikan bahwa *state* internal komponen (misalnya, nilai input dalam formulir) tetap terkait dengan item yang benar dalam daftar, bahkan jika urutan daftar berubah.

*   **Aturan untuk `key`:**
    *   **Unik**: `key` harus unik di antara *sibling* (elemen pada level yang sama dalam daftar).
    *   **Stabil**: `key` harus tetap sama untuk item yang sama di seluruh *re-render*. Jangan menghasilkan `key` secara acak.
    *   **Hindari Indeks Array sebagai `key` (jika memungkinkan)**: Menggunakan indeks *array* (`index`) sebagai `key` hanya aman jika:
        1.  Daftar tidak akan pernah diurutkan ulang.
        2.  Daftar tidak akan pernah difilter (item dihapus).
        3.  Daftar tidak akan pernah memiliki item yang ditambahkan di tengah.
        Jika salah satu kondisi di atas tidak terpenuhi, menggunakan indeks sebagai `key` dapat menyebabkan masalah performa dan *bug* yang sulit dilacak. Selalu gunakan ID unik dari data Anda jika tersedia.

**Contoh Masalah Tanpa `key` yang Tepat:**

Bayangkan daftar item dengan input. Jika Anda menggunakan indeks sebagai `key` dan kemudian mengurutkan ulang daftar, nilai input mungkin tetap berada di posisi yang sama meskipun item yang terkait telah berpindah.

```jsx
// Contoh yang menunjukkan masalah dengan index sebagai key (JANGAN DITIRU UNTUK LIST YANG BERUBAH)
import React, { useState } from 'react';

function BadTodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: 'Makan', completed: false },
    { id: 2, text: 'Tidur', completed: false },
    { id: 3, text: 'Coding', completed: false },
  ]);

  const reverseTodos = () => {
    setTodos([...todos].reverse()); // Membalik urutan
  };

  return (
    <div>
      <h2>Daftar Tugas (Bad Key Example)</h2>
      <button onClick={reverseTodos}>Balik Urutan</button>
      <ul>
        {todos.map((todo, index) => (
          // Menggunakan index sebagai key - BERPOTENSI MASALAH
          <li key={index}>
            {todo.text} <input type="text" placeholder="Catatan" />
          </li>
        ))}
      </ul>
      <p style={{ color: 'red' }}>
        Coba ketik sesuatu di kotak catatan, lalu klik "Balik Urutan".
        Anda akan melihat bahwa catatan tetap di posisi yang sama, bukan mengikuti itemnya.
      </p>
    </div>
  );
}

export default BadTodoList;
```

### 5. Handling Empty States

Saat me-*render* daftar, penting untuk mempertimbangkan apa yang akan ditampilkan jika daftar tersebut kosong. Ini disebut *handling empty states*. Anda dapat menggunakan *conditional rendering* untuk menampilkan pesan yang sesuai.

```jsx
import React from 'react';

function ItemList({ items }) {
  return (
    <div>
      <h2>Daftar Item</h2>
      {items.length > 0 ? (
        <ul>
          {items.map(item => (
            <li key={item.id}>{item.name}</li>
          ))}
        </ul>
      ) : (
        <p>Tidak ada item yang tersedia saat ini.</p>
      )}
    </div>
  );
}

function App() {
  const availableItems = [
    { id: 1, name: 'Buku' },
    { id: 2, name: 'Pensil' },
    { id: 3, name: 'Penghapus' }
  ];
  const noItems = [];

  return (
    <div>
      <ItemList items={availableItems} />
      <hr />
      <ItemList items={noItems} />
    </div>
  );
}

export default App;
```

Dengan menguasai *conditional rendering* dan *rendering lists* dengan `key` yang tepat, Anda akan dapat membangun UI yang sangat dinamis dan efisien di React.

---

## Rangkuman

### Apa yang Telah Dipelajari

Pada modul **BAB 2.2: Conditional Rendering dan Lists**, kita telah mempelajari teknik-teknik penting untuk menampilkan UI secara dinamis di React:

#### 1. **Conditional Rendering**
- Menggunakan `if/else` di luar JSX untuk logika kompleks
- Operator ternary (`? :`) untuk memilih antara dua ekspresi di dalam JSX
- Operator logika `&&` untuk menampilkan elemen jika kondisi benar

#### 2. **Switch Statements dalam JSX**
- Menggunakan `switch` di luar JSX untuk memilih elemen berdasarkan banyak kondisi
- Cocok untuk status yang memiliki banyak kemungkinan (loading, success, error, dst)

#### 3. **Rendering Lists dengan map()**
- Menggunakan fungsi `map()` untuk merender array data menjadi elemen JSX
- Praktik umum untuk menampilkan daftar produk, tugas, user, dsb

#### 4. **Keys dan Pentingnya dalam Rendering Lists**
- `key` adalah prop khusus yang harus unik di antara sibling
- Membantu React mengidentifikasi perubahan, menambah, atau menghapus item
- Hindari menggunakan indeks array sebagai key jika data bisa berubah urutan

#### 5. **Handling Empty States**
- Menampilkan pesan khusus jika array data kosong
- Meningkatkan UX dengan feedback yang jelas saat tidak ada data

### Poin-Poin Penting

✅ **Conditional rendering** - gunakan if/else, ternary, dan && sesuai kebutuhan  
✅ **Switch statement** - solusi untuk banyak kondisi  
✅ **List rendering dengan map()** - cara utama menampilkan array data  
✅ **Key unik** - wajib untuk performa dan stabilitas UI  
✅ **Empty state** - selalu tangani kasus data kosong untuk UX yang baik  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari kedelapan:

1. **Membuat Conditional Rendering dengan if/else**
   - Buatlah komponen React yang menampilkan pesan berbeda berdasarkan nilai boolean (misal: status login, status aktif, dsb) menggunakan logika `if/else` di luar JSX.

2. **Menggunakan Ternary Operator dan && Operator**
   - Buatlah komponen yang menampilkan elemen berbeda menggunakan operator ternary (`? :`) dan operator logika `&&` di dalam JSX.
   - Contoh: menampilkan pesan selamat datang jika user login, atau menampilkan jumlah pesan jika ada pesan baru.

3. **Membuat Komponen dengan Switch Statement**
   - Buatlah komponen yang menerima prop status (misal: "loading", "success", "error", "unknown") dan menampilkan elemen berbeda menggunakan logika `switch` di luar JSX.

4. **Rendering List dengan map() dan Key**
   - Buatlah komponen yang menerima array data (misal: daftar produk, tugas, atau user) dan merendernya sebagai list menggunakan fungsi `map()`.
   - Pastikan setiap elemen list memiliki prop `key` yang unik.

5. **Handling Empty State pada List**
   - Modifikasi komponen list di atas agar menampilkan pesan khusus jika array data kosong (empty state).

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.