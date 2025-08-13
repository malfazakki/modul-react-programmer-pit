# BAB 2.5: State Management Lanjutan

## Tujuan Pembelajaran

*   Memahami konsep *State Lifting*.
*   Memahami masalah *Prop Drilling*.
*   Mampu berbagi *state* antar komponen secara efektif.
*   Memahami pengenalan `useReducer` Hook.
*   Mampu mengidentifikasi dan menerapkan pola *state management* yang kompleks.

## Materi Pembelajaran

### 1. Lifting State Up

*Lifting state up* adalah pola di mana *state* yang awalnya dimiliki oleh beberapa komponen anak dipindahkan ke komponen induk terdekat yang sama-sama membutuhkan *state* tersebut. Ini memungkinkan komponen induk untuk menjadi "sumber kebenaran" untuk *state* tersebut dan meneruskannya ke komponen anak melalui *props*.

**Kapan Menggunakan Lifting State Up?**
*   Ketika dua atau lebih komponen anak perlu berbagi *state* yang sama.
*   Ketika komponen induk perlu mengkoordinasikan *state* dari beberapa komponen anak.

**Contoh Lifting State Up:**

Misalkan kita memiliki dua komponen input suhu, satu untuk Celsius dan satu untuk Fahrenheit, dan kita ingin keduanya selalu sinkron.

```jsx
import React, { useState } from 'react';

// Komponen anak: Input Suhu
function TemperatureInput({ temperature, onTemperatureChange, scale }) {
  const scaleNames = {
    c: 'Celsius',
    f: 'Fahrenheit'
  };

  return (
    <fieldset>
      <legend>Masukkan suhu dalam {scaleNames[scale]}:</legend>
      <input
        value={temperature}
        onChange={(e) => onTemperatureChange(e.target.value, scale)}
      />
    </fieldset>
  );
}

// Fungsi konversi (di luar komponen untuk reusability)
function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}

function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) {
    return '';
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}

// Komponen Induk: Kalkulator Suhu
function Calculator() {
  const [temperature, setTemperature] = useState('');
  const [scale, setScale] = useState('c');

  const handleCelsiusChange = (temp) => {
    setTemperature(temp);
    setScale('c');
  };

  const handleFahrenheitChange = (temp) => {
    setTemperature(temp);
    setScale('f');
  };

  const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
  const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

  return (
    <div>
      <h2>Kalkulator Suhu (Lifting State Up)</h2>
      <TemperatureInput
        scale="c"
        temperature={celsius}
        onTemperatureChange={handleCelsiusChange}
      />
      <TemperatureInput
        scale="f"
        temperature={fahrenheit}
        onTemperatureChange={handleFahrenheitChange}
      />
      <p>
        Suhu dalam Celsius: {celsius}
      </p>
      <p>
        Suhu dalam Fahrenheit: {fahrenheit}
      </p>
    </div>
  );
}

export default Calculator;
```

### 2. Prop Drilling Problems

*Prop drilling* adalah situasi di mana *props* harus diteruskan melalui beberapa lapisan komponen perantara yang sebenarnya tidak membutuhkan *props* tersebut, hanya untuk mencapai komponen anak yang membutuhkannya.

**Masalah yang Ditimbulkan oleh Prop Drilling:**
*   **Kode Sulit Dibaca dan Dipelihara**: Sulit untuk melacak dari mana *prop* berasal dan ke mana ia pergi.
*   **Refactoring yang Sulit**: Mengubah struktur komponen dapat memerlukan perubahan di banyak file.
*   **Kopling yang Ketat**: Komponen perantara menjadi sangat bergantung pada *props* yang tidak mereka gunakan, menciptakan kopling yang tidak perlu.

**Contoh Prop Drilling:**

```jsx
// App.jsx
function App() {
  const user = { name: 'Alice', theme: 'dark' };
  return <Toolbar user={user} />;
}

// Toolbar.jsx (perantara)
function Toolbar({ user }) {
  return (
    <div>
      <Profile user={user} /> {/* Meneruskan user ke Profile */}
    </div>
  );
}

// Profile.jsx (perantara)
function Profile({ user }) {
  return (
    <div>
      <Avatar user={user} /> {/* Meneruskan user ke Avatar */}
      <ThemeDisplay theme={user.theme} /> {/* Meneruskan theme ke ThemeDisplay */}
    </div>
  );
}

// Avatar.jsx (komponen yang membutuhkan user)
function Avatar({ user }) {
  return <p>Avatar untuk {user.name}</p>;
}

// ThemeDisplay.jsx (komponen yang membutuhkan theme)
function ThemeDisplay({ theme }) {
  return <p>Tema saat ini: {theme}</p>;
}
```
Dalam contoh di atas, `Toolbar` dan `Profile` tidak menggunakan `user` atau `theme` secara langsung, tetapi mereka harus meneruskannya ke bawah. Ini adalah *prop drilling*.

