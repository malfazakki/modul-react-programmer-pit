# BAB 1.5: State dan useState Hook

## Tujuan Pembelajaran

*   Memahami konsep fundamental *State* dalam React.
*   Mampu menggunakan `useState` Hook untuk mengelola *state*.
*   Memahami bagaimana pembaruan *state* memicu *re-rendering*.
*   Mampu mengelola beberapa variabel *state* dalam satu komponen.
*   Mampu mengelola *state* yang berupa objek dan *array*.

## Materi Pembelajaran

### 1. Konsep State vs Props

Sebelum masuk ke `useState`, penting untuk memahami perbedaan mendasar antara *state* dan *props*.

*   **Props (Properties)**:
    *   Diteruskan dari komponen induk ke komponen anak.
    *   Bersifat *read-only* (tidak dapat diubah oleh komponen anak).
    *   Digunakan untuk komunikasi satu arah (induk ke anak).
    *   Mirip dengan argumen fungsi.

*   **State**:
    *   Dikelola di dalam komponen itu sendiri.
    *   Dapat diubah oleh komponen itu sendiri.
    *   Digunakan untuk mengelola data yang berubah seiring waktu (misalnya, nilai input formulir, status *toggle*, data yang diambil dari API).
    *   Ketika *state* berubah, komponen akan di-*re-render* untuk menampilkan perubahan tersebut.

Singkatnya, *props* adalah cara komponen berkomunikasi satu sama lain, sedangkan *state* adalah cara komponen mengelola data internalnya sendiri.

### 2. useState Hook Syntax

`useState` adalah salah satu dari React Hooks yang memungkinkan Anda menambahkan *state* ke *functional components*.

**Sintaks Dasar:**

```jsx
import React, { useState } from 'react';

function MyComponent() {
  // Deklarasi variabel state
  // `count` adalah variabel state saat ini
  // `setCount` adalah fungsi untuk memperbarui `count`
  // `0` adalah nilai awal untuk `count`
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Anda mengklik {count} kali</p>
      <button onClick={() => setCount(count + 1)}>
        Klik Saya
      </button>
    </div>
  );
}

export default MyComponent;
```

**Penjelasan:**
*   `useState` adalah fungsi yang diimpor dari React.
*   Ketika Anda memanggil `useState(initialValue)`, ia mengembalikan *array* dengan dua elemen:
    1.  Variabel *state* saat ini (`count` dalam contoh di atas).
    2.  Fungsi untuk memperbarui variabel *state* tersebut (`setCount` dalam contoh di atas).
*   Anda dapat menggunakan *array destructuring* (`const [count, setCount] = useState(0);`) untuk dengan mudah mengakses kedua elemen ini.
*   Argumen yang Anda berikan ke `useState()` (misalnya `0`) adalah nilai awal dari *state*.

### 3. State Updates dan Re-rendering

Ketika Anda memanggil fungsi *setter* yang dikembalikan oleh `useState` (misalnya `setCount`), React akan melakukan dua hal:
1.  **Memperbarui nilai *state***: Nilai variabel *state* akan diubah menjadi nilai baru yang Anda berikan.
2.  **Memicu *re-render***: React akan menjadwalkan *re-render* komponen tersebut. Ini berarti fungsi komponen akan dieksekusi ulang, dan UI akan diperbarui untuk mencerminkan nilai *state* yang baru.

**Penting:**
*   **Jangan memodifikasi *state* secara langsung**: Selalu gunakan fungsi *setter* (`setCount`, `setName`, dll.) untuk memperbarui *state*. Memodifikasi *state* secara langsung tidak akan memicu *re-render* dan dapat menyebabkan perilaku yang tidak terduga.
    ```jsx
    // SALAH: Jangan lakukan ini
    // count = count + 1;

    // BENAR: Selalu gunakan setter function
    setCount(count + 1);
    ```
*   **Pembaruan *state* bisa bersifat asinkron**: React dapat mengelompokkan beberapa pembaruan *state* untuk performa yang lebih baik. Jika *state* baru Anda bergantung pada *state* sebelumnya, gunakan *functional update* (meneruskan fungsi ke *setter*).

**Contoh Functional Update:**

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => {
    // Ini mungkin tidak bekerja dengan benar jika ada banyak pembaruan cepat
    // setCount(count + 1);

    // Ini adalah cara yang lebih aman karena menggunakan nilai state sebelumnya
    setCount(prevCount => prevCount + 1);
  };

  const decrement = () => {
    setCount(prevCount => prevCount - 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Tambah</button>
      <button onClick={decrement}>Kurang</button>
    </div>
  );
}

export default Counter;
```

### 4. Multiple State Variables

Anda dapat mendeklarasikan beberapa variabel *state* dalam satu komponen dengan memanggil `useState` berkali-kali. Ini adalah praktik yang direkomendasikan daripada mencoba menyimpan semua *state* dalam satu objek besar, kecuali jika *state* tersebut secara logis terkait.

**Contoh Multiple State Variables:**

```jsx
import React, { useState } from 'react';

