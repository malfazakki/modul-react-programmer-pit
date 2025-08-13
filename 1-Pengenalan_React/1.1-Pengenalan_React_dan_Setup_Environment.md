# BAB 1.1: Pengenalan React dan Setup Environment

## Tujuan Pembelajaran

*   Memahami konsep dasar React.js dan mengapa menggunakan React.
*   Memahami perbedaan antara SPA (Single Page Application) dan MPA (Multi Page Application).
*   Memahami konsep Virtual DOM dan proses Reconciliation di React.
*   Mampu menginstal dan mengkonfigurasi environment development React (Node.js, npm/yarn).
*   Memahami perbedaan Create React App dan Vite serta kapan menggunakannya.
*   Memahami struktur folder dan file dalam proyek React.
*   Memahami sintaks JSX dasar dan perbedaannya dengan HTML.

## Materi Pembelajaran

### 1. Apa itu React dan Mengapa Menggunakan React

### Apa itu React?

React.js, atau sering disebut React, adalah sebuah library JavaScript open-source yang digunakan untuk membangun user interface (UI) yang interaktif. React dikembangkan oleh Facebook dan pertama kali diperkenalkan pada tahun 2013. Meskipun sering disebut sebagai framework, secara teknis React adalah sebuah library untuk membangun UI.

### Mengapa Menggunakan React?

Ada beberapa alasan mengapa React menjadi pilihan populer bagi banyak pengembang:

- **Mudah Dipelajari dan Digunakan**: React dirancang agar sederhana dan mudah digunakan, terutama bagi mereka yang sudah familiar dengan JavaScript.
- **Komponen yang Dapat Digunakan Kembali (Reusable Components)**: React memungkinkan pengembang untuk memecah UI menjadi komponen-komponen kecil yang independen dan dapat digunakan kembali di berbagai bagian aplikasi. Setiap komponen memiliki logika dan tampilannya sendiri, membuat kode lebih terstruktur dan mudah dikelola.
- **Performa Tinggi**: React menggunakan konsep Virtual DOM untuk memperbarui tampilan aplikasi secara efisien, yang berkontribusi pada performa tinggi dan pengalaman pengguna yang lebih baik.
- **SEO-Friendly**: Implementasi Virtual DOM dan kemampuan server-side rendering pada React dapat membantu optimisasi mesin pencari (SEO) aplikasi web.
- **Komunitas Besar dan Aktif**: React memiliki komunitas pengembang yang besar dan beragam, menyediakan banyak sumber daya, tool, dan dukungan.

### 2. SPA (Single Page Application) vs MPA (Multi Page Application)

Dalam pengembangan aplikasi web, ada dua arsitektur utama yang sering digunakan: Single Page Application (SPA) dan Multi Page Application (MPA).

### Single Page Application (SPA)

SPA adalah aplikasi web yang memuat satu halaman HTML dan secara dinamis memperbarui kontennya tanpa perlu memuat ulang seluruh halaman saat pengguna berinteraksi dengan aplikasi. SPA sangat mengandalkan framework JavaScript seperti React, Angular, atau Vue untuk mengelola state aplikasi dan UI.

#### Kelebihan SPA:

- **Pengalaman Pengguna yang Lebih Mulus**: SPA menawarkan pengalaman yang lebih interaktif dan responsif karena tidak ada reload halaman penuh.
- **Performa Lebih Cepat (setelah initial load)**: Setelah halaman awal dimuat, interaksi selanjutnya dalam SPA cenderung lebih cepat karena hanya bagian yang diperlukan yang diperbarui.
- **Pengembangan yang Efisien**: Dapat menggunakan kode backend dan frontend yang dapat digunakan kembali.

#### Kekurangan SPA:

- **Waktu Initial Load yang Lebih Lama**: SPA mungkin memiliki overhead initial load yang lebih tinggi karena perlu mengunduh seluruh bundle JavaScript, CSS, dan data awal.
- **Tantangan SEO**: Crawler mesin pencari mungkin kesulitan mengindeks konten dinamis pada SPA dibandingkan dengan halaman statis pada MPA.
- **Kompleksitas Pengembangan**: SPA bisa lebih kompleks untuk dikembangkan karena memerlukan pemahaman yang lebih dalam tentang AJAX dan framework JavaScript.

### Multi Page Application (MPA)

MPA adalah aplikasi web tradisional yang memuat beberapa halaman HTML saat pengguna menavigasi aplikasi. Setiap interaksi pengguna yang memerlukan konten baru akan memicu reload halaman penuh.