**Solusi untuk Prop Drilling:**
*   **Context API**: Untuk berbagi data yang dianggap "global" untuk pohon komponen (misalnya, tema, informasi pengguna yang diautentikasi).
*   **Component Composition**: Meneruskan komponen sebagai *children* atau *props* alih-alih data mentah.
*   **State Management Libraries**: Seperti Redux, Zustand, Jotai, Recoil untuk *state* yang sangat kompleks dan global.

### 3. State Sharing Between Components

Seperti yang dibahas di *lifting state up*, berbagi *state* antar komponen umumnya dilakukan dengan memindahkan *state* ke leluhur terdekat yang sama-sama membutuhkan *state* tersebut.

**Pola Umum:**
1.  **State di Komponen Induk**: Komponen induk memiliki *state* dan meneruskannya ke komponen anak melalui *props*.
2.  **Fungsi Callback di Komponen Induk**: Komponen induk juga meneruskan fungsi *callback* ke komponen anak melalui *props*.
3.  **Komponen Anak Memanggil Callback**: Ketika komponen anak perlu memperbarui *state* di induk, ia memanggil fungsi *callback* yang diterimanya, meneruskan data yang diperlukan.

Ini menciptakan aliran data satu arah yang jelas, dari atas ke bawah (induk ke anak) melalui *props*, dan dari bawah ke atas (anak ke induk) melalui fungsi *callback*.

### 4. useReducer Hook Introduction

`useReducer` adalah Hook alternatif untuk `useState` yang lebih cocok untuk mengelola *state* yang kompleks atau ketika *state* berikutnya bergantung pada *state* sebelumnya. Ini terinspirasi oleh pola Redux.

`useReducer` menerima dua argumen:
1.  **`reducer` function**: Fungsi yang menerima *state* saat ini dan sebuah `action`, lalu mengembalikan *state* yang baru.
2.  **`initialState`**: Nilai awal dari *state*.

`useReducer` mengembalikan *array* dengan dua elemen:
1.  **`state`**: Variabel *state* saat ini.
2.  **`dispatch` function**: Fungsi yang Anda panggil untuk "mengirim" (`dispatch`) sebuah `action` ke `reducer` untuk memperbarui *state*.

**Sintaks Dasar:**

```jsx
import React, { useReducer } from 'react';

// 1. Fungsi Reducer
// Menerima state saat ini dan sebuah action
// Mengembalikan state yang baru
function counterReducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error();
  }
}

function CounterWithReducer() {
  // 2. Menggunakan useReducer
  // counterState: variabel state saat ini
  // dispatch: fungsi untuk mengirim action
  // counterReducer: fungsi reducer yang akan digunakan
  // { count: 0 }: nilai awal state
  const [counterState, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <h2>Counter dengan useReducer</h2>
      <p>Count: {counterState.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>Tambah</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>Kurang</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}

export default CounterWithReducer;
```

**Kapan Menggunakan `useReducer` daripada `useState`?**
*   Ketika logika *state* kompleks dan melibatkan banyak sub-nilai atau ketika *state* berikutnya bergantung pada *state* sebelumnya.
*   Ketika Anda perlu mengoptimalkan kinerja untuk komponen yang memicu pembaruan mendalam (misalnya, `dispatch` tidak akan memicu *re-render* jika *reducer* mengembalikan *state* yang sama).
*   Ketika Anda ingin memisahkan logika pembaruan *state* dari komponen UI.
*   Ketika Anda perlu berbagi logika *state* yang sama antar komponen (dengan *custom hooks*).

### 5. Complex State Management Patterns

Selain `useState` dan `useReducer`, ada beberapa pola dan *library* yang digunakan untuk mengelola *state* yang lebih kompleks di aplikasi React skala besar.

#### a. Context API

React Context menyediakan cara untuk meneruskan data melalui pohon komponen tanpa harus meneruskan *props* secara manual di setiap level. Ini sangat berguna untuk data yang dianggap "global" untuk pohon komponen, seperti tema, informasi pengguna yang diautentikasi, atau pengaturan bahasa.

**Kapan Menggunakan Context API?**
*   Untuk data yang jarang berubah.
*   Untuk data yang dibutuhkan oleh banyak komponen di berbagai level.
*   Untuk menghindari *prop drilling*.

**Contoh Sederhana Context API:**

```jsx
import React, { createContext, useContext, useState } from 'react';

// 1. Buat Context
const ThemeContext = createContext(null);

// Komponen yang menyediakan nilai Context
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));
  };

  const contextValue = { theme, toggleTheme };

  return (
    <ThemeContext.Provider value={contextValue}>
      {children}
    </ThemeContext.Provider>
  );
}

// Komponen yang mengkonsumsi nilai Context
function ThemedButton() {
  const { theme, toggleTheme } = useContext(ThemeContext); // Menggunakan useContext
  const buttonStyle = {
    backgroundColor: theme === 'light' ? '#eee' : '#333',
    color: theme === 'light' ? '#333' : '#eee',
    padding: '10px 20px',
    border: 'none',
    borderRadius: '5px',
    cursor: 'pointer'
  };

  return (
    <button style={buttonStyle} onClick={toggleTheme}>
      Ganti Tema ({theme})
    </button>
  );
}

function ThemedParagraph() {
  const { theme } = useContext(ThemeContext);
  const paragraphStyle = {
    color: theme === 'light' ? '#000' : '#fff',
    backgroundColor: theme === 'light' ? '#fff' : '#222',
    padding: '10px',
    borderRadius: '5px'
  };
  return (
    <p style={paragraphStyle}>
      Ini adalah paragraf dengan tema {theme}.
    </p>
  );
}

function App() {
  return (
    <ThemeProvider>
      <div style={{ padding: '20px', textAlign: 'center' }}>
        <h2>Contoh Context API</h2>
        <ThemedButton />
        <ThemedParagraph />
      </div>
    </ThemeProvider>
  );
}

export default App;
```

