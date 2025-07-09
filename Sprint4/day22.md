# Modul Pembelajaran React.js - Hari ke-22

## Topik Harian: Advanced Hooks dan Patterns

Pada hari kedua puluh dua ini, kita akan mendalami beberapa *Hooks* dan pola desain React yang lebih canggih. Materi ini akan membantu Anda menangani skenario yang lebih kompleks dan menulis kode yang lebih fleksibel dan dapat digunakan kembali.

## Kompetensi:

*   Mampu menggunakan `useRef` dan `forwardRef` untuk berinteraksi dengan elemen DOM atau nilai yang persisten.
*   Mampu menggunakan `useImperativeHandle` untuk mengekspos fungsi ke komponen induk.
*   Mampu menggunakan React Portals untuk me-*render* *children* di luar hierarki DOM induk.
*   Memahami dan mampu menerapkan *Higher-Order Components* (HOCs).
*   Memahami dan mampu menerapkan pola *Render Props*.

## Materi Pembelajaran:

### 1. `useRef` dan `forwardRef`

#### a. `useRef` Hook

`useRef` adalah *Hook* yang memungkinkan Anda membuat "ref" yang dapat menampung nilai yang dapat diubah dan persisten di antara *render* komponen. Ini memiliki dua kasus penggunaan utama:

1.  **Mengakses Elemen DOM**: Cara paling umum untuk menggunakan `useRef` adalah untuk mendapatkan referensi langsung ke elemen DOM. Ini berguna untuk memanipulasi DOM secara langsung (misalnya, fokus pada *input*, memutar media, mengukur dimensi).
2.  **Menyimpan Nilai yang Dapat Diubah**: `useRef` juga dapat digunakan untuk menyimpan nilai apa pun yang tidak memicu *re-render* saat berubah. Ini berbeda dengan `useState` yang memicu *re-render* setiap kali *state* berubah.

```jsx
import React, { useRef } from 'react';

function TextInputWithFocusButton() {
  const inputRef = useRef(null); // Membuat ref

  const focusInput = () => {
    // Mengakses elemen DOM melalui current property dari ref
    inputRef.current.focus();
  };

  return (
    <div>
      <h2>useRef: Mengakses DOM</h2>
      <input type="text" ref={inputRef} /> {/* Menghubungkan ref ke elemen input */}
      <button onClick={focusInput}>Fokus pada Input</button>
    </div>
  );
}

function PersistentCounter() {
  const countRef = useRef(0); // Menyimpan nilai yang persisten

  const increment = () => {
    countRef.current += 1;
    console.log('Count (ref):', countRef.current);
    // Perhatikan: Perubahan countRef.current tidak akan memicu re-render
  };

  return (
    <div>
      <h2>useRef: Menyimpan Nilai Persisten</h2>
      <p>Nilai dalam ref (cek konsol): {countRef.current}</p>
      <button onClick={increment}>Tambah Ref</button>
      <p>
        *Perubahan nilai ref tidak memicu re-render. Untuk melihat perubahan di UI,
        Anda perlu memicu re-render secara manual (misalnya dengan state lain).
      </p>
    </div>
  );
}

function App() {
  return (
    <div>
      <TextInputWithFocusButton />
      <hr />
      <PersistentCounter />
    </div>
  );
}

export default App;
```

#### b. `forwardRef`

Secara *default*, Anda tidak dapat melampirkan `ref` ke komponen fungsional kustom seperti yang Anda lakukan pada elemen DOM (`<input ref={inputRef} />`). Ini karena komponen fungsional tidak memiliki *instance* DOM langsung. `forwardRef` memungkinkan Anda "meneruskan" `ref` dari komponen induk ke elemen DOM atau komponen React lain di dalamnya.

```jsx
import React, { useRef, forwardRef } from 'react';

// Komponen InputKustom yang meneruskan ref ke elemen input internalnya
const InputKustom = forwardRef((props, ref) => {
  return (
    <input type="text" ref={ref} {...props} style={{ padding: '8px', border: '1px solid #ccc' }} />
  );
});

function App() {
  const inputRef = useRef(null);

  const handleClick = () => {
    inputRef.current.focus();
    inputRef.current.value = 'Fokus dari Induk!';
  };

  return (
    <div>
      <h2>forwardRef: Meneruskan Ref ke Komponen Kustom</h2>
      <InputKustom ref={inputRef} placeholder="Ketik sesuatu..." />
      <button onClick={handleClick}>Fokus dan Isi Input Kustom</button>
    </div>
  );
}

export default App;
```

