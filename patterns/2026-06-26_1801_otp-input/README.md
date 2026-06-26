# Input OTP (One-Time Password) yang Aksesibel & Ergonomis

> **Kategori:** Input Form • **Tingkat Kesulitan:** Menengah-Tinggi • **Tanggal:** 26 Juni 2026

---

## Apa itu Panel Input OTP (One-Time Password)?

**Input OTP** adalah pola desain UI/UX formulir khusus yang digunakan untuk mengumpulkan kode verifikasi satu kali waktu (biasanya terdiri dari 4 atau 6 digit angka) yang dikirim melalui SMS, email, atau aplikasi autentikator. 

Berbeda dengan kolom teks input biasa, pola ini mendistribusikan setiap digit angka ke dalam kotak-kotak terpisah. Meski terlihat sederhana, implementasi teknis dan interaksi yang buruk di pola ini sering menjadi **sumber friksi utama (drop-off rate tinggi)** pada alur *onboarding* atau otentikasi karena pengisiannya rentan eror dan sulit diakses.

Pola OTP yang handal meminimalkan beban kognitif dengan mengotomatisasi pemindahan fokus, mendukung penyalinan (copy-paste) secara luwes, mendukung deteksi autofill sistem operasi ponsel, dan ramah terhadap pengguna pembaca layar (*screen reader*).

---

## Mengapa Pattern Ini Penting?

### Masalah yang Sering Terjadi pada Input OTP Biasa
1. **Fokus Tidak Berpindah Otomatis:** Pengguna harus mengetuk kotak berikutnya secara manual setiap kali mengisi satu digit. Hal ini memperlambat proses secara signifikan.
2. **Keterbatasan Paste (Salin-Tempel):** Ketika pengguna menyalin kode OTP 6-digit dari SMS/Email lalu menempelkannya (paste), hanya digit pertama yang terisi, atau kodenya menduplikasi kolom tersebut. Pengguna terpaksa menghafal atau mengetik bolak-balik.
3. **Penanganan Backspace yang Buruk:** Saat salah ketik dan ingin menghapus digit sebelumnya, pengguna harus mengetuk kotak kiri atau menekan tombol hapus tanpa perpindahan fokus mundur secara otomatis.
4. **Masalah Aksesibilitas (A11y):** Screen reader seringkali tidak menyuarakan nomor kolom dengan jelas, sehingga pengguna dengan disabilitas netra tidak tahu digit mana yang sedang diisi atau di mana letak fokus mereka.
5. **Autofill Gagal:** Tidak ada konfigurasi atribut autofill yang tepat sehingga sistem keyboard ponsel (iOS/Android) tidak menawarkan kode OTP langsung dari SMS yang baru masuk.

### Statistik & Dampak UX
- Berdasarkan riset konversi Checkout, **UX OTP yang buruk menyumbang hingga 15% dari total pembatalan transaksi (drop-off)** di halaman verifikasi pembayaran.
- Implementasi `autocomplete="one-time-code"` terbukti meningkatkan tingkat kecepatan penyelesaian tugas otentikasi hingga **35%** pada perangkat seluler karena pengguna tidak perlu membuka aplikasi pesan untuk menyalin kode.

---

## Use Case yang Ideal

### ✅ Cocok untuk:
- **Autentikasi Dua Faktor (2FA):** Login akun penting, penarikan dana, atau perubahan kata sandi.
- **Onboarding Aplikasi Baru:** Verifikasi nomor telepon atau alamat email pendaftaran.
- **Otorisasi Transaksi Finansial:** Konfirmasi transaksi di e-commerce, top-up e-wallet, atau aplikasi perbankan.

### ❌ Kurang cocok untuk:
- Kolom input angka biasa yang bukan verifikasi (misal: kode pos, nomor telepon, Nomor Induk Kependudukan). Gunakan standard input dengan `inputmode="numeric"` tanpa memecahnya per kotak.
- Kode dengan panjang dinamis atau panjang lebih dari 8 digit (sebaiknya gunakan single input field berkapasitas besar).

---

## Implementasi Teknis Terbaik

### 1. Struktur HTML (Semantic & Accessible)
Setiap kotak diberi nama unik, memiliki deskripsi `aria-label` yang spesifik, serta `inputmode="numeric"` untuk memicu kemunculan keyboard angka saja di mobile.

