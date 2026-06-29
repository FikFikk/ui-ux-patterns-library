# Empty States

## Apa itu Empty States?

Empty states adalah tampilan yang muncul ketika tidak ada data atau konten untuk ditampilkan kepada pengguna. Ini bukan kesalahan, melainkan kondisi normal dalam siklus hidup aplikasi — misalnya saat pengguna baru pertama kali membuka aplikasi, setelah menghapus semua item, atau ketika filter pencarian tidak menghasilkan hasil.

## Mengapa Penting?

Empty states adalah kesempatan emas untuk:
- **Mengedukasi** pengguna tentang cara menggunakan fitur
- **Memotivasi** pengguna untuk mengambil tindakan pertama
- **Mengurangi kebingungan** dan meningkatkan kepercayaan diri
- **Membedakan** produk Anda dari kompetitor yang mengabaikan detail ini

Penelitian menunjukkan bahwa empty states yang dirancang dengan baik dapat meningkatkan conversion rate hingga 40% dan mengurangi churn rate pada pengguna baru.

## Jenis-jenis Empty States

### 1. First Use (Penggunaan Pertama)
Terjadi saat pengguna belum pernah membuat atau menambahkan data apapun.

**Contoh:**
- Inbox email kosong untuk akun baru
- Dashboard tanpa proyek
- Keranjang belanja kosong

**Tujuan:** Onboarding dan panduan untuk langkah pertama

### 2. User Cleared (Pengguna Mengosongkan)
Pengguna sengaja menghapus semua item atau mencapai "inbox zero".

**Contoh:**
- Notifikasi yang sudah dibaca semua
- To-do list yang sudah selesai semua

**Tujuan:** Validasi pencapaian, beri penghargaan

### 3. No Results (Tidak Ada Hasil)
Filter, pencarian, atau query tidak menghasilkan hasil.

**Contoh:**
- Pencarian produk tidak ditemukan
- Filter terlalu spesifik

**Tujuan:** Bantu pengguna menyesuaikan kriteria atau tawarkan alternatif

### 4. Error State (Kondisi Error)
Konten seharusnya ada tapi gagal dimuat karena masalah teknis.

**Contoh:**
- Koneksi internet terputus
- Server error

**Tujuan:** Jelaskan masalah dan berikan solusi jelas

## Elemen-elemen Empty State yang Efektif

### 1. Visual yang Relevan
- Ilustrasi, ikon, atau gambar yang menjelaskan konteks
- Hindari terlalu kompleks atau generik
- Sesuaikan dengan brand personality

### 2. Headline yang Jelas
- Jelaskan kondisi dengan bahasa sederhana
- Hindari jargon teknis atau pesan error kode
- Contoh baik: "Belum ada proyek" vs "Database empty"

### 3. Deskripsi Singkat (Opsional)
- Jelaskan mengapa kondisi ini terjadi
- Berikan konteks yang membantu
- Maksimal 1-2 kalimat

### 4. Call-to-Action (CTA)
- **Wajib** untuk first use dan no results
- Buat jelas dan actionable
- Primary button untuk tindakan utama
- Secondary link untuk bantuan atau dokumentasi

### 5. Educational Content (Opsional)
- Tips singkat atau value proposition
- Screenshot atau video tutorial
- Link ke dokumentasi atau help center

## Best Practices

### ✅ Do's

1. **Berikan Arah yang Jelas**
   - Gunakan CTA button yang spesifik: "Buat Proyek Pertama" bukan "Mulai"
   - Tunjukkan langkah berikutnya dengan jelas

2. **Gunakan Tone yang Positif**
   - "Siap memulai?" bukan "Tidak ada data"
   - Hindari membuat pengguna merasa gagal

3. **Desain Konsisten dengan Brand**
   - Gunakan ilustrasi style yang sama dengan produk
   - Pertahankan voice & tone brand

4. **Optimalkan untuk Mobile**
   - Ilustrasi tetap terlihat baik di layar kecil
   - CTA mudah di-tap (min 44x44px)

5. **Berikan Konteks yang Relevan**
   - First use: fokus pada onboarding
   - No results: tawarkan cara perbaiki pencarian
   - Error: jelaskan apa yang salah dan cara fix

6. **Ukur dan Iterasi**
   - Track conversion rate dari empty state ke first action
   - A/B test berbagai copy dan CTA

### ❌ Don'ts

1. **Jangan Abaikan Empty States**
   - Layar kosong polos membingungkan dan unprofessional

2. **Jangan Gunakan Pesan Error Teknis**
   - "NULL dataset returned" → "Belum ada data"
   - Tulis untuk manusia, bukan developer

3. **Jangan Overwhelm dengan Terlalu Banyak Pilihan**
   - Fokus pada 1-2 action utama
   - Hindari decision paralysis

4. **Jangan Gunakan Ilustrasi Generic**
   - Hindari stock illustration yang tidak relevan
   - Sesuaikan dengan konteks spesifik

5. **Jangan Blame User**
   - "Kamu belum melakukan apapun" → "Siap memulai?"
   - Buat empowering, bukan accusatory

