# Modul Pembelajaran React.js - Hari ke-21

## Topik Harian: State Management dengan Redux Toolkit

Pada hari kedua puluh satu ini, kita akan mempelajari **Redux Toolkit**, *library* resmi yang direkomendasikan untuk pengembangan Redux. Redux adalah *library* manajemen *state* yang populer untuk aplikasi JavaScript, dan Redux Toolkit menyederhanakan proses pengembangan Redux dengan menyediakan *utility* dan praktik terbaik bawaan.

## Kompetensi:

*   Memahami prinsip-prinsip dasar Redux.
*   Mampu melakukan *setup* Redux Toolkit dalam aplikasi React.
*   Mampu membuat dan mengelola *slices* dan *reducers*.
*   Mampu menangani operasi asinkron dengan `createAsyncThunk`.
*   Mampu menggunakan Redux DevTools untuk *debugging*.

## Materi Pembelajaran:

### 1. Redux Principles

Sebelum masuk ke Redux Toolkit, mari kita ingat kembali tiga prinsip inti Redux:

1.  **Single Source of Truth**: Seluruh *state* aplikasi disimpan dalam satu objek besar yang disebut *store*. Ini membuat *debugging* lebih mudah dan memastikan konsistensi data.
2.  **State is Read-Only**: Satu-satunya cara untuk mengubah *state* adalah dengan mengeluarkan *action*, sebuah objek JavaScript biasa yang menjelaskan apa yang terjadi. Ini memastikan bahwa perubahan *state* dapat dilacak dan direproduksi.
3.  **Changes are Made with Pure Functions**: Untuk menentukan bagaimana *state tree* berubah sebagai respons terhadap *action*, Anda menulis *pure reducers*. *Reducers* adalah fungsi yang mengambil *state* sebelumnya dan *action* sebagai argumen, dan mengembalikan *state* berikutnya. Mereka tidak boleh memiliki *side effects*.

### 2. Redux Toolkit Setup

Redux Toolkit (RTK) adalah cara standar untuk menulis logika Redux. Ini mencakup *utility* yang membantu menyederhanakan banyak kasus penggunaan Redux yang umum, seperti konfigurasi *store*, pembuatan *reducers*, dan penanganan data asinkron.

**Instalasi:**
```bash
npm install @reduxjs/toolkit react-redux
# atau
yarn add @reduxjs/toolkit react-redux
```
*   `@reduxjs/toolkit`: Berisi semua *utility* Redux Toolkit.
*   `react-redux`: *Binding* resmi React untuk Redux, memungkinkan komponen React berinteraksi dengan *store* Redux.

**Langkah-langkah Setup Dasar:**

#### a. Buat Store (misalnya `src/app/store.js`)

```javascript
// src/app/store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from '../features/counter/counterSlice'; // Akan kita buat nanti

export const store = configureStore({
  reducer: {
    counter: counterReducer, // Tambahkan reducer Anda di sini
    // user: userReducer, // Contoh reducer lain
  },
});
```
`configureStore` menyederhanakan konfigurasi *store* Redux, secara otomatis menambahkan Redux DevTools Extension dan beberapa *middleware* Redux yang umum.

#### b. Sediakan Store ke Aplikasi React (misalnya `src/index.js`)

```javascript
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import { store } from './app/store';
import { Provider } from 'react-redux'; // Import Provider

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <Provider store={store}> {/* Bungkus App dengan Provider dan berikan store */}
      <App />
    </Provider>
  </React.StrictMode>
);
```

### 3. Slices dan Reducers

Redux Toolkit memperkenalkan konsep "slice". Sebuah *slice* adalah kumpulan logika Redux yang mencakup *state*, *reducer*, dan *actions* untuk satu fitur aplikasi. `createSlice` adalah fungsi inti yang menyederhanakan pembuatan *slice*.

**Contoh Membuat Slice (misalnya `src/features/counter/counterSlice.js`)**

```javascript
// src/features/counter/counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  value: 0,
};

export const counterSlice = createSlice({
  name: 'counter', // Nama slice, digunakan sebagai prefix untuk action types
  initialState,
  reducers: {
    // Reducer untuk increment
    increment: (state) => {
      // Redux Toolkit menggunakan Immer, jadi kita bisa "memutasi" state secara langsung
      // di dalam reducer, dan Immer akan menghasilkan state baru yang immutable.
      state.value += 1;
    },
    // Reducer untuk decrement
    decrement: (state) => {
      state.value -= 1;
    },
    // Reducer dengan payload
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    },
  },
});

// Action creators dihasilkan untuk setiap fungsi reducer yang kita definisikan
export const { increment, decrement, incrementByAmount } = counterSlice.actions;

// Reducer utama untuk slice ini
export default counterSlice.reducer;
```

