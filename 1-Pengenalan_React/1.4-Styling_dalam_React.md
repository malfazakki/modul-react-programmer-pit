# BAB 1.4: Styling dalam React

## Tujuan Pembelajaran

*   Memahami penggunaan *Inline Styles* dalam React.
*   Memahami penggunaan *CSS Classes* dan `className`.
*   Memahami dasar-dasar *CSS Modules*.
*   Memahami konsep *Responsive Design* dalam React.

## Materi Pembelajaran

### 1. Inline Styles dalam React

*Inline styles* adalah cara paling dasar untuk menerapkan *styling* di React. Anda menulis properti CSS sebagai objek JavaScript langsung di dalam elemen JSX.

**Aturan Penting:**
*   Properti CSS yang biasanya menggunakan tanda hubung (misalnya `background-color`) harus ditulis dalam *camelCase* (misalnya `backgroundColor`).
*   Nilai properti harus berupa *string* (untuk unit seperti `px`, `em`, `rem`) atau *number* (untuk properti tanpa unit seperti `opacity`).

**Contoh Inline Styles:**

```jsx
// App.jsx
import React from 'react';

function App() {
  const headerStyle = {
    backgroundColor: '#282c34',
    color: 'white',
    padding: '20px',
    textAlign: 'center',
    borderRadius: '8px'
  };

  const paragraphStyle = {
    fontSize: '16px',
    lineHeight: '1.5',
    color: '#333',
    margin: '10px 0'
  };

  return (
    <div style={{ fontFamily: 'Arial, sans-serif', maxWidth: '800px', margin: '20px auto' }}>
      <header style={headerStyle}>
        <h1>Selamat Datang di Aplikasi React</h1>
      </header>
      <p style={paragraphStyle}>
        Ini adalah contoh penggunaan <strong>inline styles</strong> dalam React.
        Setiap properti CSS ditulis sebagai pasangan kunci-nilai dalam objek JavaScript.
      </p>
      <button style={{
        backgroundColor: '#61dafb',
        color: 'white',
        border: 'none',
        padding: '10px 20px',
        borderRadius: '5px',
        cursor: 'pointer',
        fontSize: '1em'
      }}>
        Klik Saya
      </button>
    </div>
  );
}

export default App;
```

**Kelebihan:**
*   Sangat mudah untuk memulai.
*   *Scope* terbatas pada elemen tertentu, menghindari konflik nama.
*   Dapat menggunakan variabel JavaScript untuk nilai *style* yang dinamis.

**Kekurangan:**
*   Tidak mendukung *pseudo-classes* (seperti `:hover`, `:active`) atau *media queries* secara langsung.
*   Tidak ideal untuk *styling* yang kompleks atau berulang.
*   Membuat JSX terlihat berantakan jika terlalu banyak *style*.
*   Tidak ada *caching* CSS oleh *browser*.

### 2. CSS Classes dan className

Ini adalah metode *styling* yang paling tradisional dan umum digunakan, mirip dengan cara Anda me-*style* HTML biasa. Anda menulis aturan CSS dalam file `.css` terpisah dan menerapkannya ke elemen JSX menggunakan atribut `className`.

**Contoh CSS Classes:**

```css
/* App.css */
.container {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  max-width: 960px;
  margin: 30px auto;
  padding: 20px;
  border: 1px solid #ddd;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.title {
  color: #333;
  text-align: center;
  margin-bottom: 20px;
}

.button {
  background-color: #007bff;
  color: white;
  padding: 12px 25px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1em;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: #0056b3;
}

.highlight {
  color: #e44d26;
  font-weight: bold;
}
```

```jsx
// App.jsx
import React from 'react';
import './App.css'; // Import file CSS

function App() {
  const isHighlighted = true;

  return (
    <div className="container">
      <h1 className="title">Styling dengan <span className="highlight">CSS Classes</span></h1>
      <p>
        Ini adalah cara paling umum untuk me-style komponen React.
        Kita mengimpor file CSS dan menggunakan atribut <code className="highlight">className</code>.
      </p>
      <button className="button">Pelajari Lebih Lanjut</button>
    </div>
  );
}

export default App;
```

