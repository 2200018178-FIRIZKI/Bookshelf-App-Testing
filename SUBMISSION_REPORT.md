# Submission Report - Bookshelf App

**Project name**: Submission Bookshelf App - Belajar Membuat Front-End Web untuk Pemula

## Project Summary

Bookshelf App adalah aplikasi web sederhana untuk mengelola koleksi buku digital. Aplikasi ini memungkinkan pengguna untuk menambah, menghapus, mengubah status baca, dan mencari buku dalam koleksi mereka. Data disimpan menggunakan localStorage browser sehingga data tetap tersimpan meskipun browser ditutup.

### Fitur Utama:
1. **Tambah Buku Baru** - Form untuk menambahkan buku dengan judul, penulis, tahun, dan status baca
2. **Manajemen Status Buku** - Memindahkan buku antara rak "Belum selesai dibaca" dan "Selesai dibaca"
3. **Hapus Buku** - Menghapus buku dari koleksi
4. **Pencarian Buku** - Mencari buku berdasarkan judul
5. **Penyimpanan Lokal** - Data tersimpan di localStorage browser

### Code Cleanup yang Dilakukan:
- Menghapus variabel `notDoneYet` dan `myLibrary` yang tidak digunakan secara efektif
- Menghapus duplikasi event listener `DOMContentLoaded`
- Membersihkan kode yang tidak pernah dieksekusi
- Memperbaiki penamaan variabel agar lebih konsisten (`bookKey`, `bookInfo`)

### Resource Optimization:
- Mengganti favicon eksternal dengan inline SVG untuk mengurangi HTTP request
- Mengoptimalkan struktur CSS dengan menghapus selector yang tidak digunakan
- Mengurangi redundansi dalam kode JavaScript

## Error Notes

Pada project yang diperiksa terjadi beberapa error dan saya mengatasinya dengan cara:

### 1. Error Favicon 404
**Problem**: Browser menampilkan error "Failed to load resource: favicon.ico (Net::ERR_FILE_NOT_FOUND)"
**Solution**: Menambahkan inline SVG favicon menggunakan data URI untuk menghindari request file eksternal:
```html
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>📚</text></svg>" />
```

### 2. Error Form Submission
**Problem**: Form tidak menggunakan `preventDefault()` sehingga halaman reload setiap kali submit
**Solution**: Mengaktifkan `event.preventDefault()` pada form submit handler:
```javascript
submitForm.addEventListener('submit', function (event) {
  event.preventDefault(); // Mencegah reload halaman
  addTodo();
});
```

### 3. Error Variable Reference
**Problem**: Fungsi `matchBook()` menggunakan variabel `container` yang tidak terdefinisi
**Solution**: Memperbaiki logika pencarian dan menghapus referensi ke variabel yang tidak ada:
```javascript
// Sebelum: searchBookForm.append(container); // Error: container undefined
// Setelah: searchBookForm.appendChild(bookItem); // Menggunakan elemen yang benar
```

### 4. Error Data-testid Attributes
**Problem**: Atribut `data-testid` menggunakan nilai dinamis alih-alih nilai tetap sesuai ketentuan
**Solution**: Memperbaiki atribut data-testid agar sesuai dengan spesifikasi:
```javascript
// Sebelum: bookItemTitle.setAttribute('data-testid', `${bookInfo.title}`);
// Setelah: bookItemTitle.setAttribute('data-testid', 'bookItemTitle');
```

## Code Review

### 1. Event Listener Management
```javascript
// SEBELUM - Duplikasi event listener
document.addEventListener('DOMContentLoaded', () => {
  // Handler untuk form buku
});
document.addEventListener('DOMContentLoaded', function () {
  // Handler untuk form pencarian (duplikat)
});
```

**Feedback**: Duplikasi event listener dapat menyebabkan memory leak dan perilaku tidak terduga. Sebaiknya gabungkan semua initialization dalam satu event listener.

