# Infinite Scroll dengan Accessible Lazy Loading

> **Kategori:** Content Loading Pattern  
> **Tingkat Kesulitan:** Menengah  
> **Tanggal Ditambahkan:** 23 Juni 2026

---

## Apa itu Infinite Scroll?

**Infinite Scroll** (atau *endless scroll*) adalah pola pemuatan konten di mana konten baru secara otomatis dimuat dan ditambahkan ke halaman saat pengguna mendekati bagian bawah daftar — tanpa perlu mengklik tombol "Halaman Berikutnya". Pattern ini dikombinasikan dengan **Lazy Loading**, yaitu teknik menunda pemuatan elemen (terutama gambar) sampai elemen tersebut benar-benar akan terlihat di viewport.

### Cara Kerjanya

```
Pengguna scroll ↓
    → IntersectionObserver mendeteksi sentinel element mendekati viewport
    → Fetch data baru dari server (API)
    → Render item baru dan tambahkan ke DOM
    → Update ARIA live region agar screen reader tahu
    → Ulangi
```

---

## Kapan Menggunakan Pattern Ini?

### ✅ Cocok digunakan untuk:
- **Feed sosial** — Twitter/X, Instagram, Facebook: konten bersifat kontinu dan tidak linier
- **Galeri foto/produk** — Unsplash, Pinterest, e-commerce tanpa urutan ketat
- **Artikel/berita** — konten baru terus masuk, pengguna browsing tanpa tujuan spesifik
- **Search results dengan banyak item** — ketika tidak ada kebutuhan untuk "kembali ke halaman X"
- **Application-like interfaces** — dashboard, log viewer, notifikasi

### ❌ Tidak cocok untuk:
- **E-commerce dengan filter/sort** — pengguna perlu mengingat posisi item untuk dibandingkan
- **Data tabular/terstruktur** — tabel invoice, laporan keuangan, daftar kontak
- **Konten yang perlu di-share URL-nya** — tidak ada state URL yang bisa dibookmark
- **Footers penting** — pengguna tidak akan pernah mencapai footer jika infinite scroll aktif
- **Formulir panjang** — tidak relevan dan membingungkan

---

## Konsep Teknis Utama

### 1. IntersectionObserver API
Metode modern yang jauh lebih efisien daripada event listener `scroll`:

```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      loadMoreContent();
    }
  });
}, {
  rootMargin: '200px 0px', // mulai load 200px sebelum sentinel terlihat
  threshold: 0.1
});

observer.observe(sentinelElement);
```

### 2. Intersection Observer untuk Lazy Load Gambar
```javascript
const imgObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      img.classList.add('loaded');
      imgObserver.unobserve(img); // stop observing setelah load
    }
  });
}, { rootMargin: '100px 0px' });

document.querySelectorAll('img[data-src]').forEach(img => {
  imgObserver.observe(img);
});
```

### 3. ARIA Live Region untuk Aksesibilitas
```html
<!-- Wajib ada untuk screen reader! -->
<div
  role="status"
  aria-live="polite"
  aria-atomic="false"
  class="sr-only"
  id="scroll-status"
>
</div>
```

```javascript
// Update setiap kali konten baru ditambahkan
document.getElementById('scroll-status').textContent =
  `${totalItems} item dimuat. Scroll untuk melihat lebih banyak.`;
```

---

## Accessibility Considerations

Infinite scroll adalah **salah satu pattern yang paling sering buruk aksesibilitasnya**. Berikut pertimbangan kritis:

### 1. ARIA Live Region (Wajib)
Screen reader tidak otomatis tahu ada konten baru. Gunakan `aria-live="polite"` untuk mengumumkan perubahan tanpa mengganggu pembacaan aktif.

### 2. Keyboard Navigation
- Pengguna keyboard harus bisa menavigasi semua item yang dimuat
- Focus management: setelah konten dimuat, jangan pindahkan focus secara otomatis
- Sediakan tombol "Muat Lebih" sebagai alternatif (hybrid approach)

### 3. Opsi untuk Menonaktifkan (Prefers-Reduced-Motion / User Preference)
```css
@media (prefers-reduced-motion: reduce) {
  .card {
    animation: none;
    transition: none;
  }
}
```

### 4. Loading State yang Jelas
```html
<div
  role="progressbar"
  aria-label="Memuat konten..."
  aria-valuemin="0"
  aria-valuemax="100"
  aria-valuenow="50"
></div>
```

### 5. End-of-Content Signal
```html
<!-- Beritahu pengguna (dan screen reader) bahwa konten habis -->
<div role="status" aria-live="assertive">
  Semua konten telah ditampilkan. Total 120 item.
</div>
```

### 6. "Back to Top" Button
Selalu sediakan tombol kembali ke atas — pengguna keyboard dan motor disabilities membutuhkannya.

### 7. URL State (Opsional tapi Sangat Dianjurkan)
Gunakan History API untuk menyimpan posisi scroll:
```javascript
// Update URL saat halaman berubah tanpa reload
history.replaceState(null, '', `?page=${currentPage}`);
```

---

## Do's dan Don'ts

### ✅ Do's

