# Multi-Step Form (Formulir Multi-Langkah / Wizard)

## Deskripsi

Multi-Step Form (juga dikenal sebagai Wizard atau Stepper Form) adalah pattern UI/UX yang memecah formulir panjang dengan banyak kolom input (fields) menjadi beberapa langkah berurutan yang lebih pendek dan terkelompok secara logis.

Alih-alih menyajikan puluhan input dalam satu halaman panjang yang mengintimidasi, pattern ini memandu pengguna menyelesaikan tugas pengisian data secara bertahap. Setiap langkah fokus pada satu kategori informasi tertentu, dilengkapi dengan indikator kemajuan (stepper/progress bar) yang menunjukkan seberapa jauh pengguna telah berjalan dan berapa langkah yang tersisa.

Secara psikologis, pattern ini memanfaatkan **Hick's Law** (mengurangi jumlah pilihan/keputusan dalam satu waktu untuk menurunkan cognitive load) dan prinsip **Chunking** dalam psikologi kognitif untuk meningkatkan tingkat penyelesaian formulir (conversion/completion rate) dan mengurangi tingkat stress/frustrasi pengguna.

---

## Kapan Menggunakan Multi-Step Form

### ✅ Gunakan Multi-Step Form Ketika:
* **Jumlah input terlalu banyak** — Biasanya jika ada lebih dari 5-7 kolom input yang harus diisi.
* **Informasi dapat dikelompokkan secara jelas** — Misalnya pada proses checkout e-commerce (Informasi Kontak ➔ Pengiriman ➔ Pembayaran ➔ Ringkasan/Konfirmasi).
* **Proses membutuhkan waktu/perencanaan** — Pengisian pajak, aplikasi kredit, pembuatan asuransi, atau pendaftaran bisnis yang membutuhkan dokumen pendukung di langkah tertentu.
* **Proses onboarding pengguna baru** — Di mana aplikasi perlu mengumpulkan preferensi pengguna secara bertahap setelah akun dibuat.
* **Memerlukan validasi bertahap** — Validasi keamanan atau kelayakan di awal (misal: cek eligibility) sebelum melangkah ke pengisian data sensitif.

### ❌ Jangan Gunakan Multi-Step Form Ketika:
* **Formulir sangat pendek** — Jika hanya ada kurang dari 4-5 input (misalnya: form login biasa, form ganti password, atau newsletter signup).
* **Navigasi antar langkah terlalu kaku dan tidak menyimpan draf** — Mengakses tombol kembali (back) menghapus data yang telah diinput sebelumnya.
* **Urutan input tidak relevan atau dinamis secara acak** — Jika pengguna harus bebas mengisi input bagian mana pun terlebih dahulu tanpa urutan logis.
* **Tugas membutuhkan pembandingan cepat** — Jika data di langkah terakhir terus membutuhkan referensi visual langsung dari input di langkah pertama yang tersembunyi.

---

## Anatomi Multi-Step Form

```
┌────────────────────────────────────────────────────────┐
│  [1. Akun] ──── (2. Profil) ──── [3. Konfirmasi]       │  ← Progress Indicator
├────────────────────────────────────────────────────────┤
│  Langkah 2 dari 3: Profil Bisnis                       │  ← Sub-Header & Context
│                                                        │
│  Nama Perusahaan *                                     │
│  [ Input text...                                    ]  │  ← Form Body (Input area)
│                                                        │
│  Ukuran Tim                                            │
│  ( ) 1-10 orang   (o) 11-50 orang   ( ) 50+ orang      │
│                                                        │
├────────────────────────────────────────────────────────┤
│  [ Kembali ]                         [ Selanjutnya ]   │  ← Navigation Buttons
└────────────────────────────────────────────────────────┘
```

### Komponen Utama:
1. **Progress Indicator (Stepper)**
   * **Visual Tracker**: Menunjukkan daftar langkah, statusnya (selesai, sedang aktif, atau belum mulai), dan total langkah. Bisa berupa nomor, ikon, baris diagram, atau persentase visual.
2. **Context & Header**
   * Memberikan judul langkah yang eksplisit agar pengguna tahu persis fokus informasi yang sedang diisi saat ini (contoh: "Informasi Pengiriman").
3. **Form Body (Area Input)**
   * Area pengisian input yang relevan dengan langkah aktif. Idealnya dibatasi hanya memuat input esensial saja.
4. **Navigation Buttons**
   * **Utama (Selanjutnya/Next)**: Tombol aksi utama untuk memvalidasi langkah aktif dan beralih ke langkah berikutnya.
   * **Sekunder (Kembali/Back)**: Memungkinkan pengguna melihat atau merevisi input sebelumnya tanpa menghapus draf pengisian saat ini.
   * **Aksi Akhir (Kirim/Submit)**: Tombol konfirmasi final yang hanya muncul di langkah terakhir.

---

## Best Practices dalam Desain