### 2. `useImperativeHandle`

`useImperativeHandle` adalah *Hook* yang digunakan bersama dengan `forwardRef`. Ini memungkinkan Anda untuk menyesuaikan nilai *instance* yang diekspos ke *ref* saat menggunakan `forwardRef`. Ini sangat berguna ketika Anda ingin mengekspos hanya fungsi atau properti tertentu dari komponen anak ke komponen induk, daripada seluruh *instance* DOM.

**Kapan Menggunakan `useImperativeHandle`?**
Ketika Anda perlu memicu fungsi atau mengakses properti tertentu dari komponen anak dari komponen induk, tetapi Anda ingin membatasi apa yang dapat diakses melalui *ref* untuk menjaga *encapsulation*.

```jsx
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

// Komponen Modal yang mengekspos fungsi open dan close melalui ref
const Modal = forwardRef((props, ref) => {
  const [isOpen, setIsOpen] = React.useState(false);

  // Menggunakan useImperativeHandle untuk mengekspos fungsi ke ref
  useImperativeHandle(ref, () => ({
    open: () => setIsOpen(true),
    close: () => setIsOpen(false),
    // Anda bisa mengekspos properti lain juga
    // isModalOpen: isOpen,
  }));

  if (!isOpen) return null;

  return (
    <div style={{
      position: 'fixed',
      top: '50%',
      left: '50%',
      transform: 'translate(-50%, -50%)',
      backgroundColor: 'white',
      padding: '20px',
      border: '1px solid black',
      zIndex: 1000
    }}>
      <h3>Ini adalah Modal</h3>
      <p>{props.children}</p>
      <button onClick={() => setIsOpen(false)}>Tutup</button>
    </div>
  );
});

function App() {
  const modalRef = useRef(null);

  const handleOpenModal = () => {
    modalRef.current.open(); // Memanggil fungsi open dari komponen anak
  };

  const handleCloseModal = () => {
    modalRef.current.close(); // Memanggil fungsi close dari komponen anak
  };

  return (
    <div>
      <h2>useImperativeHandle: Mengontrol Komponen Anak</h2>
      <button onClick={handleOpenModal}>Buka Modal</button>
      <button onClick={handleCloseModal}>Tutup Modal</button>
      <Modal ref={modalRef}>
        <p>Konten di dalam modal.</p>
      </Modal>
    </div>
  );
}

export default App;
```

### 3. React Portals

React Portals menyediakan cara untuk me-*render* *children* ke dalam node DOM yang ada di luar hierarki DOM komponen induk. Ini sangat berguna untuk kasus penggunaan seperti *modals*, *tooltips*, atau *popovers* yang secara visual perlu "melarikan diri" dari *overflow* atau *z-index* dari elemen induknya.

```jsx
import React, { useState } from 'react';
import ReactDOM from 'react-dom';

// Komponen Modal yang menggunakan Portal
function Modal({ children, isOpen, onClose }) {
  if (!isOpen) return null;

  return ReactDOM.createPortal(
    <div style={{
      position: 'fixed',
      top: 0,
      left: 0,
      right: 0,
      bottom: 0,
      backgroundColor: 'rgba(0,0,0,0.5)',
      display: 'flex',
      justifyContent: 'center',
      alignItems: 'center',
      zIndex: 999
    }}>
      <div style={{
        backgroundColor: 'white',
        padding: '20px',
        borderRadius: '8px',
        boxShadow: '0 4px 6px rgba(0,0,0,0.1)',
        minWidth: '300px',
        textAlign: 'center'
      }}>
        {children}
        <button onClick={onClose} style={{ marginTop: '15px', padding: '8px 15px' }}>
          Tutup Modal
        </button>
      </div>
    </div>,
    document.getElementById('modal-root') // Target DOM node untuk portal
  );
}

function App() {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <div style={{ border: '2px solid blue', padding: '20px', margin: '20px' }}>
      <h2>React Portals</h2>
      <p>
        Konten ini berada di dalam komponen App. Modal akan di-render di luar hierarki DOM ini.
      </p>
      <button onClick={() => setIsModalOpen(true)}>Buka Modal</button>

      <Modal isOpen={isModalOpen} onClose={() => setIsModalOpen(false)}>
        <h3>Pesan Penting!</h3>
        <p>Ini adalah konten modal yang di-render menggunakan React Portal.</p>
      </Modal>
      {/* Pastikan ada div dengan id="modal-root" di public/index.html Anda */}
      {/* <div id="modal-root"></div> */}
    </div>
  );
}

export default App;
```
**Penting**: Anda perlu menambahkan `div` dengan `id="modal-root"` (atau nama apa pun yang Anda pilih) di file `public/index.html` Anda, di luar `div` `root` utama aplikasi React Anda.

