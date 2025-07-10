# BAB 2.1: Event Handling dan Forms

## Tujuan Pembelajaran:

*   Memahami `SyntheticEvent` dalam React.
*   Mampu mengimplementasikan *Controlled Components*.
*   Mampu menangani *Form Handling*.
*   Memahami perbedaan antara *Controlled* dan *Uncontrolled Components*.
*   Mampu membuat formulir dengan *multiple inputs*.
*   Mampu melakukan validasi input sederhana.

## Materi Pembelajaran:

### 1. Event Handling dalam React

*Event handling* di React sangat mirip dengan *event handling* di HTML DOM, tetapi dengan beberapa perbedaan sintaksis:
*   *Event* di React diberi nama dengan *camelCase*, bukan *lowercase*. Misalnya, `onClick` di React, bukan `onclick` di HTML.
*   Anda meneruskan fungsi sebagai *event handler*, bukan *string*.

**Contoh Event Handling:**

```jsx
import React from 'react';

function ButtonClicker() {
  const handleClick = () => {
    alert('Tombol diklik!');
  };

  const handleMouseEnter = () => {
    console.log('Mouse masuk ke area tombol!');
  };

  const handleMouseLeave = () => {
    console.log('Mouse keluar dari area tombol!');
  };

  return (
    <div>
      <h2>Event Handling Sederhana</h2>
      <button
        onClick={handleClick}
        onMouseEnter={handleMouseEnter}
        onMouseLeave={handleMouseLeave}
        style={{ padding: '10px 20px', fontSize: '1em', cursor: 'pointer' }}
      >
        Klik atau Arahkan Mouse ke Saya
      </button>
    </div>
  );
}

export default ButtonClicker;
```

### 2. SyntheticEvent Objects

React membungkus *native browser events* ke dalam objek `SyntheticEvent`. Objek ini memiliki antarmuka yang sama dengan *native browser event*, termasuk metode seperti `stopPropagation()` dan `preventDefault()`. Ini memastikan bahwa *event* berperilaku konsisten di berbagai *browser*.

Anda dapat mengakses objek `SyntheticEvent` ini sebagai argumen pertama yang diteruskan ke *event handler* Anda.

**Contoh Penggunaan SyntheticEvent:**

```jsx
import React from 'react';

function LinkPreventer() {
  const handleClick = (event) => {
    // Mencegah perilaku default dari link (yaitu, navigasi ke URL)
    event.preventDefault();
    console.log('Link diklik, tapi navigasi dicegah!');
    alert('Anda mencoba mengklik link, tapi saya mencegahnya!');
  };

  const handleInputChange = (event) => {
    // event.target mengacu pada elemen DOM yang memicu event
    console.log('Nilai input:', event.target.value);
  };

  return (
    <div>
      <h2>SyntheticEvent Objects</h2>
      <a href="https://www.google.com" onClick={handleClick}>
        Klik Saya (Navigasi Dicegah)
      </a>
      <br /><br />
      <input type="text" onChange={handleInputChange} placeholder="Ketik sesuatu..." />
    </div>
  );
}

export default LinkPreventer;
```

### 3. Controlled vs Uncontrolled Components

Dalam React, ada dua cara utama untuk mengelola formulir: *Controlled Components* dan *Uncontrolled Components*.

#### a. Controlled Components

Dalam *Controlled Components*, data formulir ditangani oleh komponen React. Setiap perubahan pada elemen formulir (seperti `input`, `textarea`, `select`) akan memperbarui *state* komponen, dan *state* tersebut kemudian mengontrol nilai elemen formulir. Ini adalah pendekatan yang direkomendasikan di React.

**Karakteristik:**
*   Nilai input selalu didorong oleh *state* React.
*   Perubahan input ditangani oleh *event handler* (`onChange`) yang memperbarui *state*.
*   Memungkinkan validasi *real-time* dan manipulasi input yang lebih mudah.

**Contoh Controlled Component:**

```jsx
import React, { useState } from 'react';

function NameForm() {
  const [name, setName] = useState('');

  const handleChange = (event) => {
    setName(event.target.value); // Memperbarui state setiap kali input berubah
  };

  const handleSubmit = (event) => {
    event.preventDefault(); // Mencegah refresh halaman
    alert('Nama yang disubmit: ' + name);
    setName(''); // Mengosongkan input setelah submit
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Controlled Component</h2>
      <label>
        Nama:
        <input type="text" value={name} onChange={handleChange} />
      </label>
      <p>Anda mengetik: {name}</p>
      <button type="submit">Submit</button>
    </form>
  );
}

export default NameForm;
```