### 1. Chunking Informasi Secara Logis
Bagi input ke dalam kategori yang saling berdekatan secara fungsional.
* *Buruk*: Menaruh input "Nama Depan", "Metode Pembayaran", "Alamat Rumah", dan "Nomor Kartu Kredit" di langkah yang sama.
* *Baik*: Kelompokkan menjadi langkah "Informasi Akun", "Alamat Pengiriman", dan "Detail Pembayaran".

### 2. Sediakan Indikator Kemajuan (Progress Indicator) yang Jelas
* **Untuk Kuesioner Pendek (2-4 langkah)**: Gunakan penomoran visual yang jelas (1 ➔ 2 ➔ 3).
* **Untuk Formulir Panjang / Gamifikasi (>5 langkah)**: Gunakan progress bar linear (persentase) guna menghindari kesan bahwa formulir terlalu panjang.
* Pastikan stepper responsif. Pada layar mobile, visualisasi stepper horizontal biasanya perlu disederhanakan (misal cukup teks: `Langkah 2 dari 5`).

### 3. Simpan Draf Otomatis (Auto-Save / Draft Persistence)
Guna menjaga kenyamanan pengguna jika terjadi kegagalan jaringan atau refresh tidak sengaja:
* Simpan data formulir secara lokal sementara (misalnya menggunakan `localStorage` atau `sessionStorage` di sisi browser).
* Izinkan tombol "Kembali" berfungsi dengan lancar tanpa memaksa pengguna mengetik ulang input yang sudah diisi.

### 4. Tombol Navigasi yang Konsisten dan Jelas
* Selalu posisikan tombol navigasi di area yang sama di setiap langkah (biasanya kanan bawah untuk "Selanjutnya" dan kiri bawah untuk "Kembali").
* Bedakan gaya visual tombol secara hierarkis (tombol "Selanjutnya" menggunakan warna kontras tinggi, tombol "Kembali" menggunakan gaya garis terluar/outline atau teks polos).

### 5. Validasi Real-time Sebelum Pindah ke Langkah Berikutnya
* Jangan biarkan pengguna berpindah ke langkah selanjutnya jika ada kegagalan input kritis pada langkah yang sedang aktif.
* Lakukan validasi per lapangan (inline-validation) secara asinkron atau saat perpindahan langkah untuk mencegah kebingungan saat submit final.

### 6. Summary / Review Step (Langkah Peninjauan)
* Sebelum submit final, berikan ringkasan seluruh data yang telah diisi pada satu tempat.
* Sediakan tombol pintas seperti "Edit" atau "Ubah" di samping ringkasan informasi agar pengguna bisa langsung melompat kembali ke langkah terkait tanpa harus menekan tombol "Kembali" berkali-kali.

---

## Accessibility (Aksesibilitas) Considerations

Aksesibilitas adalah tantangan terbesar pada Multi-Step Form karena perubahan konten halaman terjadi secara asinkron tanpa memuat ulang (reload) halaman secara penuh.

### 1. Hubungkan Stepper dengan Screen Reader
* Gunakan elemen daftar HTML `<ol>` atau `<ul>` untuk pembungkus stepper.
* Tambahkan atribut `aria-current="step"` pada langkah yang saat ini sedang aktif agar pembaca layar tahu posisi pengguna dalam rentang proses.
* Gunakan label deskriptif yang jelas pada tombol langkah (Contoh: `aria-label="Langkah 1: Hubungi akun anda, Selesai"`).

### 2. Manajemen Fokus (Focus Management)
Saat pengguna menekan tombol "Selanjutnya" atau "Kembali":
* Pengguna pembaca layar (screen reader) dan keyboard perlu tahu bahwa layar telah bergeser ke langkah baru.
* **Pindahkan fokus secara terprogram (programmatic focus)** menggunakan JavaScript ke elemen judul langkah baru (seperti header `<h3>` dengan atribut `tabindex="-1"`) atau ke elemen input pertama yang harus diisi di langkah tersebut.
* Tanpa ini, fokus keyboard akan tetap tertahan di tombol "Selanjutnya" yang baru saja di-klik, sehingga pengguna screen reader tidak sadar jika form di atasnya telah berubah.

### 3. Pemberitahuan Status Dinamis (Live Regions)
* Sediakan elemen dengan `role="status"` atau `aria-live="polite"` untuk mengumumkan keberhasilan pergantian langkah atau ringkasan error.
* Contoh: *"Menampilkan langkah 2 dari 3: Profil Bisnis."*

### 4. Validasi dan Error Formatting
* Hubungkan pesan error dengan input terkait secara semantik menggunakan `aria-describedby="error-element-id"`.
* Labeli input yang wajib diisi dengan atribut `required` atau `aria-required="true"`.
* Pastikan kontras warna antara teks error dan latar belakang memenuhi rasio minimal WCAG AA (kepekatan warna minimal 4.5:1).

### 5. Dukungan prefers-reduced-motion
* Jika menggunakan animasi transisi (seperti slide-in/fade) saat berganti langkah, hormati setelan sistem operasi pengguna dengan mematikan animasi jika media query `@media (prefers-reduced-motion: reduce)` terdeteksi aktif.