function UserProfile() {
  const [name, setName] = useState('John Doe');
  const [age, setAge] = useState(30);
  const [isEditing, setIsEditing] = useState(false);

  return (
    <div>
      <h2>Profil Pengguna</h2>
      {isEditing ? (
        <div>
          <input
            type="text"
            value={name}
            onChange={(e) => setName(e.target.value)}
          />
          <input
            type="number"
            value={age}
            onChange={(e) => setAge(Number(e.target.value))}
          />
          <button onClick={() => setIsEditing(false)}>Simpan</button>
        </div>
      ) : (
        <div>
          <p>Nama: {name}</p>
          <p>Usia: {age}</p>
          <button onClick={() => setIsEditing(true)}>Edit Profil</button>
        </div>
      )}
    </div>
  );
}

export default UserProfile;
```

### 5. State dengan Objects dan Arrays

Ketika *state* Anda adalah objek atau *array*, Anda harus berhati-hati untuk tidak memutasinya secara langsung. Sebaliknya, Anda harus membuat salinan baru dari objek atau *array* tersebut dengan perubahan yang diinginkan, lalu meneruskan salinan baru itu ke fungsi *setter*. Ini karena React melakukan perbandingan dangkal (shallow comparison) untuk menentukan apakah *state* telah berubah.

#### a. State dengan Objects

Untuk memperbarui objek, gunakan *spread operator* (`...`) untuk menyalin properti yang ada dan menimpa properti yang ingin Anda ubah.

```jsx
import React, { useState } from 'react';

function ContactForm() {
  const [formData, setFormData] = useState({
    firstName: '',
    lastName: '',
    email: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prevFormData => ({
      ...prevFormData, // Salin semua properti yang ada
      [name]: value     // Timpa properti yang berubah
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Data yang disubmit:', formData);
    alert(`Terima kasih, ${formData.firstName}! Data Anda telah disubmit.`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Formulir Kontak</h2>
      <div>
        <label>Nama Depan:</label>
        <input
          type="text"
          name="firstName"
          value={formData.firstName}
          onChange={handleChange}
        />
      </div>
      <div>
        <label>Nama Belakang:</label>
        <input
          type="text"
          name="lastName"
          value={formData.lastName}
          onChange={handleChange}
        />
      </div>
      <div>
        <label>Email:</label>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
        />
      </div>
      <button type="submit">Submit</button>
      <p>Current Data: {JSON.stringify(formData)}</p>
    </form>
  );
}

export default ContactForm;
```

#### b. State dengan Arrays

Untuk memperbarui *array*, gunakan metode *array* yang tidak memutasi (*non-mutating methods*) seperti `map`, `filter`, `slice`, atau *spread operator* (`...`) untuk membuat *array* baru.

```jsx
import React, { useState } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: 'Belajar React Hooks', completed: false },
    { id: 2, text: 'Membangun Aplikasi Todo', completed: false },
  ]);
  const [newTodoText, setNewTodoText] = useState('');

  const addTodo = (e) => {
    e.preventDefault();
    if (newTodoText.trim() === '') return;

    const newTodo = {
      id: todos.length + 1,
      text: newTodoText,
      completed: false,
    };
    setTodos(prevTodos => [...prevTodos, newTodo]); // Tambahkan todo baru
    setNewTodoText('');
  };

  const toggleTodo = (id) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  const deleteTodo = (id) => {
    setTodos(prevTodos => prevTodos.filter(todo => todo.id !== id)); // Hapus todo
  };

  return (
    <div>
      <h2>Daftar Tugas</h2>
      <form onSubmit={addTodo}>
        <input
          type="text"
          value={newTodoText}
          onChange={(e) => setNewTodoText(e.target.value)}
          placeholder="Tambahkan tugas baru..."
        />
        <button type="submit">Tambah</button>
      </form>
      <ul>
        {todos.map(todo => (
          <li key={todo.id} style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
            <span onClick={() => toggleTodo(todo.id)} style={{ cursor: 'pointer' }}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)} style={{ marginLeft: '10px' }}>
              Hapus
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoList;
```

Memahami dan menguasai `useState` adalah langkah krusial dalam membangun aplikasi React yang interaktif dan dinamis. Dengan ini, Anda dapat membuat komponen yang merespons interaksi pengguna dan menampilkan data yang berubah.

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari kelima:

1. **Membuat Counter Sederhana dengan useState**
   - Buatlah komponen React bernama `Counter` yang menampilkan angka dan dua tombol: "Tambah" dan "Kurang".
   - Gunakan `useState` untuk mengelola nilai angka dan perbarui nilainya saat tombol diklik.

2. **Mengelola Beberapa State dalam Satu Komponen**
   - Buatlah komponen `UserProfile` yang memiliki state untuk nama, usia, dan status edit (editing/not editing).
   - Tampilkan data profil dan sediakan tombol untuk mengubah ke mode edit, lalu simpan perubahan ke state.

3. **Mengelola State Berupa Objek**
   - Buatlah komponen formulir kontak (`ContactForm`) yang menggunakan satu state objek untuk menyimpan beberapa field (nama depan, nama belakang, email).
   - Tampilkan data yang sedang diinput secara real-time di bawah formulir.

4. **Mengelola State Berupa Array**
   - Buatlah komponen `TodoList` yang menggunakan state array untuk menyimpan daftar tugas.
   - Sediakan fitur untuk menambah, menandai selesai, dan menghapus tugas dari daftar.

5. **Menggunakan Functional Update pada State**
   - Pada salah satu komponen di atas (misal: `Counter`), gunakan *functional update* pada setter state untuk memastikan pembaruan state yang aman jika terjadi update berturut-turut.

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.