# Advanced Filter Panel & Faceted Search (Faceted Filtering)

> **Kategori:** Data Display & Search • **Tingkat Kesulitan:** Menengah-Tinggi • **Tanggal:** 27 Juni 2026

---

## Apa itu Advanced Filter Panel & Faceted Search?

**Faceted Search / Filter Panel** adalah pola desain UI/UX yang memungkinkan pengguna menyaring dan menyempurnakan daftar data atau produk berdasarkan berbagai atribut (*facets*), seperti kategori, rentang harga, rating, warna, ketersediaan, dan preferensi spesifik lainnya.

Tidak seperti filter sederhana tunggal, *faceted filtering* menampilkan jumlah hasil (item count) yang diperbarui secara real-time atau live-counter pada setiap kriteria. Pola ini memberikan umpan balik langsung kepada pengguna mengenai seberapa banyak hasil yang akan mereka dapatkan sebelum mereka mengaplikasikan filter tersebut.

Di e-commerce modern (seperti Tokopedia, Amazon, atau Traveloka) serta aplikasi SaaS berbasis data (seperti dashboard analitik atau portal pencari kerja), Filter Panel yang dirancang dengan baik sangat menentukan tingkat kemudahan pengguna dalam menemukan barang/informasi yang relevan (*findability*).

---

## Mengapa Pattern Ini Penting?

### Masalah yang Sering Terjadi pada Filter Panel Biasa
1. **Zero-Result Trap (Trapped in Zero Results):** Pengguna memilih kombinasi filter yang terlalu spesifik dan berakhir di halaman kosong ("Produk tidak ditemukan") tanpa tahu opsi mana yang membuat hasil menjadi nol.
2. **Kueri Berulang yang Lambat (Excessive Server Loads):** Setiap kali kotak centang diklik, halaman langsung melakukan reload penuh atau melakukan permintaan API berulang yang berat tanpa mekanisme debounce atau penundaan yang disengaja.
3. **Kehilangan Konteks Otomatis (Layout Shift & Focus Loss):** Saat filter diterapkan di desktop/mobile, konten list langsung bergerak atau memicu reload yang membuat posisi scroll dan fokus keyboard pengguna hilang.
4. **Masalah Aksesibilitas (A11y):** Panel filter samping sering kali menggunakan checkbox kustom yang tidak dapat diakses keyboard, tidak mengumumkan perubahan jumlah hasil ke *screen reader* (live region), serta tombol "Clear All" yang tersembunyi.
5. **Responsivitas Mobile yang Ringkih:** Panel samping desktop dipaksakan muncul di mobile sehingga menutupi layar, atau sebaliknya modal filter mobile tidak mempertahankan state filter sementara yang dipilih sebelum ditekan "Terapkan".

### Statistik & Dampak UX
- Berdasarkan laporan riset UX dari Baymard Institute, **lebih dari 40% situs e-commerce memiliki implementasi filtering yang buruk**, menyebabkan tingkat pengembalian pencarian kosong yang tinggi dan pembatalan sesi (*bounce*).
- Menampilkan **live count (jumlah hasil)** di samping setiap opsi filter terbukti meningkatkan keberhasilan pencarian pengguna hingga **28%** karena mencegah kondisi *zero-result*.
- Menyediakan indikator filter aktif (*filter chips/badges*) dengan tombol hapus cepat satu per satu mempercepat proses eksplorasi ulang pengguna hingga **35%**.

---

## Use Case yang Ideal

### ✅ Cocok untuk:
- **E-Commerce & Digital Marketplace:** Pencarian katalog produk dengan banyak variasi (harga, ukuran, merek, warna, ulasan).
- **Portal Pemesanan Travel & Hotel:** Menyaring akomodasi berdasarkan bintang, jenis properti, fasilitas (Wi-Fi, kolam renang), dan rentang harga per malam.
- **Portal Lowongan Kerja & Rekrutmen:** Menyaring pekerjaan berdasarkan tipe lokasi (Remote, Hybrid, Onsite), gaji, tingkat pengalaman, dan spesialisasi.
- **Aplikasi SaaS & B2B Analytics:** Menyaring tabel transaksi keuangan, log audit, atau basis data pelanggan berdasarkan status, rentang tanggal, dan tag.

### ❌ Kurang cocok untuk:
- Katalog sederhana dengan item kurang dari 10-15 buah (cukup gunakan dropdown sorting sederhana).
- Form entri data lurus tanpa fungsionalitas pencarian/penjelajahan.

---

## Implementasi Teknis Terbaik