#### Kelebihan MPA:

- **SEO yang Lebih Baik**: MPA lebih cocok untuk SEO karena setiap halaman dapat diindeks secara terpisah oleh mesin pencari.
- **Waktu Initial Load yang Lebih Cepat**: Waktu initial load setiap halaman mungkin lebih cepat dibandingkan SPA karena browser hanya perlu memuat konten untuk halaman saat ini.
- **Kompatibilitas**: MPA lebih kompatibel dengan browser lama dan sistem legacy.

#### Kekurangan MPA:

- **Pengalaman Pengguna Kurang Mulus**: Memerlukan reload halaman penuh setiap kali navigasi, yang dapat menyebabkan penundaan.
- **Interaksi Lebih Lambat**: Memuat halaman baru bisa lebih lambat dibandingkan pembaruan dinamis pada SPA.
- **Beban Server**: Setiap permintaan halaman memukul server, berpotensi meningkatkan beban server.

Pilihan antara SPA dan MPA tergantung pada kebutuhan spesifik aplikasi Anda.

### 3. Konsep Virtual DOM dan Reconciliation

### Virtual DOM (VDOM)

Virtual DOM adalah representasi ringan dari DOM (Document Object Model) yang sebenarnya, disimpan dalam memori. React menggunakan VDOM untuk mengoptimalkan pembaruan pada DOM yang sebenarnya.

#### Cara Kerja Virtual DOM:

1. **Render Awal**: Ketika komponen React pertama kali di-render, React membuat representasi VDOM dari DOM yang sebenarnya.
2. **Pembaruan**: Ketika state atau props dari sebuah komponen berubah, React membuat tree VDOM yang baru.
3. **Perbandingan (Diffing)**: React membandingkan tree VDOM yang baru dengan yang sebelumnya untuk mengidentifikasi perubahan. Algoritma diffing ini sangat efisien dalam menemukan perbedaan (node yang ditambahkan, dihapus, atau diubah).
4. **Pembaruan DOM Sebenarnya**: React hanya memperbarui bagian-bagian DOM yang sebenarnya yang telah berubah, meminimalkan jumlah manipulasi DOM. Proses ini membuat pembaruan lebih cepat dan efisien.

### Reconciliation

Reconciliation adalah proses di React yang membantu memperbarui UI secara efisien. Ini adalah mekanisme untuk menyinkronkan dua tree DOM (Virtual DOM dan Real DOM) dengan menemukan perbedaan dan menerapkan perubahan yang diperlukan.

#### Fase Reconciliation:

1. **Render**: React membangun tree Virtual DOM berdasarkan state dan props komponen saat ini.
2. **Reconciliation (Diffing Algorithm)**: React membandingkan Virtual DOM sebelumnya dengan yang baru. Ini mengidentifikasi perbedaan dan membuat daftar pembaruan minimal yang diperlukan untuk menyinkronkan DOM yang sebenarnya dengan Virtual DOM yang diperbarui.
3. **Commit**: Pada fase terakhir, React menerapkan pembaruan yang dihitung ke DOM yang sebenarnya, memastikan UI mencerminkan state komponen saat ini.

Singkatnya, Virtual DOM memungkinkan React untuk mengoptimalkan pembaruan, dan proses reconciliation memastikan bahwa hanya perubahan yang diperlukan yang dilakukan pada DOM yang sebenarnya, menghasilkan aplikasi yang cepat dan responsif.

### 4. Setup Node.js, npm/yarn

Untuk memulai pengembangan React, Anda perlu menginstal Node.js dan package manager seperti npm (Node Package Manager) atau Yarn.

### Instalasi Node.js dan npm

npm datang bersamaan dengan instalasi Node.js.