6. **Jangan Lupakan Accessibility**
   - Alt text untuk ilustrasi
   - Sufficient color contrast
   - Keyboard navigation untuk CTA

## Accessibility Considerations

### ARIA Labels
```html
<div role="status" aria-live="polite" aria-label="Tidak ada item ditemukan">
```

### Semantic HTML
- Gunakan `<article>` atau `<section>` untuk wrapper
- `<h2>` atau `<h3>` untuk headline
- `<button>` untuk CTA, bukan `<div>` dengan click handler

### Keyboard Navigation
- CTA button harus focusable dan aktivasi dengan Enter/Space
- Tab order yang logical

### Screen Reader Friendly
- Deskripsi alt text yang meaningful untuk ilustrasi
- Hindari icon-only button tanpa label text

### Color Contrast
- Minimum 4.5:1 untuk text
- Minimum 3:1 untuk interactive elements

## Implementasi Teknis

### Loading vs Empty State
Bedakan dengan jelas:
```javascript
if (loading) {
  return <SkeletonLoader />
}
if (error) {
  return <ErrorState />
}
if (data.length === 0) {
  return <EmptyState />
}
return <DataList data={data} />
```

### Conditional Rendering
```javascript
const EmptyState = ({ type, onAction }) => {
  const config = {
    'first-use': {
      title: 'Belum ada proyek',
      description: 'Buat proyek pertama Anda dan mulai berkolaborasi',
      ctaText: 'Buat Proyek Baru',
      illustration: 'first-project.svg'
    },
    'no-results': {
      title: 'Tidak ditemukan',
      description: 'Coba gunakan kata kunci lain atau kurangi filter',
      ctaText: 'Reset Filter',
      illustration: 'search-empty.svg'
    }
  }
  
  const content = config[type]
  return <EmptyStateUI {...content} onAction={onAction} />
}
```

### Animation (Opsional)
Gunakan subtle animation untuk menarik perhatian tanpa mengganggu:
```css
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.empty-state {
  animation: fadeInUp 0.5s ease-out;
}
```

## Contoh dari Produk Nyata

### 1. **Dropbox** — First Use
- Ilustrasi folder kosong yang friendly
- Headline: "Your Dropbox is empty"
- CTA jelas: "Upload files" dengan drag-drop area
- Tips: Menunjukkan berbagai cara upload (drag, button, mobile)

### 2. **Slack** — No Messages
- Ilustrasi plant yang growing (metaphor untuk channel baru)
- Tone positif: "This is the very beginning"
- Educational: Menjelaskan purpose dari channel

### 3. **Spotify** — Empty Playlist
- Ilustrasi vinyl record
- Headline: "Your playlist is empty"
- CTA: "Find songs" yang langsung buka search
- Quick action: Tombol "Import from..." untuk migrasi

### 4. **Airbnb** — No Search Results
- Foto destinations alternatif
- Headline: "No exact matches"
- Helpful suggestions: "Try different dates" atau nearby locations
- Tidak membuat user merasa stuck

### 5. **GitHub** — No Repositories
- Ilustrasi octocat dengan buku
- Educational content: Link ke tutorial "Creating your first repository"
- Multiple CTA: "New repository" dan "Import repository"

## Kapan Menggunakan Micro-interactions

Empty states adalah tempat bagus untuk micro-interactions yang delightful:

- **Hover effects** pada CTA button
- **Subtle parallax** pada ilustrasi (jangan berlebihan)
- **Confetti animation** untuk "cleared all tasks" state
- **Bounce animation** saat empty state pertama kali muncul

**Prinsip:** Enhance, jangan distract. Animation harus mendukung fungsi, bukan sekadar hiasan.

## Checklist Implementasi

- [ ] Identifikasi semua skenario empty state dalam aplikasi
- [ ] Buat copy yang spesifik untuk setiap skenario
- [ ] Desain atau pilih ilustrasi yang relevan
- [ ] Tentukan CTA yang clear dan actionable
- [ ] Implementasi dengan semantic HTML
- [ ] Tambahkan ARIA labels untuk accessibility
- [ ] Test dengan screen reader
- [ ] Verifikasi keyboard navigation
- [ ] Cek responsive di berbagai device
- [ ] A/B test copy dan CTA
- [ ] Monitor conversion metrics

## Referensi

- **Nielsen Norman Group**: "Empty States: What, Why, and How"
- **Material Design Guidelines**: Empty States patterns
- **Apple HIG**: Providing Helpful Empty States
- **Smashing Magazine**: "Designing Empty States"
- **Empty States** (emptystat.es): Koleksi contoh dari berbagai produk
- **Mobbin**: UI pattern library dengan kategori empty states

## Template Sederhana

```
[Ilustrasi/Icon]

[Headline — Bold, 20-24px]
"Belum ada [item]"

[Deskripsi — Regular, 14-16px, optional]
Penjelasan singkat 1-2 kalimat

[Primary CTA Button]

[Secondary Link — optional]
Butuh bantuan?
```

---

**Tips Akhir:** Empty states bukan afterthought — rencanakan dari awal dalam product design. Setiap layar yang bisa menampilkan data, bisa juga kosong. Treat empty states sebagai first impression yang sama pentingnya dengan onboarding.