### 1. Struktur HTML (Semantic & Accessible)
Menggunakan elemen semantic `<aside>`, `<fieldset>`, `<legend>`, serta atribut ARIA seperti `aria-live` untuk pengumuman dinamik dan `aria-expanded` untuk filter yang collapsible (bisa dilipat).

```html
<aside class="filter-panel" aria-label="Filter Hasil Pencarian">
  <div class="filter-header">
    <h2>Filter</h2>
    <button type="button" id="reset-all" class="btn-link" aria-label="Hapus semua filter">Hapus Semua</button>
  </div>

  <!-- Active Filters / Chips Hub -->
  <div id="active-filters-container" class="active-filters" aria-label="Filter aktif">
    <!-- Chips dimasukkan secara dinamis oleh JS -->
  </div>

  <form id="filter-form">
    <!-- Group: Kategori -->
    <fieldset class="filter-group">
      <legend class="filter-legend">
        <span>Kategori</span>
        <button type="button" class="toggle-btn" aria-expanded="true" aria-controls="filter-category-opts">
          <span class="sr-only">Buka/tutup Kategori</span>
        </button>
      </legend>
      <div id="filter-category-opts" class="filter-options">
        <label class="custom-checkbox">
          <input type="checkbox" name="category" value="electronics" checked>
          <span class="checkmark"></span>
          <span class="label-text">Elektronik</span>
          <span class="count">(124)</span>
        </label>
        <!-- Opsi lainnya -->
      </div>
    </fieldset>
  </form>

  <!-- Live Announcer untuk Screen Readers -->
  <div id="filter-announcer" class="sr-only" role="status" aria-live="polite"></div>
</aside>
```

### 2. Styling CSS (Ergonomis & Responsif)
Menggunakan layout Flexbox/Grid modern, gaya checkbox kustom yang tetap mempertahankan ring fokus keyboard asli (`:focus-visible`), serta penanganan modal drawer untuk tampilan layar seluler.

```css
/* Custom Accessible Checkbox */
.custom-checkbox {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  position: relative;
  user-select: none;
}

.custom-checkbox input[type="checkbox"] {
  position: absolute;
  opacity: 0;
  width: 0;
  height: 0;
}

.custom-checkbox .checkmark {
  width: 20px;
  height: 20px;
  border: 2px solid var(--gray-400);
  border-radius: 4px;
  transition: all 0.2s ease;
}

.custom-checkbox input[type="checkbox"]:focus-visible + .checkmark {
  outline: 2px solid var(--primary-color);
  outline-offset: 2px;
}

.custom-checkbox input[type="checkbox"]:checked + .checkmark {
  background-color: var(--primary-color);
  border-color: var(--primary-color);
}
```

### 3. Logika JavaScript (State Management & Debounce)
Script mengelola state filter yang aktif, memperbarui daftar hasil dan *count badge*, memperbarui parameter URL (`URLSearchParams`) untuk permalink/bookmarking, serta mendukung pengoperasian keyboard yang mulus.

```javascript
// Mengelola perubahan filter dengan penanganan URL dan ARIA status
const filterForm = document.getElementById('filter-form');
const announcer = document.getElementById('filter-announcer');

filterForm.addEventListener('change', (e) => {
  updateActiveFilters();
  applyFilters();
});

function applyFilters() {
  // 1. Ambil data terbaca dari form
  // 2. Filter dataset produk
  // 3. Update DOM produk
  // 4. Umumkan jumlah hasil ke screen reader
  const count = getFilteredCount();
  announcer.textContent = `Menampilkan ${count} produk sesuai filter.`;
}
```

---

## Panduan Aksesibilitas (WCAG 2.1 / 2.2)

1. **Pengelompokan Form Semantic (`<fieldset>` & `<legend>`)**
   - Setiap kelompok filter (seperti Rentang Harga, Rating, Merek) wajib dibungkus dengan `<fieldset>` dan diberi judul menggunakan `<legend>`. Ini memastikan screen reader menyuarakan konteks kelompok saat pengguna berpindah antar elemen checkbox.

2. **Keyboard Navigation & Focus Indicator (`:focus-visible`)**
   - Seluruh elemen interaktif (checkbox, slider range, toggle accordion, tombol reset) harus dapat dijangkau menggunakan tombol `Tab` dan dioperasikan via `Space` / `Enter`.
   - Jangan pernah menghilangkan `outline` fokus tanpa memberikan pengganti visual kontras tinggi (minimal rasio 3:1).

3. **Live Region Announcements (`aria-live="polite"`)**
   - Saat pengguna mengubah item filter pada SPA (Single Page Application), konten diperbarui tanpa pereloadan halaman. Gunakan elemen tersembunyi dengan `aria-live="polite"` untuk menyuarakan jumlah hasil pencarian terbaru kepada pengguna pembaca layar secara non-intrusif.