**Kelebihan:**
*   Familiar bagi pengembang web.
*   Mendukung semua fitur CSS (pseudo-classes, media queries, keyframes).
*   *Browser caching* CSS.
*   Baik untuk *global styles*.

**Kekurangan:**
*   Potensi konflik nama kelas global jika tidak diatur dengan baik (misalnya, menggunakan BEM atau metodologi lain).
*   Sulit untuk mengelola *scope* CSS dalam aplikasi besar.

### 3. CSS Modules Introduction

*CSS Modules* adalah solusi untuk masalah *scoping* CSS global. Dengan *CSS Modules*, setiap nama kelas CSS secara otomatis di-*scope* secara lokal ke komponen tempat ia diimpor. Ini berarti Anda dapat menggunakan nama kelas yang sederhana tanpa khawatir tentang konflik.

**Cara Kerja:**
Ketika Anda mengimpor file CSS sebagai *CSS Module* (misalnya `import styles from './MyComponent.module.css';`), *build tool* akan mengubah nama kelas CSS menjadi nama unik (misalnya `MyComponent_button__abc12`).

**Contoh CSS Modules:**

```css
/* Button.module.css */
.button {
  background-color: #4CAF50; /* Green */
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 8px;
}

.button:hover {
  background-color: #45a049;
}

.primary {
  background-color: #008CBA; /* Blue */
}

.primary:hover {
  background-color: #007bb5;
}
```

```jsx
// MyButton.jsx
import React from 'react';
import styles from './Button.module.css'; // Import sebagai CSS Module

function MyButton({ type, children, onClick }) {
  // Menggabungkan kelas default dengan kelas berdasarkan type
  const buttonClass = `${styles.button} ${type === 'primary' ? styles.primary : ''}`;

  return (
    <button className={buttonClass} onClick={onClick}>
      {children}
    </button>
  );
}

export default MyButton;
```

```jsx
// App.jsx
import React from 'react';
import MyButton from './MyButton';

function App() {
  return (
    <div>
      <h1>Styling dengan CSS Modules</h1>
      <MyButton onClick={() => alert('Default Button Clicked!')}>
        Default Button
      </MyButton>
      <MyButton type="primary" onClick={() => alert('Primary Button Clicked!')}>
        Primary Button
      </MyButton>
      <p>
        Nama kelas CSS di-hash secara otomatis untuk mencegah konflik nama.
        Lihat inspeksi elemen di browser untuk melihat nama kelas yang dihasilkan.
      </p>
    </div>
  );
}

export default App;
```

**Kelebihan:**
*   Mencegah konflik nama kelas secara otomatis.
*   Membuat CSS lebih modular dan mudah dikelola.
*   Tetap menggunakan sintaks CSS standar.

**Kekurangan:**
*   Membutuhkan *build configuration* (biasanya sudah ada di CRA/Vite).
*   Sedikit *boilerplate* saat mengimpor dan menggunakan kelas.

### 4. Dynamic Styling dengan Props

Meskipun *inline styles* dan *CSS Modules* memiliki keterbatasan dalam hal *styling* dinamis, Anda masih dapat mencapainya dengan beberapa teknik:

#### a. Dynamic Styling dengan Inline Styles

```jsx
// DynamicButton.jsx
import React from 'react';

function DynamicButton({ variant, size, children, onClick }) {
  const getButtonStyle = () => {
    const baseStyle = {
      border: 'none',
      cursor: 'pointer',
      borderRadius: '4px',
      transition: 'all 0.3s ease'
    };

    // Variants
    const variants = {
      primary: { backgroundColor: '#007bff', color: 'white' },
      secondary: { backgroundColor: '#6c757d', color: 'white' },
      danger: { backgroundColor: '#dc3545', color: 'white' },
      light: { backgroundColor: '#f8f9fa', color: '#212529', border: '1px solid #dee2e6' }
    };

    // Sizes
    const sizes = {
      small: { padding: '6px 12px', fontSize: '0.875rem' },
      medium: { padding: '8px 16px', fontSize: '1rem' },
      large: { padding: '12px 24px', fontSize: '1.25rem' }
    };

    return {
      ...baseStyle,
      ...variants[variant],
      ...sizes[size]
    };
  };

  return (
    <button style={getButtonStyle()} onClick={onClick}>
      {children}
    </button>
  );
}

export default DynamicButton;
```