#### b. Uncontrolled Components

Dalam *Uncontrolled Components*, data formulir ditangani oleh DOM itu sendiri, mirip dengan HTML tradisional. Anda menggunakan *ref* untuk mengakses nilai formulir langsung dari DOM saat Anda membutuhkannya (misalnya, saat formulir disubmit).

**Karakteristik:**
*   Nilai input tidak dikelola oleh *state* React.
*   Anda mengakses nilai input langsung dari DOM menggunakan *ref*.
*   Lebih cepat untuk *form* yang sangat sederhana atau jika Anda perlu mengintegrasikan dengan *library* DOM non-React.

**Contoh Uncontrolled Component:**

```jsx
import React, { useRef } from 'react';

function UncontrolledNameForm() {
  const nameInputRef = useRef(null); // Membuat ref

  const handleSubmit = (event) => {
    event.preventDefault();
    // Mengakses nilai input melalui ref
    alert('Nama yang disubmit (Uncontrolled): ' + nameInputRef.current.value);
    nameInputRef.current.value = ''; // Mengosongkan input secara manual
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Uncontrolled Component</h2>
      <label>
        Nama:
        <input type="text" ref={nameInputRef} defaultValue="John Doe" /> {/* Menggunakan ref */}
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default UncontrolledNameForm;
```

**Kapan Menggunakan yang Mana?**
*   **Controlled Components** adalah pilihan yang lebih disukai untuk sebagian besar kasus karena memberikan kontrol penuh atas data formulir dan memungkinkan validasi *real-time*.
*   **Uncontrolled Components** dapat digunakan untuk formulir yang sangat sederhana atau ketika Anda perlu mengintegrasikan dengan *library* DOM pihak ketiga yang tidak dikelola oleh React.

### 4. Form Submission

Penanganan *form submission* di React melibatkan pencegahan perilaku *default browser* (yaitu, me-*refresh* halaman) dan kemudian memproses data formulir.

**Langkah-langkah:**
1.  Tambahkan *event handler* `onSubmit` ke elemen `<form>`.
2.  Di dalam *event handler*, panggil `event.preventDefault()` untuk menghentikan *refresh* halaman.
3.  Akses nilai input (dari *state* jika *controlled*, atau dari *ref* jika *uncontrolled*).
4.  Lakukan sesuatu dengan data formulir (misalnya, kirim ke *server*, tampilkan di UI).

**Contoh Form Submission (dengan Controlled Component):**

```jsx
import React, { useState } from 'react';

function RegistrationForm() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (event) => {
    event.preventDefault(); // Mencegah refresh halaman
    console.log('Username:', username);
    console.log('Password:', password);
    alert(`Pendaftaran berhasil untuk username: ${username}`);
    // Di sini Anda bisa mengirim data ke API
    setUsername('');
    setPassword('');
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Formulir Pendaftaran</h2>
      <div>
        <label>
          Username:
          <input
            type="text"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
          />
        </label>
      </div>
      <div>
        <label>
          Password:
          <input
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
        </label>
      </div>
      <button type="submit">Daftar</button>
    </form>
  );
}

export default RegistrationForm;
```

### 5. Input Validation Sederhana

Validasi input adalah proses memastikan bahwa data yang dimasukkan pengguna memenuhi kriteria tertentu. Dengan *Controlled Components*, Anda dapat melakukan validasi *real-time* saat pengguna mengetik.

**Contoh Input Validation Sederhana:**