1. **Unduh Node.js**: Kunjungi situs web resmi Node.js (https://nodejs.org/) dan unduh versi LTS (Long-Term Support) untuk stabilitas maksimum.
2. **Jalankan Installer**: Ikuti petunjuk instalasi.
3. **Verifikasi Instalasi**: Buka terminal atau command prompt dan jalankan perintah berikut untuk memeriksa versi Node.js dan npm:

```bash
node -v
npm -v
```

Anda akan melihat nomor versi jika instalasi berhasil.

### Instalasi Yarn (Opsional, Alternatif untuk npm)

Yarn adalah alternatif untuk npm yang menawarkan instalasi yang lebih cepat dan konsisten.

1. **Instal Yarn menggunakan npm (Direkomendasikan)**: Jika Anda sudah menginstal npm, Anda dapat menginstal Yarn secara global dengan perintah berikut:

```bash
npm install --global yarn
```

2. **Verifikasi Instalasi**:

```bash
yarn -v
```

Anda akan melihat nomor versi jika instalasi berhasil.

### 5. Create React App vs Vite

Create React App (CRA) dan Vite adalah tool populer untuk memulai proyek React. Keduanya memiliki kelebihan dan kekurangan masing-masing.

### Create React App (CRA)

CRA adalah tool resmi yang direkomendasikan oleh tim React untuk memulai proyek baru.

- **Zero-Configuration**: CRA menyediakan setup yang siap pakai tanpa perlu konfigurasi yang rumit, cocok untuk pemula.
- **Ekosistem Matang**: Memiliki dukungan komunitas yang kuat dan ekosistem yang stabil.
- **Build Tool**: Menggunakan Webpack sebagai build tool utamanya.

#### Kelebihan CRA:

- Mudah digunakan untuk pemula dan proyek kecil hingga menengah.
- Dukungan resmi dari Facebook dan tim React.

#### Kekurangan CRA:

- Waktu startup development server yang lebih lambat, terutama untuk proyek besar.
- Hot reloading bisa memakan waktu lebih lama.
- Hanya mendukung React.

### Vite

Vite adalah build tool modern yang menawarkan pengalaman pengembangan yang sangat cepat.

- **Fast Development Server**: Vite memanfaatkan native ES module imports untuk startup development server yang sangat cepat.
- **Efficient HMR (Hot Module Replacement)**: Pembaruan hot reloading di Vite hampir instan.
- **Fleksibel**: Mendukung berbagai framework frontend selain React, seperti Vue dan Svelte.
- **Optimized Builds**: Menghasilkan build produksi yang dioptimalkan dengan tree-shaking dan code-splitting.

#### Kelebihan Vite:

- Performa lebih cepat dalam pengembangan dan build produksi.
- Fleksibilitas konfigurasi yang tinggi.
- Dukungan out-of-the-box untuk TypeScript, JSX, dan CSS.

#### Kekurangan Vite:

- Komunitas dan ekosistem plugin masih berkembang dibandingkan Webpack/CRA.
- Mungkin memerlukan konfigurasi manual untuk code splitting.

### Kapan Menggunakan yang Mana?

- **Gunakan CRA jika**: Anda adalah pemula, menginginkan setup tanpa konfigurasi, dan proyek Anda tidak memiliki persyaratan performa yang sangat tinggi.
- **Gunakan Vite jika**: Kecepatan dan performa sangat penting, Anda menginginkan fleksibilitas konfigurasi, atau Anda bekerja dengan berbagai framework frontend.

### 6. Struktur Folder dan File React

React tidak memiliki aturan baku tentang bagaimana Anda harus menyusun file ke dalam folder. Namun, ada beberapa pendekatan umum yang populer.

### Struktur Default (Create React App/Vite)

Ketika Anda membuat proyek React baru dengan CRA atau Vite, Anda akan mendapatkan struktur dasar seperti ini:

```
my-app/
├── node_modules/
├── public/
│   ├── index.html
│   └── ... (aset publik lainnya)
├── src/
│   ├── App.jsx
│   ├── index.jsx
│   ├── index.css
│   ├── logo.svg
│   └── ... (file sumber lainnya)
├── .gitignore
├── package.json
├── README.md
└── yarn.lock (atau package-lock.json)
```

- **node_modules/**: Berisi semua package dan library yang diinstal.
- **public/**: Berisi file yang akan disajikan secara langsung ke browser, seperti index.html (tempat aplikasi React Anda akan di-render).
- **src/**: Ini adalah folder utama tempat Anda akan menulis sebagian besar kode aplikasi React Anda.

### Pendekatan Umum untuk Mengorganisir Folder dan File

#### 1. Pengelompokan Berdasarkan Fitur atau Rute

Mengelompokkan file (CSS, JS, test) bersama dalam folder yang dikelompokkan berdasarkan fitur atau rute.

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.jsx
│   │   └── Button.css
│   └── Card/
│       ├── Card.jsx
│       └── Card.css
├── features/
│   ├── Auth/
│   │   ├── AuthPage.jsx
│   │   ├── AuthForm.jsx
│   │   └── authSlice.jsx
│   ├── Products/
│   │   ├── ProductList.jsx
│   │   ├── ProductCard.jsx
│   │   └── productSlice.jsx
├── pages/
│   ├── HomePage.jsx
│   ├── AboutPage.jsx
├── App.jsx
├── index.jsx
└── utils/
    └── helpers.jsx
```

- **components/**: Berisi komponen UI yang dapat digunakan kembali di seluruh aplikasi.
- **features/**: Mengelompokkan komponen, state management, dan logika terkait untuk fitur tertentu.
- **pages/**: Berisi komponen yang mewakili halaman-halaman utama aplikasi.
- **utils/**: Berisi fungsi-fungsi helper atau utility yang digunakan di banyak tempat.

#### 2. Pengelompokan Berdasarkan Jenis File

Mengelompokkan file dengan jenis yang sama bersama-sama.

```
src/
├── api/
│   ├── userApi.jsx
│   └── productApi.jsx
├── components/
│   ├── Button.jsx
│   ├── Card.jsx
├── hooks/
│   ├── useAuth.jsx
│   └── useProducts.jsx
├── views/
│   ├── HomeView.jsx
│   └── AboutView.jsx
├── App.jsx
└── index.jsx
```

### Tips Penting:

- **Hindari Nesting Terlalu Dalam**: Terlalu banyak nesting direktori dapat menyulitkan pengelolaan kode dan impor.
- **Jangan Terlalu Memikirkannya di Awal**: Jika Anda baru memulai, jangan menghabiskan terlalu banyak waktu untuk memilih struktur file. Mulailah dengan pendekatan sederhana dan sesuaikan seiring berjalannya proyek.

### 7. JSX Syntax Dasar

JSX (JavaScript XML) adalah ekstensi sintaks JavaScript yang memungkinkan Anda menulis kode seperti HTML langsung di dalam file JavaScript. React menggunakan JSX untuk mendeskripsikan seperti apa UI seharusnya terlihat.

### Mengapa Menggunakan JSX?

- **Intuitif dan Deklaratif**: JSX memungkinkan Anda mendefinisikan UI dengan cara yang lebih intuitif dan deklaratif, mirip dengan HTML.
- **Kekuatan Penuh JavaScript**: Meskipun terlihat seperti HTML, JSX memiliki kekuatan penuh JavaScript, memungkinkan Anda menyematkan ekspresi JavaScript di dalamnya.
- **Pesan Kesalahan yang Lebih Baik**: React dapat menampilkan pesan kesalahan dan peringatan yang lebih bermanfaat saat menggunakan JSX.

### Contoh Dasar JSX

```jsx
const element = <h1>Hello, React!</h1>;
```

Ini bukan string atau HTML biasa, melainkan JSX.

### Aturan Penulisan Dasar JSX

#### 1. Satu Elemen Root

Setiap blok JSX harus memiliki satu elemen root tunggal. Jika Anda perlu mengembalikan beberapa elemen, bungkus dalam satu elemen induk (misalnya `<div>`, `<>`, atau `React.Fragment`).

```jsx
// Benar
const element = (
  <div>
    <h1>Judul</h1>
    <p>Paragraf</p>
  </div>
);

// Benar (menggunakan Fragment)
const element = (
  <>
    <h1>Judul</h1>
    <p>Paragraf</p>
  </>
);

// Salah
// const element = (
//   <h1>Judul</h1>
//   <p>Paragraf</p>
// );
```

#### 2. Penulisan Atribut

Penulisan atribut di JSX mirip dengan HTML, tetapi beberapa atribut harus ditulis dengan aturan JSX (misalnya, `class` menjadi `className`, `for` menjadi `htmlFor`). Atribut juga ditulis dalam camelCase.

```jsx
const button = <button className="my-button" onClick={handleClick}>Klik Saya</button>;
```

#### 3. Menyematkan Ekspresi JavaScript

Anda dapat menyematkan ekspresi JavaScript di dalam JSX menggunakan kurung kurawal `{}`.

```jsx
const name = "Dunia React";
const element = <h1>Hello, {name}!</h1>;

const sum = 10 + 5;
const resultElement = <p>Hasil penjumlahan: {sum}</p>;

function formatUser(user) {
  return user.firstName + ' ' + user.lastName;
}
const user = { firstName: 'Jane', lastName: 'Doe' };
const userElement = <p>Nama Pengguna: {formatUser(user)}</p>;
```

#### 4. Komentar di JSX

Komentar di JSX ditulis di dalam kurung kurawal `{}`.

```jsx
const element = (
  <div>
    {/* Ini adalah komentar di JSX */}
    <h1>Judul</h1>
    <p>Paragraf</p>
  </div>
);
```

#### 5. Tag Kosong

Jika sebuah tag tidak memiliki children, Anda dapat menutupnya dengan `/` di akhir tag, mirip dengan XML.

```jsx
const image = <img src="logo.png" alt="Logo" />;
```

JSX akan dikompilasi menjadi JavaScript agar browser dapat membacanya.

---
## Rangkuman

### Apa yang Telah Dipelajari

Kita telah mempelajari konsep-konsep fundamental React.js yang menjadi dasar pengembangan aplikasi web modern:

#### 1. **React.js - Library JavaScript untuk UI**
- React adalah library JavaScript open-source untuk membangun user interface yang interaktif
- Dikembangkan oleh Facebook pada tahun 2013
- Menawarkan komponen yang dapat digunakan kembali, performa tinggi, dan komunitas yang besar

#### 2. **Arsitektur Aplikasi Web**
- **SPA (Single Page Application)**: Aplikasi yang memuat satu halaman HTML dan memperbarui konten secara dinamis
  - Kelebihan: Pengalaman pengguna mulus, performa cepat setelah initial load
  - Kekurangan: Initial load lebih lama, tantangan SEO
- **MPA (Multi Page Application)**: Aplikasi tradisional dengan multiple halaman HTML
  - Kelebihan: SEO lebih baik, initial load lebih cepat
  - Kekurangan: Pengalaman pengguna kurang mulus, interaksi lebih lambat

#### 3. **Virtual DOM dan Reconciliation**
- **Virtual DOM**: Representasi ringan dari DOM yang disimpan dalam memori
- **Reconciliation**: Proses membandingkan dan memperbarui DOM secara efisien
- Proses: Render → Diffing → Commit untuk optimasi performa

#### 4. **Setup Development Environment**
- **Node.js dan npm**: Runtime JavaScript dan package manager
- **Yarn**: Alternatif npm dengan instalasi lebih cepat
- **Create React App vs Vite**: Tool untuk memulai proyek React
  - CRA: Zero-configuration, cocok untuk pemula
  - Vite: Performa lebih cepat, fleksibilitas tinggi

#### 5. **Struktur Folder React**
- Pendekatan berdasarkan fitur atau jenis file
- Organisasi komponen, pages, utils, dan assets
- Tips: Hindari nesting terlalu dalam, mulai sederhana

#### 6. **JSX Syntax Dasar**
- **JSX**: Ekstensi sintaks JavaScript untuk menulis markup
- **Aturan dasar**: Satu elemen root, atribut camelCase, embedding expressions
- **Perbedaan dengan HTML**: `className` vs `class`, `htmlFor` vs `for`

### Poin-Poin Penting

✅ **React adalah library, bukan framework** - fokus pada UI components  
✅ **Virtual DOM** - kunci performa React yang efisien  
✅ **SPA vs MPA** - pilihan berdasarkan kebutuhan aplikasi  
✅ **JSX** - sintaks yang intuitif untuk mendefinisikan UI  
✅ **Development tools** - CRA untuk pemula, Vite untuk performa  

---

## Evaluasi Harian

Berikut adalah tugas yang harus dikerjakan oleh santri pada hari pertama:

1. **Membuat Proyek React Baru**
   - Buatlah sebuah proyek React baru menggunakan Create React App atau Vite.
   - Pastikan proyek dapat dijalankan di komputer masing-masing.

2. **Memahami Perbedaan JSX dengan HTML**
   - Tuliskan penjelasan singkat mengenai perbedaan antara JSX dan HTML.
   - Sertakan contoh kode sederhana yang menunjukkan perbedaan tersebut.

3. **Menjelaskan Konsep Virtual DOM**
   - Buatlah ringkasan singkat tentang apa itu Virtual DOM dan bagaimana cara kerjanya di React.

4. **Menjelaskan Perbedaan SPA vs MPA**
   - Tuliskan penjelasan mengenai perbedaan antara Single Page Application (SPA) dan Multi Page Application (MPA).
   - Berikan minimal 2 kelebihan dan 2 kekurangan dari masing-masing arsitektur.

> **Catatan:**
> - Tugas dikerjakan secara individu dan dikumpulkan dalam bentuk file markdown atau dokumen digital.
> - Sertakan hasil proyek React (screenshot atau link repository) pada tugas pertama.