#### b. State Management Libraries (Redux, Zustand, Jotai, Recoil)

Untuk aplikasi yang sangat besar dan kompleks dengan *state* yang dibagi di banyak tempat dan sering diperbarui, *library* manajemen *state* eksternal dapat menjadi solusi yang lebih terstruktur.

*   **Redux**: *Library* manajemen *state* yang paling terkenal, menyediakan *single source of truth* untuk *state* aplikasi Anda. Memiliki kurva pembelajaran yang curam tetapi sangat kuat dan dapat diskalakan.
*   **Zustand, Jotai, Recoil**: Alternatif yang lebih ringan dan modern untuk Redux, seringkali dengan API yang lebih sederhana dan kurang *boilerplate*.

Memilih pola *state management* yang tepat sangat bergantung pada ukuran dan kompleksitas aplikasi Anda. Untuk aplikasi kecil hingga menengah, `useState`, `useReducer`, dan Context API seringkali sudah cukup. Untuk aplikasi yang lebih besar, pertimbangkan *library* manajemen *state* eksternal.

---

## Rangkuman

### Apa yang Telah Dipelajari

Pada modul **BAB 2.5: State Management Lanjutan**, kita telah mempelajari berbagai pola dan teknik manajemen state di React untuk aplikasi yang lebih kompleks:

#### 1. **Lifting State Up**
- Memindahkan state ke komponen induk agar dapat dibagikan ke beberapa komponen anak
- Membuat komponen induk sebagai "sumber kebenaran" untuk state bersama

#### 2. **Prop Drilling Problems**
- Masalah ketika props harus diteruskan melalui banyak komponen perantara
- Menyulitkan pemeliharaan dan refactoring kode
- Solusi: Context API, component composition, atau state management libraries

#### 3. **State Sharing Between Components**
- State diindukkan, fungsi callback diteruskan ke anak
- Aliran data satu arah: props dari induk ke anak, callback dari anak ke induk

#### 4. **useReducer Hook Introduction**
- Alternatif useState untuk state kompleks atau logika update yang rumit
- Menggunakan reducer function dan dispatch action
- Cocok untuk state yang saling bergantung atau banyak sub-nilai

#### 5. **Complex State Management Patterns**
- Context API untuk data global (tema, user, dsb)
- State management libraries (Redux, Zustand, Jotai, Recoil) untuk aplikasi besar
- Pilihan pola tergantung skala dan kebutuhan aplikasi

### Poin-Poin Penting

✅ **Lifting state up** - solusi berbagi state antar komponen  
✅ **Prop drilling** - waspadai masalah dan gunakan solusi yang tepat  
✅ **useReducer** - untuk state kompleks dan logika update yang rumit  
✅ **Context API** - berbagi data global tanpa prop drilling  
✅ **State management libraries** - gunakan untuk aplikasi besar dan kompleks  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari kesebelas:

1. **Menerapkan Lifting State Up**
   - Buatlah dua komponen input (misal: dua input angka atau dua input suhu) yang saling terhubung nilainya melalui lifting state up ke komponen induk.
   - Pastikan perubahan pada salah satu input langsung mempengaruhi input lainnya.

2. **Mendemonstrasikan Prop Drilling**
   - Buatlah struktur komponen bertingkat (minimal 3 level) di mana data harus diteruskan dari komponen paling atas ke komponen paling bawah melalui props (prop drilling).
   - Tampilkan data tersebut di komponen paling bawah.

3. **Berbagi State antar Komponen**
   - Buatlah dua komponen saudara (sibling) yang berbagi state melalui komponen induk.
   - Satu komponen dapat mengubah state, dan perubahan tersebut langsung terlihat di komponen saudara lainnya.

4. **Menggunakan useReducer untuk State Kompleks**
   - Buatlah komponen counter yang menggunakan `useReducer` untuk mengelola state (misal: tambah, kurang, reset).
   - Tampilkan nilai counter dan tombol untuk setiap aksi.

5. **Menerapkan Context API Sederhana**
   - Buatlah Context untuk tema (light/dark) dan gunakan Provider untuk membungkus beberapa komponen.
   - Tampilkan tombol untuk mengganti tema dan pastikan tema diterapkan pada beberapa komponen yang berbeda.

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.