---

## Do's dan Don'ts

### ✅ DO:
1. **Tunjukkan ringkasan di akhir**: Beri kesempatan pengguna meninjau ulang alamat pengiriman atau detail pembayaran sebelum bertransaksi.
2. **Pertahankan input pengguna**: Jika pengguna menekan "Kembali", input yang mereka masukkan wajib tetap ada di sana.
3. **Format responsif**: Ubah visualisasi stepper di mobile agar tidak merusak tata letak layar kecil.
4. **Berikan konfirmasi kesuksesan yang melegakan**: Setelah submit akhir selesai, tampilkan halaman sukses mandiri yang ramah dan jelaskan langkah tindak lanjut selanjutnya.
5. **Gunakan warna semantik**: Berikan status visual (hijau untuk centang/selesai, biru untuk aktif, abu-abu untuk belum aktif).

### ❌ DON'T:
1. **Jangan melompatkan langkah secara paksa**: Hindari memaksa pengguna mengisi formulir langkah 3 jika langkah 1 dan 2 belum diisi/ divalidasi.
2. **Jangan menyembunyikan navigasi kembali**: Menghilangkan tombol "Kembali" membuat pengguna merasa terjebak.
3. **Jangan gunakan stepper horizontal di layar sempit**: Di mobile, stepper horizontal yang dipaksa akan berhimpitan dan tidak terbaca.
4. **Jangan membuat form terlalu banyak langkah**: Jika formulir dipotong menjadi terlalu banyak langkah kecil (misalnya 10 langkah dengan masing-masing hanya 1 input), pengguna akan merasa lelah dengan klik navigasi yang berulang-ulang (form completion fatigue).

---

## Variasi Pattern

### 1. Stepper Horizontal
* **Penggunaan**: Sangat baik di layar desktop yang lebar.
* **Layout**: Alur proses ditempatkan di atas formulir secara mendatar dari kiri ke kanan.

### 2. Stepper Vertikal (Sidebar)
* **Penggunaan**: Cocok untuk aplikasi dashboard atau antarmuka dengan banyak instruksi tambahan di sisi kanan.
* **Layout**: Alur navigasi ditempatkan di panel samping kiri secara menurun dari atas ke bawah.

### 3. Accordion Wizard
* **Penggunaan**: Sering dipakai di antarmuka seluler atau halaman checkout terpadu.
* **Layout**: Setiap langkah direpresentasikan sebagai panel akordeon. Saat melangkah ke fase berikutnya, panel sebelumnya menutup dan panel baru membuka ke bawah secara halus.

### 4. Form Single-Question-at-a-Time (Conversational Form)
* **Penggunaan**: Populer untuk survei, kuesioner, atau onboarding produk bergaya interaktif (contoh: Typeform).
* **Layout**: Hanya menampilkan satu kolom input di layar pada satu waktu dengan transisi navigasi otomatis.

---

## Contoh Real-World

* **Airbnb**: Proses pendaftaran properti baru dibagi menjadi langkah-langkah intuitif dengan video tutorial, visualisasi peta interaktif, dan penyimpanan otomatis berkala.
* **Shopify Checkout**: Alur checkout 3-langkah (Informasi Kontak ➔ Pengiriman ➔ Pembayaran) adalah standar emas konversi e-commerce global yang sangat efektif.
* **TurboTax**: Panduan pelaporan pajak yang sangat rumit diubah menjadi serangkaian kuesioner bergaya percakapan bernarasi ramah, menyembunyikan kompleksitas formula perhitungan matematika di balik layar.
* **Stripe**: Alur pengaktifan akun bisnis Stripe (onboarding) yang sangat panjang dapat diisi bertahap, disimpang sebagai draf, dan dilanjutkan kapan saja pengguna siap.

---

## Metrik & Kriteria Keberhasilan

Kesesuaian penerapan pattern ini diukur melalui metrik produk berikut:
1. **Drop-off Rate per Step**: Menganalisis pada langkah keberapa pengguna paling banyak keluar dari formulir. Ini membantu mengidentifikasi jika ada langkah yang memuat pertanyaan terlalu membingungkan atau memerlukan unggahan berkas yang rumit.
2. **Time to Completion**: Berapa lama waktu rata-rata yang dihabiskan pengguna untuk menyelesaikan formulir. Form yang terstruktur baik biasanya mempercepat waktu pengisian.
3. **Form Abandonment Rate**: Persentase pengguna yang memulai pengisian formulir namun tidak menyelesaikannya.
4. **Validation Error Rate**: Seberapa sering pengguna memicu pesan validasi kesalahan per langkah. Jika tinggi, tandanya label instruksi kurang memadai.

---

## Changelog

* **2026-06-22**: Menambahkan pattern Multi-Step Form lengkap dengan contoh implementasi interaktif, petunjuk aksesibilitas penuh (focus management & screen reader compatibility), checklist Do's and Don'ts, serta contoh implementasi di industri nyata.