```jsx
import React, { useState } from 'react';

function ValidationForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [emailError, setEmailError] = useState('');
  const [passwordError, setPasswordError] = useState('');

  const validateEmail = (email) => {
    if (!email.includes('@')) {
      setEmailError('Email harus mengandung karakter "@"');
    } else {
      setEmailError('');
    }
  };

  const validatePassword = (password) => {
    if (password.length < 6) {
      setPasswordError('Password minimal 6 karakter');
    } else {
      setPasswordError('');
    }
  };

  const handleEmailChange = (e) => {
    const newEmail = e.target.value;
    setEmail(newEmail);
    validateEmail(newEmail); // Validasi real-time
  };

  const handlePasswordChange = (e) => {
    const newPassword = e.target.value;
    setPassword(newPassword);
    validatePassword(newPassword); // Validasi real-time
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    // Validasi akhir sebelum submit
    validateEmail(email);
    validatePassword(password);

    if (!emailError && !passwordError && email && password) {
      alert('Formulir berhasil disubmit!');
      console.log({ email, password });
      // Lakukan sesuatu dengan data
    } else {
      alert('Mohon perbaiki kesalahan dalam formulir.');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Formulir dengan Validasi</h2>
      <div>
        <label>
          Email:
          <input
            type="text"
            value={email}
            onChange={handleEmailChange}
          />
        </label>
        {emailError && <p style={{ color: 'red', fontSize: '0.8em' }}>{emailError}</p>}
      </div>
      <div>
        <label>
          Password:
          <input
            type="password"
            value={password}
            onChange={handlePasswordChange}
          />
        </label>
        {passwordError && <p style={{ color: 'red', fontSize: '0.8em' }}>{passwordError}</p>}
      </div>
      <button type="submit">Login</button>
    </form>
  );
}

export default ValidationForm;
```

Dengan menguasai *event handling* dan manajemen formulir, Anda akan dapat membangun aplikasi React yang sepenuhnya interaktif dan responsif terhadap masukan pengguna.

---

## Rangkuman

### Apa yang Telah Dipelajari

Pada modul **BAB 2.1: Event Handling dan Forms**, kita telah mempelajari dasar-dasar penanganan event dan formulir di React:

#### 1. **Event Handling dalam React**
- Event di React menggunakan camelCase (`onClick`, `onChange`)
- Handler berupa fungsi, bukan string
- Mendukung berbagai event DOM (klik, mouse, input, dsb)

#### 2. **SyntheticEvent Objects**
- React membungkus event browser menjadi objek SyntheticEvent
- Konsistensi perilaku event di semua browser
- Dapat menggunakan method seperti `preventDefault()` dan `stopPropagation()`

#### 3. **Controlled vs Uncontrolled Components**
- **Controlled**: Nilai input dikontrol oleh state React, perubahan melalui handler
- **Uncontrolled**: Nilai input dikelola DOM, akses via ref
- Controlled lebih direkomendasikan untuk validasi dan kontrol penuh

#### 4. **Form Submission**
- Penanganan submit dengan `onSubmit` dan `event.preventDefault()`
- Data diakses dari state (controlled) atau ref (uncontrolled)
- Proses data sebelum/ketika submit

#### 5. **Input Validation Sederhana**
- Validasi real-time pada input (misal: email harus mengandung "@", password minimal 6 karakter)
- Menampilkan pesan error jika validasi gagal
- Validasi sebelum submit untuk memastikan data yang benar  

### Poin-Poin Penting

✅ **Event handling di React** - camelCase, handler fungsi, konsisten di semua browser  
✅ **SyntheticEvent** - membungkus event native, method standar tersedia  
✅ **Controlled component** - input dikontrol state, validasi mudah  
✅ **Uncontrolled component** - input dikelola DOM, akses via ref  
✅ **Validasi input** - real-time dan sebelum submit untuk data yang benar  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari ketujuh:

1. **Membuat Event Handler Sederhana**
   - Buatlah komponen React yang memiliki tombol dan menangani event klik (`onClick`) serta event mouse lainnya (misal: `onMouseEnter`, `onMouseLeave`).
   - Tampilkan pesan atau perubahan tampilan saat event terjadi.

2. **Menggunakan SyntheticEvent**
   - Buatlah komponen yang memiliki link dan input teks.
   - Tangani event klik pada link dengan `preventDefault()` dan tampilkan pesan di konsol.
   - Tangani event perubahan pada input dan tampilkan nilai input secara real-time.

3. **Membuat Controlled Component**
   - Buatlah formulir sederhana (misal: input nama) yang dikelola dengan state React (controlled component).
   - Tampilkan data yang sedang diinput secara real-time dan proses data saat form disubmit.

4. **Membuat Uncontrolled Component**
   - Buatlah formulir sederhana yang menggunakan ref untuk mengambil nilai input (uncontrolled component).
   - Tampilkan nilai input saat form disubmit.

5. **Membuat Formulir dengan Multiple Inputs dan Validasi Sederhana**
   - Buatlah formulir pendaftaran dengan beberapa input (misal: username, email, password).
   - Lakukan validasi sederhana (misal: email harus mengandung “@”, password minimal 6 karakter).
   - Tampilkan pesan error jika validasi gagal dan tampilkan data jika validasi berhasil.

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.