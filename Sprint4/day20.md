# Modul Pembelajaran React.js - Hari ke-20

## Topik Harian: Testing dalam React

Pada hari kedua puluh ini, kita akan mempelajari tentang **Testing dalam React**. Pengujian adalah bagian krusial dari pengembangan perangkat lunak yang berkualitas. Dengan menguji komponen React Anda, Anda dapat memastikan bahwa mereka berfungsi seperti yang diharapkan, mencegah *bug*, dan memfasilitasi *refactoring* yang aman.

## Kompetensi:

*   Memahami peran Jest dan React Testing Library dalam pengujian React.
*   Mampu melakukan *Unit Testing* komponen React.
*   Mampu menguji interaksi pengguna.
*   Mampu melakukan *mocking* dependensi dan panggilan API.
*   Memahami konsep *Test Coverage*.

## Materi Pembelajaran:

### 1. Jest dan React Testing Library

Ada beberapa alat yang digunakan untuk pengujian di ekosistem React. Yang paling umum adalah kombinasi Jest dan React Testing Library.

*   **Jest**:
    *   Jest adalah *framework* pengujian JavaScript yang dikembangkan oleh Facebook.
    *   Ini adalah *test runner*, *assertion library*, dan *mocking framework* yang lengkap.
    *   Jest sangat cepat dan mudah dikonfigurasi, terutama untuk proyek React (seringkali sudah terintegrasi dengan Create React App).

*   **React Testing Library (RTL)**:
    *   RTL adalah sekumpulan *utility* yang membantu Anda menguji komponen React dengan cara yang mendorong praktik terbaik.
    *   Filosofi RTL adalah "semakin mirip pengujian Anda dengan cara pengguna menggunakan perangkat lunak Anda, semakin besar kepercayaan diri yang dapat diberikannya kepada Anda."
    *   RTL berfokus pada pengujian perilaku komponen dari perspektif pengguna, bukan detail implementasi internal.

**Instalasi (jika belum ada, CRA/Vite biasanya sudah menyertakan Jest dan RTL):**
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event jest
# atau
yarn add --dev @testing-library/react @testing-library/jest-dom @testing-library/user-event jest
```
Pastikan juga Anda memiliki konfigurasi Jest yang benar di `package.json` atau file konfigurasi Jest terpisah.

### 2. Unit Testing Components

*Unit testing* adalah pengujian unit terkecil dari kode Anda secara terisolasi. Dalam konteks React, ini berarti menguji komponen individual.

**Langkah-langkah Dasar Unit Testing Komponen:**
1.  **Render Komponen**: Gunakan fungsi `render` dari RTL untuk me-*render* komponen ke dalam lingkungan pengujian.
2.  **Cari Elemen**: Gunakan *query* dari RTL (misalnya `getByText`, `getByRole`, `getByLabelText`) untuk menemukan elemen di DOM yang di-*render*.
3.  **Lakukan Assertions**: Gunakan *matcher* Jest (misalnya `toBeInTheDocument`, `toHaveTextContent`, `toHaveAttribute`) untuk memverifikasi bahwa elemen berperilaku seperti yang diharapkan.

**Contoh Unit Testing Komponen Sederhana:**

```jsx
// src/components/Button.js
import React from 'react';

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

export default Button;

// src/components/Button.test.js
import React from 'react';
import { render, screen } from '@testing-library/react';
import Button from './Button';

test('renders button with correct text', () => {
  render(<Button>Klik Saya</Button>);
  const buttonElement = screen.getByText(/klik saya/i); // Mencari teks secara case-insensitive
  expect(buttonElement).toBeInTheDocument(); // Memastikan elemen ada di DOM
});

test('button has type attribute by default', () => {
  render(<Button>Submit</Button>);
  const buttonElement = screen.getByRole('button', { name: /submit/i });
  expect(buttonElement).toHaveAttribute('type', 'button'); // Memastikan atribut type adalah 'button'
});
```

### 3. Testing User Interactions

RTL menyediakan `user-event` *library* untuk mensimulasikan interaksi pengguna dengan cara yang lebih realistis daripada `fireEvent`.

**Contoh Testing User Interactions:**

```jsx
// src/components/Counter.js
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}

export default Counter;

// src/components/Counter.test.js
import React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event'; // Import userEvent
import Counter from './Counter';

test('increments count when increment button is clicked', async () => {
  render(<Counter />);
  const incrementButton = screen.getByRole('button', { name: /increment/i });
  const countElement = screen.getByText(/count: 0/i);

  expect(countElement).toHaveTextContent('Count: 0');

  await userEvent.click(incrementButton); // Mensimulasikan klik
  expect(countElement).toHaveTextContent('Count: 1');

  await userEvent.click(incrementButton);
  expect(countElement).toHaveTextContent('Count: 2');
});