**Menggunakan State dan Dispatch Actions di Komponen React:**

```jsx
// src/App.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux'; // Hooks dari react-redux
import { increment, decrement, incrementByAmount } from './features/counter/counterSlice';

function App() {
  // Mengakses state dari store
  const count = useSelector((state) => state.counter.value);
  // Mendapatkan fungsi dispatch untuk mengirim actions
  const dispatch = useDispatch();

  return (
    <div style={{ textAlign: 'center', marginTop: '50px' }}>
      <h1>Counter: {count}</h1>
      <button onClick={() => dispatch(increment())} style={{ margin: '5px' }}>
        Increment
      </button>
      <button onClick={() => dispatch(decrement())} style={{ margin: '5px' }}>
        Decrement
      </button>
      <button onClick={() => dispatch(incrementByAmount(5))} style={{ margin: '5px' }}>
        Increment by 5
      </button>
    </div>
  );
}

export default App;
```

### 4. AsyncThunk untuk Async Operations

Redux Toolkit menyediakan `createAsyncThunk` untuk menangani operasi asinkron (seperti panggilan API) dengan cara yang terstruktur. Ini secara otomatis menghasilkan *action types* (pending, fulfilled, rejected) dan menangani *dispatch* *action* tersebut.

**Contoh `createAsyncThunk` (misalnya `src/features/users/usersSlice.js`)**

```javascript
// src/features/users/usersSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Definisikan async thunk
export const fetchUsers = createAsyncThunk(
  'users/fetchUsers', // Nama action type
  async () => {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    const data = await response.json();
    return data; // Nilai yang dikembalikan akan menjadi payload dari action fulfilled
  }
);

const usersSlice = createSlice({
  name: 'users',
  initialState: {
    list: [],
    status: 'idle', // 'idle' | 'loading' | 'succeeded' | 'failed'
    error: null,
  },
  reducers: {}, // Reducer sinkron (jika ada)
  extraReducers: (builder) => {
    // Menangani action yang dihasilkan oleh createAsyncThunk
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.list = action.payload; // Simpan data pengguna yang diambil
      })
      .addCase(fetchUsers.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message; // Simpan pesan error
      });
  },
});

export default usersSlice.reducer;
```

**Tambahkan `usersReducer` ke `store.js`:**

```javascript
// src/app/store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from '../features/counter/counterSlice';
import usersReducer from '../features/users/usersSlice'; // Import usersReducer

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    users: usersReducer, // Tambahkan usersReducer
  },
});
```

**Menggunakan `fetchUsers` di Komponen React:**

```jsx
// src/App.js (atau komponen lain)
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchUsers } from './features/users/usersSlice'; // Import async thunk

function UserList() {
  const users = useSelector((state) => state.users.list);
  const status = useSelector((state) => state.users.status);
  const error = useSelector((state) => state.users.error);
  const dispatch = useDispatch();

  useEffect(() => {
    // Dispatch async thunk saat komponen pertama kali di-mount
    if (status === 'idle') {
      dispatch(fetchUsers());
    }
  }, [status, dispatch]);

  if (status === 'loading') {
    return <div>Memuat pengguna...</div>;
  }

  if (status === 'failed') {
    return <div>Error: {error}</div>;
  }

  return (
    <div style={{ textAlign: 'center', marginTop: '20px' }}>
      <h2>Daftar Pengguna</h2>
      <ul>
        {users.map((user) => (
          <li key={user.id}>{user.name} ({user.email})</li>
        ))}
      </ul>
    </div>
  );
}

export default UserList;
```

### 5. Redux DevTools

Redux DevTools Extension adalah alat yang sangat berguna untuk *debugging* aplikasi Redux Anda. Ini memungkinkan Anda untuk:
*   Melihat setiap *action* yang di-*dispatch*.
*   Melihat perubahan *state* setelah setiap *action*.
*   "Time-travel debugging": Kembali ke *state* sebelumnya.
*   Mengulang *action*.

**Instalasi:**
1.  Instal ekstensi Redux DevTools di *browser* Anda (Chrome, Firefox, Edge).
2.  Jika Anda menggunakan `configureStore` dari Redux Toolkit, Redux DevTools sudah terintegrasi secara otomatis dan tidak memerlukan konfigurasi tambahan di kode Anda.

**Cara Menggunakan:**
Buka *Developer Tools* di *browser* Anda (biasanya F12), dan cari tab "Redux". Di sana Anda akan melihat semua *action* dan *state* aplikasi Anda.

Dengan menguasai Redux Toolkit, Anda akan memiliki alat yang kuat untuk mengelola *state* aplikasi React yang kompleks secara terstruktur dan efisien, terutama untuk aplikasi berskala besar.