```javascript
// SETELAH - Single event listener
document.addEventListener('DOMContentLoaded', () => {
  const submitForm = document.getElementById('bookForm');
  submitForm.addEventListener('submit', function (event) {
    event.preventDefault();
    addTodo();
  });

  const searchBookForm = document.getElementById('searchBook');
  searchBookForm.addEventListener('submit', function (event) {
    event.preventDefault();
    matchBook();
  });

  showBooks();
});
```

### 2. Variable Naming dan Scope
```javascript
// SEBELUM - Variabel global yang tidak diperlukan
let notDoneYet = [];
let myLibrary = [];

function showBooks() {
  for (let i = 0; i < localStorage.length; i++) {
    bookKey = localStorage.key(i); // Variabel global implisit
    bookInfo = JSON.parse(localStorage.getItem(`${bookKey}`)); // Variabel global implisit
  }
}
```

**Feedback**: Variabel global dapat menyebabkan konflik nama dan kesulitan debugging. Gunakan `const`/`let` dengan scope yang tepat.

```javascript
// SETELAH - Proper variable scoping
function showBooks() {
  for (let i = 0; i < localStorage.length; i++) {
    const bookKey = localStorage.key(i);
    const bookInfo = JSON.parse(localStorage.getItem(bookKey));
  }
}
```

### 3. Error Handling
```javascript
// SEBELUM - Tidak ada validasi input
function matchBook() {
  const valueSearchBook = document.getElementById('searchBookTitle').value;
  // Langsung memproses tanpa validasi
}
```

**Feedback**: Selalu validasi input pengguna untuk mencegah error dan memberikan user experience yang lebih baik.

```javascript
// SETELAH - Dengan validasi
function matchBook() {
  const valueSearchBook = document.getElementById('searchBookTitle').value;
  
  if (!valueSearchBook.trim()) {
    alert('Masukkan judul buku yang ingin dicari!');
    return;
  }
  // Lanjutkan pemrosesan
}
```

### 4. DOM Manipulation Efficiency
```javascript
// SEBELUM - Manipulasi DOM yang tidak efisien
bookItemIsCompleteButton.setAttribute('onclick', `itemIsComplete(id)`); // Hardcoded string
```

**Feedback**: Menggunakan setAttribute dengan onclick string berbahaya dan tidak efisien. Lebih baik gunakan addEventListener.

```javascript
// SETELAH - Lebih aman dan maintainable
bookItemIsCompleteButton.addEventListener('click', () => {
  itemIsComplete(bookInfo.id);
});
```

### 5. Data Structure Usage
```javascript
// SEBELUM - Pencarian linear yang tidak efisien
for (let i = 0; i < localStorage.length; i++) {
  const bookKey = localStorage.key(i);
  const bookInfo = JSON.parse(localStorage.getItem(bookKey));
  if (bookInfo.title.toLowerCase().includes(valueSearchBook.toLowerCase())) {
    // Process found book
  }
}
```

**Feedback**: Untuk aplikasi dengan data yang lebih besar, pertimbangkan menggunakan struktur data yang lebih efisien seperti Map atau menyimpan index pencarian.

```javascript
// ALTERNATIF - Menggunakan array untuk pencarian yang lebih efisien
const allBooks = Object.keys(localStorage)
  .map(key => JSON.parse(localStorage.getItem(key)))
  .filter(book => book.title.toLowerCase().includes(searchTerm.toLowerCase()));
```

## Kesimpulan

Proyek Bookshelf App telah berhasil diperbaiki dengan mengatasi berbagai error dan mengoptimalkan kode. Aplikasi sekarang berjalan dengan stabil, mengikuti best practices JavaScript, dan memenuhi semua ketentuan submission Dicoding.

### Improvements Made:
✅ Fixed favicon 404 error  
✅ Implemented proper form handling  
✅ Corrected data-testid attributes  
✅ Optimized search functionality  
✅ Removed unused variables and duplicated code  
✅ Added proper error handling and user feedback  
✅ Improved code organization and readability  

Aplikasi siap untuk disubmit dan telah diuji untuk memastikan semua fitur berfungsi dengan baik.
