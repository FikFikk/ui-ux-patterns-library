# Toast Notification — Feedback Non-Intrusive untuk User Actions

## Deskripsi

Toast Notification (atau Snackbar) adalah pattern UI untuk menampilkan pesan feedback singkat yang muncul sementara di layar tanpa menghalangi interaksi user dengan aplikasi. Pesan ini muncul secara otomatis, bertahan beberapa detik, lalu menghilang sendiri — seperti roti panggang (toast) yang muncul dari pemanggang.

Pattern ini memberikan feedback kepada user tentang hasil dari aksi mereka (berhasil, error, informasi) tanpa mengganggu alur kerja atau memaksa user untuk menutup dialog.

## Kapan Menggunakan Toast Notification

### ✅ Gunakan Toast Ketika:

- **Konfirmasi aksi berhasil** — "File berhasil disimpan", "Email terkirim", "Item ditambahkan ke keranjang"
- **Notifikasi non-critical** — Update background, sync selesai, perubahan setting tersimpan
- **Undo action tersedia** — "Email dihapus" dengan tombol "Undo"
- **Progress singkat selesai** — Upload selesai, download complete
- **Informasi contextual** — "Mode offline aktif", "Koneksi terputus"
- **Pesan yang tidak memerlukan keputusan** — User tidak perlu memilih atau merespons

### ❌ Jangan Gunakan Toast Ketika:

- **Error critical** yang membutuhkan perhatian segera — gunakan modal/dialog
- **Pesan memerlukan konfirmasi user** — gunakan confirmation dialog
- **Informasi penting yang harus dibaca** — toast bisa hilang sebelum user selesai baca
- **Multiple actions diperlukan** — toast terlalu kecil untuk banyak tombol
- **Konten panjang** — max 2 baris teks, lebih dari itu gunakan banner atau dialog
- **Dalam formulir dengan inline validation** — gunakan inline error messages

## Anatomi Toast Notification

```
┌──────────────────────────────────────────────┐
│ [Icon] Pesan singkat feedback                │
│        [Action Button]  [✕ Close]            │
└──────────────────────────────────────────────┘
```

### Komponen Utama:

1. **Container** — Background dengan warna sesuai tipe (success, error, warning, info)
2. **Icon** (opsional) — Visual indicator untuk tipe pesan
3. **Message** — Teks singkat, max 2 baris (40-60 karakter ideal)
4. **Action Button** (opsional) — Misal: "Undo", "View", "Retry"
5. **Close Button** (opsional) — User bisa tutup manual sebelum timeout

## Tipe-Tipe Toast

### 1. Success Toast (Hijau)
- **Kapan**: Aksi berhasil diselesaikan
- **Contoh**: "✓ Perubahan berhasil disimpan"
- **Icon**: Checkmark
- **Durasi**: 3-4 detik

### 2. Error Toast (Merah)
- **Kapan**: Error terjadi tapi tidak critical
- **Contoh**: "✕ Gagal mengirim, coba lagi"
- **Icon**: X atau Alert
- **Durasi**: 5-7 detik (lebih lama untuk dibaca)
- **Action**: Button "Retry" jika memungkinkan

### 3. Warning Toast (Kuning/Orange)
- **Kapan**: Peringatan yang perlu diketahui user
- **Contoh**: "⚠ Koneksi tidak stabil"
- **Icon**: Warning triangle
- **Durasi**: 4-6 detik

### 4. Info Toast (Biru)
- **Kapan**: Informasi netral atau update
- **Contoh**: "ℹ Data telah diperbarui"
- **Icon**: Info circle
- **Durasi**: 3-4 detik

## Best Practices

### Duration (Waktu Tampil)

- **Pesan singkat**: 3-4 detik
- **Pesan dengan action button**: 6-10 detik (beri waktu user untuk bereaksi)
- **Error messages**: 5-7 detik (lebih lama untuk dibaca dan pahami)
- **Rumus umum**: 150-200 ms per kata + 1 detik buffer

### Positioning

**Desktop:**
- **Bottom-left** (paling umum) — Tidak menghalangi header/navigation
- **Bottom-center** — Untuk pesan penting yang butuh perhatian
- **Top-center** — Alternatif, mirip notification banner
- **Top-right** — Biasanya untuk system notifications

**Mobile:**
- **Bottom** dengan margin dari edge (aman dari gesture swipe-up)
- **Top** di bawah status bar untuk pesan penting

### Stacking & Queue

Jika multiple toasts muncul bersamaan:

1. **Stacking** — Tumpuk secara vertikal (max 3-4 toasts)
2. **Queueing** — Tampilkan satu per satu dengan delay
3. **Replace** — Toast baru menggantikan yang lama (untuk update progress)

### Animation

- **Entry**: Slide-in dari bottom/side + fade-in (200-300ms)
- **Exit**: Fade-out + slide-out (200ms)
- **Easing**: `cubic-bezier(0.4, 0.0, 0.2, 1)` untuk smooth, natural

### Accessibility