```html
<!-- public/index.html -->
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <div id="modal-root"></div> <!-- Tambahkan ini -->
</body>
```

### 4. Higher-Order Components (HOCs)

*Higher-Order Component* (HOC) adalah teknik canggih di React untuk menggunakan kembali logika komponen. HOC adalah fungsi yang mengambil komponen sebagai argumen dan mengembalikan komponen baru dengan fungsionalitas tambahan.

**Kapan Menggunakan HOCs?**
*   Untuk menggunakan kembali logika non-visual (misalnya, *fetching* data, *state management*, *authentication*).
*   Untuk memanipulasi *props* atau *state* komponen.
*   Untuk *conditional rendering*.

```jsx
import React, { useState, useEffect } from 'react';

// HOC: withLoading - Menambahkan fungsionalitas loading state
function withLoading(Component) {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) {
      return <p>Memuat data...</p>;
    }
    return <Component {...props} />;
  };
}

// Komponen yang akan menerima fungsionalitas loading
function MyComponent({ data }) {
  return (
    <div>
      <h3>Data yang Dimuat:</h3>
      <ul>
        {data.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}

// Menerapkan HOC ke MyComponent
const MyComponentWithLoading = withLoading(MyComponent);

function App() {
  const [loading, setLoading] = useState(true);
  const [items, setItems] = useState([]);

  useEffect(() => {
    // Simulasikan fetching data
    setTimeout(() => {
      setItems(['Item 1', 'Item 2', 'Item 3']);
      setLoading(false);
    }, 2000);
  }, []);

  return (
    <div>
      <h2>Higher-Order Components (HOCs)</h2>
      <MyComponentWithLoading isLoading={loading} data={items} />
    </div>
  );
  // Perhatikan: Dengan Hooks, banyak kasus penggunaan HOC dapat digantikan oleh Custom Hooks.
  // Namun, HOC tetap merupakan pola yang valid dan sering ditemukan di codebase lama.
}

export default App;
```

### 5. Render Props Pattern

Pola *Render Props* adalah teknik untuk berbagi kode antara komponen React menggunakan *prop* yang nilainya adalah fungsi. Komponen anak memanggil fungsi ini dengan data yang relevan, dan fungsi tersebut mengembalikan elemen React yang akan di-*render*.

**Kapan Menggunakan Render Props?**
*   Untuk berbagi logika non-visual yang sama seperti HOCs.
*   Ketika Anda ingin lebih fleksibel dalam bagaimana logika yang dibagikan memengaruhi *rendering* komponen.

```jsx
import React, { useState } from 'react';

// Komponen yang menyediakan logika (misalnya, melacak posisi mouse)
function MouseTracker(props) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const handleMouseMove = (event) => {
    setPosition({
      x: event.clientX,
      y: event.clientY,
    });
  };

  return (
    <div style={{ height: '200px', border: '1px solid black', position: 'relative' }} onMouseMove={handleMouseMove}>
      {/* Memanggil fungsi render prop dengan data yang relevan */}
      {props.render(position)}
    </div>
  );
}

// Komponen yang menggunakan MouseTracker untuk menampilkan posisi mouse
function App() {
  return (
    <div>
      <h2>Render Props Pattern</h2>
      <p>Gerakkan mouse Anda di dalam kotak di bawah ini:</p>
      <MouseTracker
        render={(mousePosition) => (
          <p style={{ position: 'absolute', top: mousePosition.y - 10, left: mousePosition.x - 10 }}>
            ({mousePosition.x}, {mousePosition.y})
          </p>
        )}
      />
      <p>
        *Pola Render Props juga sering digantikan oleh Custom Hooks di aplikasi modern.
        Namun, ini adalah pola yang penting untuk dipahami.
      </p>
    </div>
  );
}

export default App;
```

`useRef`, `forwardRef`, `useImperativeHandle`, React Portals, HOCs, dan *Render Props* adalah alat-alat canggih yang memungkinkan Anda membangun aplikasi React yang lebih kompleks, fleksibel, dan dapat digunakan kembali. Memahami kapan dan bagaimana menggunakan masing-masing pola ini akan meningkatkan kemampuan Anda sebagai pengembang React.