4. **Filter Active Badges / Chips yang Mudah Dihapus**
   - Sediakan daftar chip filter aktif di bagian atas yang menyatukan seluruh kriteria yang terpilih. Setiap chip harus memiliki tombol hapus (`aria-label="Hapus filter Elektronik"`) agar pengguna pembaca layar dapat menghapus satu filter khusus tanpa harus menggeledah ulang panel samping.

5. **State Sync & Bookmarkable URLs**
   - Sinkronkan kondisi filter dengan URL Query String (misal: `?category=electronics&price_max=5000000`). Hal ini mendukung navigaasi *Back/Forward* browser dan mempermudah berbagi tautan (*deep-linking*) bagi semua pengguna.

---

## Do's dan Don'ts

### ✅ DO's (Praktik Terbaik)
- **Tampilkan Jumlah Hasil (Item Counts):** Berikankah angka seperti `Elektronik (42)` di sebelah checkbox untuk mencegah pengguna memilih filter yang bernilai nol.
- **Sediakan Tombol "Hapus Semua" (Reset All):** Selalu berikan cara cepat satu-klik untuk mengembalikan seluruh filter ke kondisi awal.
- **Gunakan Batasi Auto-Apply (Debounce / Apply Button):** Untuk filter bertipe rentang angka/slider atau pencarian teks, tunda eksekusi pencarian 300-500ms atau sediakan tombol "Terapkan" agar sistem tidak melakukan fetch API berlebihan pada setiap ketukan tombol.
- **Pertahankan State Filter Saat Mobile Drawer Ditutup:** Pada versi mobile, izinkan pengguna memilih beberapa opsi sebelum menekan tombol "Terapkan (X hasil)" di bagian navigasi bawah.

### ❌ DON'TS (Hindari)
- **Jangan Sembunyikan Hasil Terlalu Jauh di Mobile:** Mengharuskan pengguna mobile menutup drawer manual hanya untuk melihat apakah ada hasil pencarian.
- **Jangan Me-reset Scroll Position:** Jangan kembalikan scroll window pengguna ke bagian paling atas setiap kali pengguna merubah satu indikator checkbox.
- **Jangan Disable Checkbox Bernilai 0 Tanpa Penjelasan:** Jalankan penyaringan aman; jika sebuah opsi memiliki 0 item karena kombinasi filter lain, disable checkbox tersebut dan berikan styling redup atau sembunyikan sesuai konteks.
- **Jangan Gunakan Control Kustom Tanpa Keyboard Accessibility:** Menjelapang `<div>` atau `<span>` menjadi kotak centang buatan tanpa atribut `tabindex="0"`, `role="checkbox"`, dan penanganan `aria-checked`.

---

## Contoh & Referensi Produk Nyata

1. **Tokopedia (Mobile & Desktop App):**
   - Memiliki implementasi bottom sheet filter yang sangat responsif di mobile dengan *live counter* pada tombol aksi utama "Tampilkan (120) Produk".
   - Menggunakan *filter chips* horizontal di atas daftar produk untuk penghapusan filter cepat.

2. **Amazon:**
   - Standar emas dalam *faceted search* sidebar desktop dengan kategori bertingkat (*multi-level facets*) dan penyaringan berdasarkan ulasan bintang pelanggan.

3. **Traveloka:**
   - Penggunaan filter eksplorasi yang sangat kuat pada pencarian tiket pesawat dan hotel, menggunakan kombinasi slider harga, checkbox fasilitas, dan toggle instant.

4. **Airbnb:**
   - Menggunakan modal overlay filter modern berukuran besar dengan visualisasi grafik distribusi harga (*price distribution histogram*) yang membantu pengguna menentukan rentang anggaran yang efisien.

---

## Fitur Unggulan pada Demo `example.html`

Dalam berkas `example.html` yang terlampir di folder pattern ini, Anda dapat mencoba demo interaktif yang mencakup:
- **Panel Filter Desktop & Mobile Drawer:** Tampilan adaptif yang bertransisi ke bottom drawer pada antarmuka mobile.
- **Interactive Faceted Filtering:** Penyaringan produk simulasi real-time (Kategori, Rentang Harga, Rating Bintang, Status Stok).
- **Dynamic Active Filter Chips:** Chip filter interaktif yang dapat dihapus satu per satu atau sekaligus.
- **Web Accessibility Ready:** Penanganan keyboard lengkap, status ARIA live region untuk pembaca layar, dan kontras visual tinggi.