```html
<div class="otp-inputs" role="group" aria-labelledby="otp-group-label" id="otp-group">
  <input type="text" id="otp-input-1" class="otp-input" maxlength="1" pattern="[0-9]" inputmode="numeric" autocomplete="one-time-code" aria-label="Digit ke-1 dari 6" autofocus required>
  <input type="text" id="otp-input-2" class="otp-input" maxlength="1" pattern="[0-9]" inputmode="numeric" aria-label="Digit ke-2 dari 6" required>
  <input type="text" id="otp-input-3" class="otp-input" maxlength="1" pattern="[0-9]" inputmode="numeric" aria-label="Digit ke-3 dari 6" required>
  <input type="text" id="otp-input-4" class="otp-input" maxlength="1" pattern="[0-9]" inputmode="numeric" aria-label="Digit ke-4 dari 6" required>
  <input type="text" id="otp-input-5" class="otp-input" maxlength="1" pattern="[0-9]" inputmode="numeric" aria-label="Digit ke-5 dari 6" required>
  <input type="text" id="otp-input-6" class="otp-input" maxlength="1" pattern="[0-9]" inputmode="numeric" aria-label="Digit ke-6 dari 6" required>
</div>
```

*Catatan Penting:* Gunakan `type="text"` (atau `type="tel"`) dengan `inputmode="numeric"` dan `pattern="[0-9]"` alih-alih `type="number"`. Input type number memiliki perilaku bawaan browser yang buruk seperti memperbolehkan karakter scroll wheel, tombol panah atas/bawah memperbesar nilai, dan bug panjang karakter pada beberapa mobile browser.

### 2. Styling CSS (Ergonomis & Responsif)
Kotak harus berukuran cukup besar, memiliki status fokus yang kontras tinggi, dan secara visual beradaptasi baik di layar ponsel yang sempit.

```css
.otp-inputs {
  display: flex;
  gap: 12px;
  justify-content: space-between;
}

.otp-input {
  width: 100%;
  height: 60px;
  outline: none;
  border: 2px solid var(--border-color);
  border-radius: 12px;
  text-align: center;
  font-size: 1.75rem;
  font-weight: 700;
  color: var(--text-main);
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}

.otp-input:focus {
  border-color: var(--border-focus);
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.15);
}

/* Modifikasi input tersembunyi jika ada opsi privasi */
.otp-input.secure-masked {
  -webkit-text-security: disc; /* Mendukung sembunyikan karakter di Chrome/Safari */
}
```

### 3. Logika JavaScript (Mekanisme Keyboard & Paste)
Script menangani perpindahan fokus maju, mundur via Backspace, navigasi panah kiri/kanan, mencegah pengetikan non-angka, serta mengurai masukan paste agar terdistribusi dengan benar.

```javascript
const inputs = document.querySelectorAll('.otp-input');

inputs.forEach((input, index) => {
  // Hanya izinkan angka dan pindahkan fokus ke depan
  input.addEventListener('input', (e) => {
    const val = e.target.value;
    e.target.value = val.replace(/[^0-9]/g, ''); // Hanya izinkan angka

    if (e.target.value.length === 1 && index < inputs.length - 1) {
      inputs[index + 1].focus();
    }
  });

  // Deteksi Backspace untuk kembali ke kolom sebelumnya
  input.addEventListener('keydown', (e) => {
    if (e.key === 'Backspace') {
      if (e.target.value.length === 0 && index > 0) {
        inputs[index - 1].focus();
        inputs[index - 1].value = '';
      } else {
        e.target.value = '';
      }
      e.preventDefault();
    } else if (e.key === 'ArrowLeft' && index > 0) {
      inputs[index - 1].focus();
      e.preventDefault();
    } else if (e.key === 'ArrowRight' && index < inputs.length - 1) {
      inputs[index + 1].focus();
      e.preventDefault();
    }
  });

  // Tangani Paste Komprehensif
  input.addEventListener('paste', (e) => {
    e.preventDefault();
    const pasteData = (e.clipboardData || window.clipboardData).getData('text').trim();
    const digits = pasteData.replace(/[^0-9]/g, '').split('');

    if (digits.length > 0) {
      digits.forEach((digit, i) => {
        if (index + i < inputs.length) {
          inputs[index + i].value = digit;
        }
      });
      // Pindahkan fokus ke input kosong berikutnya
      const nextEmpty = Array.from(inputs).findIndex(inp => inp.value === '');
      if (nextEmpty !== -1) {
        inputs[nextEmpty].focus();
      } else {
        inputs[inputs.length - 1].focus();
      }
    }
  });
});
```

---

## Accessibility Considerations (Aksesibilitas)

Mengacu pada standar **WCAG 2.1 Level AA**:

1. **Focus State yang Jelas (WCAG 2.4.7):** Indikator fokus harus terlihat sangat kontras. Gunakan kombinasi perubahan warna border yang tajam dan `box-shadow` lembut (outline ring) agar terlihat oleh penderita gangguan visual minor.
2. **Keterbacaan bagi Screen Reader (WCAG 1.3.1 & 4.1.2):**
   - Bungkus kumpulan input dalam kontainer ber- `role="group"` dan hubungkan dengan label utama menggunakan `aria-labelledby`.
   - Setiap kolom wajib memiliki atribut `aria-label` yang mengumumkan posisi digit (contoh: `"Digit ke-1 dari 6"`) bukan sekadar pelafalan teks hambar.
3. **Status Feedback yang Dinamis (WCAG 4.1.3):**
   Kontainer untuk pesan error atau sukses harus memiliki atribut `role="status"` dan `aria-live="polite"`. Dengan cara ini, ketika hasil verifikasi muncul (baik sukses maupun gagal), pembaca layar akan segera membacakannya tanpa mengalihkan fokus kursor pengguna.
4. **Touch Target Size (WCAG 2.5.5):** Di layar ponsel, pastikan setiap kotak berukuran minimal `48px x 48px` (lebih direkomendasikan `50px` hingga `60px` tinggi) serta memiliki jarak antarkotak yang cukup lebar agar tidak terjadi salah ketuk jari.

---

## Do's and Don'ts

### ✅ DO
- **Gunakan `inputmode="numeric"`:** Menjamin keyboard angka muncul secara otomatis di perangkat Android & iOS tanpa memaksa pengguna mengganti jenis keyboard manual.
- **Gunakan `autocomplete="one-time-code"`:** Penting untuk integrasi system-level autofill. Ketika SMS berisi OTP masuk, sistem operasi seluler akan mengekstraksi kode dan menampilkannya di atas keyboard untuk dimasukkan dalam satu ketukan.
- **Sediakan Tombol Kirim Ulang dengan Delay Timer:** Jangan perbolehkan pengguna mengirim ulang OTP secara bertubi-tubi. Berikan hitung mundur mundur (misalnya 30 - 60 detik) untuk mencegah spam API dan mendidik kesabaran pengguna.
- **Sediakan Opsi Sembunyikan/Tampilkan Karakter (Masking):** Bagi pengguna di tempat umum (*shoulder surfing*), kemampuan menyembunyikan angka OTP dengan lingkaran disk hitam/bintang sangat berharga.

### ❌ DON'T
- **Jangan gunakan input bertipe `type="number"`:** Masalah panah scroll wheel, pemblokiran e-paste, dan bug perilaku mobile browser menjadikannya pilihan buruk.
- **Jangan memblokir tombol klik mouse pada kotak:** Biarkan pengguna yang menggunakan mouse/trackpad mengklik kotak manapun secara bebas jika ingin memperbaiki satu digit di tengah secara manual.
- **Jangan gunakan nested forms:** Jangan bungkus input OTP di dalam tag form yang tumpang tindih.
- **Jangan hapus fokus otomatis pasca submit gagal:** Saat kode dinyatakan salah oleh API, jangan biarkan input kosong tanpa fokus. Kosongkan kembali seluruh kotak dan arahkan kursor fokus otomatis ke digit pertama untuk meningkatkan kecepatan koreksi.

---

## Contoh dari Produk Nyata

1. **Stripe:** Memiliki alur autodetect OTP super cepat. Kotak input terisi otomatis ketika kode disalin dari clipboard atau melalui fitur autofill SMS Safari di Mac/iOS, memiliki transisi loading di dalam tombol secara *seamless*.
2. **Apple (iOS AutoFill):** Penerapan `autocomplete="one-time-code"` yang mulus pada iOS Safari yang mendeteksi SMS OTP masuk dan menawarkan pengisian dalam satu ketuk di bagian atas keyboard.
3. **Tokopedia / Shopee:** Menggunakan panel mini 6-digit OTP berdesain bersih dengan *countdown timer* interaktif untuk menghindari pengiriman ganda dan memberikan umpan balik status verifikasi instan.

---

## Referensi & Bacaan Lanjutan

- [W3C - Web Accessibility Initiative (WAI) Form Tutorials](https://www.w3.org/WAI/tutorials/forms/)
- [Apple Developer Documentation - Input Autocomplete for One-Time Codes](https://developer.apple.com/documentation/security/password_autofill/enabling_password_autofill_on_an_html_input_element)
- [Caniuse - Input Web-Text-Security Property](https://caniuse.com/css-text-security)
- [Google Web Dev - SMS Receiver API & One-Time Code Guidelines](https://web.dev/web-otp/)