test('resets count when reset button is clicked', async () => {
  render(<Counter />);
  const incrementButton = screen.getByRole('button', { name: /increment/i });
  const resetButton = screen.getByRole('button', { name: /reset/i });
  const countElement = screen.getByText(/count: 0/i);

  await userEvent.click(incrementButton); // Count jadi 1
  await userEvent.click(incrementButton); // Count jadi 2
  expect(countElement).toHaveTextContent('Count: 2');

  await userEvent.click(resetButton); // Reset
  expect(countElement).toHaveTextContent('Count: 0');
});
```

### 4. Mocking API Calls

Dalam pengujian unit, Anda tidak ingin benar-benar memanggil API eksternal karena itu akan membuat pengujian lambat, tidak stabil, dan bergantung pada ketersediaan *server*. Sebagai gantinya, Anda akan "mengolok-olok" (*mock*) panggilan API. Jest memiliki kemampuan *mocking* yang kuat.

**Pendekatan Umum:**
*   **`jest.mock()`**: Untuk mengolok-olok seluruh modul (misalnya, `axios`).
*   **`jest.fn()`**: Untuk membuat fungsi *mock* individual.
*   **`fetch-mock` atau `msw`**: *Library* yang lebih canggih untuk *mocking* permintaan jaringan.

**Contoh Mocking API Calls (menggunakan `jest.mock` untuk `fetch`):**

```jsx
// src/components/UserFetcher.js
import React, { useState, useEffect } from 'react';

function UserFetcher() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users/1');
        if (!response.ok) {
          throw new Error('Failed to fetch user');
        }
        const data = await response.json();
        setUser(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };
    fetchUser();
  }, []);

  if (loading) return <div>Memuat pengguna...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!user) return <div>Tidak ada pengguna.</div>;

  return (
    <div>
      <h2>Detail Pengguna</h2>
      <p>Nama: {user.name}</p>
      <p>Email: {user.email}</p>
    </div>
  );
}

export default UserFetcher;

// src/components/UserFetcher.test.js
import React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import UserFetcher from './UserFetcher';

// Mock global fetch API
global.fetch = jest.fn();

describe('UserFetcher', () => {
  beforeEach(() => {
    // Reset mock sebelum setiap test
    fetch.mockClear();
  });

  test('renders loading state initially', () => {
    render(<UserFetcher />);
    expect(screen.getByText(/memuat pengguna/i)).toBeInTheDocument();
  });

  test('renders user data after successful fetch', async () => {
    // Mock respons sukses dari fetch
    fetch.mockResolvedValueOnce({
      ok: true,
      json: async () => ({ id: 1, name: 'Leanne Graham', email: 'Sincere@april.biz' }),
    });

    render(<UserFetcher />);

    // Menunggu hingga loading text hilang dan data pengguna muncul
    await waitFor(() => expect(screen.queryByText(/memuat pengguna/i)).not.toBeInTheDocument());

    expect(screen.getByText(/detail pengguna/i)).toBeInTheDocument();
    expect(screen.getByText(/nama: leanne graham/i)).toBeInTheDocument();
    expect(screen.getByText(/email: sincere@april.biz/i)).toBeInTheDocument();
  });

  test('renders error message on fetch failure', async () => {
    // Mock respons gagal dari fetch
    fetch.mockRejectedValueOnce(new Error('Network error'));

    render(<UserFetcher />);

    await waitFor(() => expect(screen.queryByText(/memuat pengguna/i)).not.toBeInTheDocument());

    expect(screen.getByText(/error: network error/i)).toBeInTheDocument();
  });
});
```

### 5. Test Coverage

*Test coverage* adalah metrik yang menunjukkan seberapa banyak kode Anda yang diuji oleh *test* Anda. Jest dapat menghasilkan laporan *test coverage*.

**Cara Menjalankan Test Coverage dengan Jest:**
Tambahkan *flag* `--coverage` saat menjalankan *test* Anda:
```bash
npm test -- --coverage
# atau
yarn test --coverage
```
Ini akan menghasilkan laporan di terminal dan juga membuat folder `coverage` di *root* proyek Anda yang berisi laporan HTML yang lebih detail.

**Memahami Laporan Coverage:**
Laporan *coverage* biasanya menunjukkan persentase untuk:
*   **Statements**: Berapa banyak pernyataan kode yang dieksekusi.
*   **Branches**: Berapa banyak cabang kondisional (misalnya, `if/else`, `switch`) yang dieksekusi.
*   **Functions**: Berapa banyak fungsi yang dipanggil.
*   **Lines**: Berapa banyak baris kode yang dieksekusi.

**Penting**: *Test coverage* yang tinggi tidak selalu berarti kode Anda bebas *bug*. Ini hanya menunjukkan seberapa banyak kode yang dieksekusi oleh *test*. Anda tetap perlu menulis *test* yang bermakna dan menguji skenario penting.

Dengan menguasai pengujian di React, Anda akan dapat membangun aplikasi yang lebih andal, mudah dipelihara, dan memberikan kepercayaan diri yang lebih besar dalam setiap perubahan kode.