#### b. Dynamic Styling dengan CSS Modules

```jsx
// DynamicCard.module.css
.card {
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
}

.title {
  margin-top: 0;
  font-size: 1.25rem;
}

.content {
  color: #666;
}

.status {
  display: inline-block;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: bold;
  text-transform: uppercase;
}

.success {
  background-color: #d4edda;
  color: #155724;
}

.warning {
  background-color: #fff3cd;
  color: #856404;
}

.error {
  background-color: #f8d7da;
  color: #721c24;
}
```

```jsx
// DynamicCard.jsx
import React from 'react';
import styles from './DynamicCard.module.css';

function DynamicCard({ title, content, status }) {
  const getStatusClass = () => {
    switch (status) {
      case 'success':
        return styles.success;
      case 'warning':
        return styles.warning;
      case 'error':
        return styles.error;
      default:
        return '';
    }
  };

  return (
    <div className={styles.card}>
      <h3 className={styles.title}>{title}</h3>
      <p className={styles.content}>{content}</p>
      <span className={`${styles.status} ${getStatusClass()}`}>{status}</span>
    </div>
  );
}

export default DynamicCard;
```

### 5. Responsive Design dalam React

Membangun aplikasi yang responsif berarti memastikan aplikasi terlihat dan berfungsi dengan baik di berbagai ukuran layar (desktop, tablet, *mobile*). Di React, Anda dapat mencapai *responsive design* dengan beberapa cara:

#### a. Menggunakan Media Queries dengan CSS Tradisional atau CSS Modules
Ini adalah cara paling umum dan standar untuk *responsive design*. Anda menulis *media queries* di file CSS Anda.

```css
/* App.css atau Component.module.css */
.container {
  padding: 10px;
}

@media (min-width: 768px) { /* Untuk layar tablet dan desktop */
  .container {
    padding: 20px;
  }
}

.responsiveImage {
  max-width: 100%;
  height: auto;
  display: block;
}
```

#### b. Menggunakan Library UI Frameworks
Banyak *UI frameworks* seperti Bootstrap, Material-UI (MUI), Ant Design, atau Chakra UI menyediakan komponen yang sudah responsif *out-of-the-box* dan sistem *grid* yang membantu dalam *responsive layout*.

#### c. Menggunakan Hooks Kustom untuk Ukuran Layar
Anda bisa membuat *custom hook* untuk melacak ukuran layar dan kemudian menggunakan *state* tersebut untuk me-*render* komponen atau *style* yang berbeda secara kondisional.

```jsx
import React, { useState, useEffect } from 'react';

function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return width;
}

function App() {
  const width = useWindowWidth();
  const isMobile = width < 768;

  return (
    <div>
      <h1>Ukuran Layar Anda: {width}px</h1>
      {isMobile ? (
        <p>Anda sedang melihat di perangkat mobile.</p>
      ) : (
        <p>Anda sedang melihat di perangkat desktop/tablet.</p>
      )}
      <div style={{
        backgroundColor: isMobile ? 'orange' : 'purple',
        color: 'white',
        padding: '20px',
        textAlign: 'center'
      }}>
        Konten ini berubah berdasarkan lebar layar.
      </div>
    </div>
  );
}

export default App;
```

Memilih metode *styling* yang tepat tergantung pada preferensi pribadi, ukuran proyek, dan kebutuhan spesifik. Seringkali, kombinasi dari beberapa metode ini digunakan dalam satu proyek.

---

## Rangkuman

### Apa yang Telah Dipelajari

Pada modul **BAB 1.4: Styling dalam React**, kita telah mempelajari berbagai pendekatan untuk menerapkan styling pada komponen React:

#### 1. **Inline Styles dalam React**
- **Implementasi**: Menggunakan objek JavaScript dengan properti camelCase
- **Aturan**: Properti CSS dengan tanda hubung menjadi camelCase (`background-color` → `backgroundColor`)
- **Kelebihan**: Mudah dimulai, scope terbatas, dapat menggunakan variabel JavaScript
- **Kekurangan**: Tidak mendukung pseudo-classes, media queries, membuat JSX berantakan

#### 2. **CSS Classes dan className**
- **Implementasi**: File CSS terpisah dengan `className` untuk menerapkan kelas
- **Kelebihan**: Familiar, mendukung semua fitur CSS, browser caching
- **Kekurangan**: Potensi konflik nama kelas global, sulit mengelola scope dalam aplikasi besar

#### 3. **CSS Modules Introduction**
- **Konsep**: CSS yang di-scope secara lokal ke komponen
- **Implementasi**: Import sebagai module `import styles from './Component.module.css'`
- **Kelebihan**: Mencegah konflik nama otomatis, CSS modular, sintaks CSS standar
- **Kekurangan**: Membutuhkan build configuration, sedikit boilerplate

#### 4. **Dynamic Styling dengan Props**
- **Dengan Inline Styles**: Menggunakan fungsi untuk menggabungkan objek style berdasarkan props
- **Dengan CSS Modules**: Menggunakan logika JavaScript untuk menentukan kelas yang akan diterapkan
- **Keuntungan**: Memungkinkan variasi komponen dengan styling berbeda tanpa duplikasi kode

#### 5. **Responsive Design dalam React**
- **Media Queries**: Menggunakan CSS tradisional atau CSS Modules
- **UI Frameworks**: Komponen responsif out-of-the-box
- **Custom Hooks**: Melacak ukuran layar untuk conditional rendering

### Poin-Poin Penting

✅ **Inline styles** - objek JavaScript dengan properti camelCase untuk styling sederhana  
✅ **CSS Classes** - pendekatan tradisional dengan `className` dan file CSS terpisah  
✅ **CSS Modules** - mencegah konflik nama dengan scoping lokal otomatis  
✅ **Dynamic Styling** - menggunakan props untuk variasi komponen dengan inline styles atau CSS modules  
✅ **Responsive Design** - media queries dan custom hooks untuk berbagai ukuran layar  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari keempat:

1. **Menerapkan Inline Styles**
   - Buatlah komponen React sederhana (misal: `ProfileBox`) yang menggunakan *inline styles* untuk mengatur tampilan (warna, padding, border, dsb).
   - Tampilkan minimal dua elemen dengan style berbeda menggunakan objek style di JSX.

2. **Menggunakan CSS Classes dan className**
   - Buatlah file CSS terpisah dan terapkan beberapa kelas CSS pada komponen React menggunakan atribut `className`.
   - Gunakan minimal dua kelas berbeda pada elemen yang berbeda di dalam komponen.

3. **Menerapkan CSS Modules**
   - Buatlah komponen (misal: `Button`) yang menggunakan CSS Modules untuk styling.
   - Tampilkan dua tombol dengan style berbeda (misal: default dan primary) menggunakan CSS Modules.

4. **Membuat Komponen dengan Dynamic Styling**
   - Buatlah komponen (misal: `AlertBox`) yang menerima props untuk menentukan styling yang berbeda.
   - Implementasikan menggunakan salah satu metode: inline styles dinamis atau CSS Modules dengan logika kondisional.
   - Tampilkan minimal tiga variasi komponen dengan style berbeda (misal: success, warning, dan error).

5. **Membuat Komponen Responsif**
   - Buatlah komponen yang responsif menggunakan salah satu metode berikut: media queries di CSS, media queries di CSS modules, atau custom hook untuk mendeteksi ukuran layar.
   - Tampilkan perubahan tampilan (misal: warna, padding, atau layout) saat ukuran layar berubah (mobile vs desktop).

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil kode (screenshot atau link repository) pada setiap tugas yang membutuhkan implementasi React.