- **ARIA live regions**: `role="status"` untuk info, `role="alert"` untuk error
- **Screen reader**: Pesan harus dibacakan otomatis tanpa user focus
- **Keyboard**: Jika ada action button, harus bisa di-tab dan di-trigger dengan Enter
- **Focus management**: Toast tidak mengambil focus kecuali ada interaction
- **Motion reduction**: Respect `prefers-reduced-motion` untuk animasi minimal

## Do's and Don'ts

### ✅ DO:

- **Gunakan pesan singkat dan jelas** — "File berhasil dihapus" ✓
- **Satu aksi per toast** — Max 1-2 button
- **Prioritaskan undo action** — Bantu user recover dari mistake
- **Konsisten dalam positioning** — Jangan pindah-pindah tempat
- **Beri jeda antar toast** — Min 1 detik jeda agar tidak overwhelming
- **Gunakan warna semantik** — Hijau = success, Merah = error, dll.

### ❌ DON'T:

- **Jangan gunakan untuk critical errors** — Gunakan modal dialog
- **Jangan terlalu panjang** — Hindari pesan lebih dari 2 baris
- **Jangan terlalu sering** — Spam toast mengganggu user
- **Jangan blocking** — Toast tidak boleh menghalangi interaksi penting
- **Jangan hilang terlalu cepat** — User harus punya waktu baca
- **Jangan gunakan untuk input validation** — Gunakan inline errors

## Implementasi Pattern

Lihat file `example.html` untuk implementasi lengkap dengan HTML, CSS, dan JavaScript vanilla.

## Variasi Pattern

### 1. Snackbar (Material Design)
- Lebih minimalis
- Action button rata kanan
- Max 1 action button
- Muncul dari bottom-center

### 2. Banner Notification
- Lebih persisten (tidak auto-dismiss)
- Full-width di top/bottom
- Untuk pesan lebih penting yang butuh acknowledge

### 3. Floating Notification
- Dengan shadow lebih prominent
- Bisa drag untuk dismiss
- Icon lebih besar
- Lebih cocok untuk desktop apps

### 4. Progress Toast
- Dengan progress bar
- Untuk operasi yang butuh waktu (upload, download)
- Update in-place tanpa spawn toast baru

## Contoh Real-World

### GitHub
- "Copied!" saat copy code snippet
- Simple, bottom-center, 2 detik
- Hijau dengan checkmark

### Gmail
- "Message sent" dengan "Undo"
- Bottom-left, 5 detik dengan action
- Bisa di-undo dalam window tersebut

### Slack
- "Message deleted" dengan undo
- Top-right dengan avatar
- Stacking untuk multiple notifications

### Google Drive
- "Moved to trash" dengan "Undo"
- Bottom-left, persisten sampai user action
- Progress indicator untuk multiple files

### WhatsApp Web
- "✓ Message deleted"
- Bottom-center, 3 detik
- Minimalis tanpa action

## Metrics & Success Criteria

Track effectiveness pattern ini dengan:

1. **Dismissal rate** — Berapa % user menutup toast manual? (High = pesan kurang relevan)
2. **Action rate** — Untuk toast dengan button, berapa % user klik action?
3. **Undo rate** — Berapa sering user pakai undo? (Indicator useful feature)
4. **Time to read** — Apakah duration cukup? (A/B test different durations)
5. **Error recovery** — Apakah toast membantu user recover dari error?

## Psikologi & UX Reasoning

### Mengapa Pattern Ini Efektif?

1. **Non-Intrusive Feedback** — User dapat terus bekerja tanpa interupsi
2. **Instant Gratification** — Konfirmasi immediate bahwa aksi berhasil
3. **Reduced Cognitive Load** — Auto-dismiss = user tidak perlu mikir untuk tutup
4. **Undo Capability** — Memberi user sense of control dan safety
5. **Peripheral Awareness** — Muncul di pinggir, tidak menghalangi focus area

### Prinsip Design yang Diterapkan

- **Feedback Principle** — Sistem harus selalu beri feedback untuk user actions
- **Visibility of System Status** — User tahu apa yang terjadi di sistem
- **User Control** — Undo action memberi kontrol ke user
- **Error Prevention** — Dengan undo, user tidak takut eksperimen
- **Recognition over Recall** — Icon & warna bantu user pahami tipe pesan

## Referensi & Resources

### Design Systems
- [Material Design — Snackbars](https://material.io/components/snackbars)
- [Apple HIG — Alerts](https://developer.apple.com/design/human-interface-guidelines/alerts)
- [Carbon Design System — Notifications](https://carbondesignsystem.com/components/notification/usage)

### Libraries
- [React Hot Toast](https://react-hot-toast.com/) — Lightweight, accessible
- [Notyf](https://github.com/caroso1222/notyf) — Vanilla JS, minimalistic
- [Toastify](https://github.com/apvarun/toastify-js) — No dependencies

### Articles
- Nielsen Norman Group: "Toast & Snackbar Notifications: When to Use Which"
- Smashing Magazine: "Designing Better Notifications"
- A List Apart: "Accessibility and Timing in UI Feedback"

## Changelog

- **2026-06-21**: Pattern pertama kali dibuat dengan implementasi lengkap, accessibility guidelines, dan real-world examples