| Yang Harus Dilakukan | Alasan |
|---|---|
| Gunakan `IntersectionObserver` bukan event scroll | Lebih efisien, tidak block main thread |
| Tambahkan ARIA live region | Screen reader perlu diberitahu ada konten baru |
| Sediakan tombol "Muat Lebih" sebagai fallback | Hybrid approach lebih accessible |
| Tampilkan indikator loading yang jelas | Pengguna tahu sistem sedang bekerja |
| Beritahu pengguna saat konten habis | Jangan biarkan pengguna scroll tanpa kejelasan |
| Implement lazy loading untuk gambar | Hemat bandwidth, tingkatkan performance |
| Simpan state scroll di URL | Pengguna bisa kembali ke posisi yang sama |
| Debounce network requests | Hindari request duplikat saat scroll cepat |
| Tambahkan tombol "Back to Top" | UX dan accessibility |
| Test dengan keyboard-only navigation | Pastikan semua item bisa diakses |

### ❌ Don'ts

| Yang Harus Dihindari | Alasan |
|---|---|
| Gunakan event listener `scroll` tanpa throttle | Sangat boros resource, menyebabkan jank |
| Autoload konten tanpa indikator loading | Pengguna bingung apakah ada konten baru |
| Lupakan error handling | Jaringan bisa gagal; tampilkan pesan error dan tombol retry |
| Infinite scroll di semua jenis konten | Tidak cocok untuk konten yang butuh navigasi spesifik |
| Remove/replace DOM nodes yang sudah ada | Menyebabkan masalah aksesibilitas dan perubahan layout |
| Lupa unobserve gambar setelah load | Memory leak pada IntersectionObserver |
| Block main thread saat memproses data | Gunakan microtask atau Web Workers untuk data besar |
| Abaikan jaringan lambat | Tambahkan progressive loading dan skeleton |
| Infinite scroll tanpa batas halaman | Berikan opsi "stop" atau "load more" secara eksplisit |
| Pindahkan focus ke item baru secara otomatis | Mengganggu alur baca pengguna screen reader |

---

## Implementasi: Hybrid Approach (Direkomendasikan)

Pendekatan terbaik adalah **Hybrid**: infinite scroll untuk pengguna dengan mouse dan layar sentuh, namun juga menyediakan tombol "Muat Lebih" yang selalu terlihat untuk pengguna keyboard dan yang lebih suka kontrol eksplisit.

```
┌─────────────────────────────────┐
│         Content Grid            │
│  [Item 1] [Item 2] [Item 3]    │
│  [Item 4] [Item 5] [Item 6]    │
│                                 │
│  ···············sentinel········│  ← IntersectionObserver
│                                 │
│  [⟳ Memuat 6 item lagi...]    │  ← Loading indicator
│                                 │
│  ─ ATAU ─                       │
│                                 │
│  [ Muat Lebih (12 tersisa) ]   │  ← Fallback button
└─────────────────────────────────┘
```

---

## Contoh dari Produk Nyata

### 🐦 Twitter/X
- Feed utama menggunakan infinite scroll murni
- Ada tombol "X tweet baru" di atas saat ada update
- **Pelajaran:** Tetap berikan kontrol kepada pengguna untuk kapan memuat konten baru

### 📷 Instagram
- Grid explore dan feed menggunakan infinite scroll
- Gambar di-lazy load secara agresif (placeholder blur)
- **Pelajaran:** Blur placeholder (LQIP - Low Quality Image Placeholder) meningkatkan persepsi kecepatan

### 🛍️ Tokopedia / Shopee
- Hybrid approach: scroll untuk load, tapi ada counter "menampilkan 1-48 dari 500 produk"
- Filter tetap sticky di atas
- **Pelajaran:** Selalu tunjukkan berapa banyak konten masih tersisa

### 📌 Pinterest
- Masonry layout dengan infinite scroll — ikonik
- Setiap pin punya URL sendiri (preserves deep linking)
- **Pelajaran:** URL per item sangat penting untuk shareability

### 📰 Medium
- Artikel recommended di bawah artikel yang dibaca
- Bukan infinite scroll murni — setiap artikel punya batas yang jelas
- **Pelajaran:** Context matters; editorial content butuh closure, bukan scroll tanpa akhir

---

## Pertimbangan Performa

```
Metrik yang perlu dimonitor:
├── FID (First Input Delay) — scroll listener yang buruk bisa merusaknya
├── CLS (Cumulative Layout Shift) — gambar tanpa dimensi menyebabkan layout jump
├── LCP (Largest Contentful Paint) — lazy load yang terlalu agresif bisa menundanya
└── Memory Usage — DOM yang terus membesar bisa menyebabkan memory leak
```

### Teknik Virtualisasi (untuk list sangat panjang)
Jika konten sangat banyak (ribuan item), pertimbangkan **virtual scrolling** — hanya render item yang ada di viewport:
- React: `react-window`, `react-virtual`
- Vue: `vue-virtual-scroller`
- Vanilla: Intersection Observer + DOM recycling

---

## Referensi

- [WCAG 2.1 - Understanding SC 1.3.1: Info and Relationships](https://www.w3.org/WAI/WCAG21/Understanding/info-and-relationships.html)
- [MDN Web Docs - Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
- [Web.dev - Lazy Loading Images](https://web.dev/lazy-loading-images/)
- [NNGroup - Infinite Scrolling Is Not for Every Website](https://www.nngroup.com/articles/infinite-scrolling-tips/)
- [ARIA Authoring Practices Guide - Feed Pattern](https://www.w3.org/WAI/ARIA/apg/patterns/feed/)
- [Smashing Magazine - Infinite Scrolling Best Practices](https://www.smashingmagazine.com/2022/03/designing-better-infinite-scroll/)
- [Web Almanac 2023 - Performance](https://almanac.httparchive.org/en/2023/performance)

---

*Pattern ini dibuat sebagai bagian dari [UI/UX Patterns Library](../../README.md) — koleksi pattern desain UI/UX dalam Bahasa Indonesia.*
