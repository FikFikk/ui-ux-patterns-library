# Sticky Navigation dengan Scroll Behavior

> **Kategori:** Navigation • **Tingkat Kesulitan:** Menengah • **Tanggal:** 24 Juni 2026

---

## Apa itu Sticky Navigation?

**Sticky Navigation** adalah pola navigasi di mana header/navbar tetap terlihat (menempel di bagian atas viewport) saat pengguna menggulir halaman ke bawah. Pattern ini menggabungkan beberapa **scroll behavior** cerdas untuk memaksimalkan ruang layar sambil tetap memberikan akses mudah ke navigasi.

Terdapat beberapa varian utama:

| Varian | Deskripsi | Contoh Produk |
|--------|-----------|---------------|
| **Always Visible** | Navbar selalu tampil di atas | GitHub, Twitter/X |
| **Hide on Scroll Down, Show on Scroll Up** | Sembunyikan saat scroll bawah, tampilkan saat scroll atas | Medium, Airbnb |
| **Shrink on Scroll** | Navbar mengecil saat pengguna scroll | Apple, Stripe |
| **Progressive Sticky** | Muncul di viewport hanya setelah melewati hero section | Notion, Figma |

---

## Mengapa Pattern Ini Penting?

### Masalah yang Dipecahkan
Pengguna web modern menghabiskan **70% waktunya** di bawah lipatan halaman (below the fold). Tanpa navigasi yang mudah diakses, mereka harus scroll kembali ke atas setiap kali ingin berpindah halaman — pengalaman yang melelahkan, terutama di halaman panjang.

### Manfaat Utama
- **Efisiensi navigasi**: Pengguna bisa berpindah halaman kapan saja tanpa kehilangan konteks
- **Orientasi**: Pengguna selalu tahu sedang di website apa meskipun sudah scroll jauh
- **Konversi**: CTA (Call-to-Action) di navbar tetap terlihat sepanjang waktu
- **Engagement**: Mengurangi bounce rate karena navigasi selalu accessible

---

## Use Case yang Ideal

### ✅ Cocok untuk:
- **Halaman panjang** (landing page, artikel, dokumentasi)
- **E-commerce** — akses cepat ke cart, search, kategori
- **Blog/Media** — logo + kategori + search selalu tersedia
- **Web app** — navigasi antar fitur tanpa kehilangan progres kerja
- **One-page website** — anchor links ke section berbeda

### ❌ Kurang cocok untuk:
- Halaman pendek yang tidak memerlukan scroll
- Mobile app native (iOS/Android punya pattern navigasi tersendiri)
- Halaman yang sangat dense dengan konten visual penuh (dashboard chart-heavy)

---

## Implementasi

### Prinsip CSS Utama

```css
/* Metode 1: CSS position sticky — paling sederhana & performan */
.navbar {
  position: sticky;
  top: 0;
  z-index: 1000;
}

/* Metode 2: Fixed positioning dengan JavaScript untuk behavior lebih kompleks */
.navbar {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 1000;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

/* State: tersembunyi (scroll ke bawah) */
.navbar.hidden {
  transform: translateY(-100%);
}

/* State: terlihat dengan bayangan (sudah scroll) */
.navbar.scrolled {
  box-shadow: 0 2px 20px rgba(0, 0, 0, 0.1);
}
```

### JavaScript untuk Smart Behavior

```javascript
class StickyNav {
  constructor(navElement) {
    this.nav = navElement;
    this.lastScrollY = 0;
    this.scrollThreshold = 100; // px sebelum perilaku aktif
    this.ticking = false;
    
    this.init();
  }
  
  init() {
    window.addEventListener('scroll', () => this.onScroll(), { passive: true });
  }
  
  onScroll() {
    // Gunakan requestAnimationFrame untuk performa optimal
    if (!this.ticking) {
      requestAnimationFrame(() => {
        this.updateNav();
        this.ticking = false;
      });
      this.ticking = true;
    }
  }
  
  updateNav() {
    const currentScrollY = window.scrollY;
    
    // Tambahkan bayangan setelah scroll melewati threshold
    if (currentScrollY > this.scrollThreshold) {
      this.nav.classList.add('scrolled');
    } else {
      this.nav.classList.remove('scrolled');
    }
    
    // Sembunyikan saat scroll bawah, tampilkan saat scroll atas
    if (currentScrollY > this.lastScrollY && currentScrollY > this.scrollThreshold) {
      this.nav.classList.add('hidden');
      this.nav.setAttribute('aria-hidden', 'true');
    } else {
      this.nav.classList.remove('hidden');
      this.nav.setAttribute('aria-hidden', 'false');
    }
    
    this.lastScrollY = currentScrollY;
  }
}

// Inisialisasi
const navbar = document.querySelector('.navbar');
new StickyNav(navbar);
```

---

## Accessibility Considerations

### WCAG 2.1 Level AA Requirements

#### 1. Focus Management (WCAG 2.4.3)
Ketika navbar tersembunyi, elemen di dalamnya **harus tidak dapat difokus** oleh keyboard:

```css
.navbar.hidden {
  transform: translateY(-100%);
  /* visibility: hidden otomatis mencegah fokus tab */
  visibility: hidden;
}

.navbar:not(.hidden) {
  visibility: visible;
}
```

#### 2. Reduced Motion (WCAG 2.3.3)
Pengguna dengan vestibular disorder atau sensitivitas gerak perlu alternatif:

```css
@media (prefers-reduced-motion: reduce) {
  .navbar {
    transition: none !important;
  }
}
```

#### 3. Skip Navigation Link (WCAG 2.4.1)
Sediakan "Skip to main content" untuk pengguna screen reader dan keyboard-only:

```html
<a href="#main-content" class="skip-link">
  Lewati ke konten utama
</a>
<nav aria-label="Navigasi utama">...</nav>
<main id="main-content">...</main>
```

```css
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  background: #000;
  color: #fff;
  padding: 8px;
  z-index: 9999;
  transition: top 0.2s;
}

.skip-link:focus {
  top: 0;
}
```

#### 4. ARIA Attributes yang Tepat
```html
<header role="banner">
  <nav aria-label="Navigasi utama" aria-hidden="false">
    <ul role="list">
      <li><a href="/" aria-current="page">Beranda</a></li>
      <li><a href="/produk">Produk</a></li>
    </ul>
  </nav>
</header>
```

#### 5. Kontras Warna
- Teks navigasi: minimum **4.5:1** contrast ratio terhadap background
- Gunakan checker: https://webaim.org/resources/contrastchecker/

#### 6. Area Klik / Touch Target
- Minimum **44×44px** untuk touch target (WCAG 2.5.8)
- Link navigasi harus mudah ditekan di mobile

---

## Do's ✅

| Do | Alasan |
|----|--------|
| Gunakan `position: sticky` untuk kasus sederhana | Lebih performan, tidak perlu JS |
| Tambahkan transisi CSS yang halus | UX terasa natural, tidak mengejutkan |
| Terapkan `aria-hidden` saat navbar tersembunyi | Accessibility untuk screen reader |
| Sediakan skip navigation link | WCAG requirement, penting untuk keyboard user |
| Gunakan `requestAnimationFrame` untuk scroll handler | Mencegah layout thrashing, 60fps smooth |
| Tambahkan visual indicator (bayangan/border) saat sticky | Memisahkan navbar dari konten secara visual |
| Uji di berbagai ukuran layar | Behavior bisa berbeda di tablet/mobile |
| Pertimbangkan `prefers-reduced-motion` | Inklusif untuk semua pengguna |

## Don'ts ❌

| Don't | Alasan |
|-------|--------|
| Jangan gunakan `scroll` event tanpa debounce/rAF | Menyebabkan jank dan drop frame rate |
| Jangan sembunyikan navbar saat modal/drawer terbuka | Konflik z-index dan UX membingungkan |
| Jangan buat navbar terlalu tinggi | Mengambil terlalu banyak ruang layar di mobile |
| Jangan lupakan `z-index` yang tepat | Navbar bisa tertutup konten yang di-scroll |
| Jangan animasikan `top` atau `height` | Menyebabkan layout reflow — gunakan `transform` |
| Jangan blok konten dengan navbar tanpa `scroll-padding-top` | Anchor link akan tertutup navbar |
| Jangan gunakan behavior yang sama di mobile dan desktop | Mobile butuh pertimbangan khusus ruang layar |

### Tentang `scroll-padding-top`
```css
/* Tanpa ini, anchor link akan tertutup navbar */
html {
  scroll-padding-top: 70px; /* tinggi navbar */
}

/* Atau per-elemen */
.section {
  scroll-margin-top: 70px;
}
```

---

## Contoh dari Produk Nyata

### 🏡 Airbnb
- **Behavior**: Hide on scroll down, show on scroll up
- **Tambahan**: Search bar melebar/mengecil berdasarkan scroll position  
- **Pembelajaran**: Navbar komplex bisa disimplifikasi di scroll position tertentu

### 📝 Medium
- **Behavior**: Navbar transparan di hero, menjadi solid + shadow setelah melewati hero
- **Tambahan**: Progress bar baca artikel terintegrasi di navbar  
- **Pembelajaran**: Mengkombinasikan scroll behavior dengan fitur tambahan

### 🐙 GitHub
- **Behavior**: Always visible sticky (tidak hide/show)
- **Tambahan**: Sticky sub-navigation per repo section  
- **Pembelajaran**: Untuk web app dengan banyak navigasi level, always-visible lebih aman

### 🎨 Stripe
- **Behavior**: Navbar mengecil (shrink) saat scroll melewati hero  
- **Tambahan**: Background berubah dari transparan menjadi blur-frosted-glass  
- **Pembelajaran**: Efek visual yang sophisticated tanpa mengorbankan fungsi

### 📱 Twitter/X
- **Behavior**: Berbeda antara mobile (bottom nav) dan desktop (left sidebar)  
- **Pembelajaran**: Sticky pattern harus disesuaikan dengan platform, bukan copy-paste

---

## Variasi Advanced: Frosted Glass Navbar

Tren modern menggunakan efek **glassmorphism** dengan `backdrop-filter`:

```css
.navbar-glass {
  position: sticky;
  top: 0;
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.3);
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  .navbar-glass {
    background: rgba(0, 0, 0, 0.8);
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  }
}
```

> ⚠️ **Catatan**: `backdrop-filter` tidak didukung sepenuhnya di semua browser. Selalu sediakan fallback dengan `background` solid.

---

## Variasi Advanced: Progress Reading Indicator

Pattern bonus yang sering dikombinasikan dengan sticky nav:

```css
.reading-progress {
  position: fixed;
  top: 60px; /* di bawah navbar */
  left: 0;
  width: 0%;
  height: 3px;
  background: linear-gradient(90deg, #667eea, #764ba2);
  z-index: 999;
  transition: width 0.1s linear;
}
```

```javascript
window.addEventListener('scroll', () => {
  const scrollTop = window.scrollY;
  const docHeight = document.documentElement.scrollHeight - window.innerHeight;
  const progress = (scrollTop / docHeight) * 100;
  document.querySelector('.reading-progress').style.width = `${progress}%`;
}, { passive: true });
```

---

## Performa & Best Practices

### Composite Layer untuk Animasi Smooth
```css
.navbar {
  will-change: transform; /* Beri hint browser untuk GPU compositing */
}

/* Hapus will-change setelah tidak diperlukan (misal: saat tidak scroll) */
```

### INP (Interaction to Next Paint) Consideration
- Scroll handler yang berat akan menurunkan INP score
- Gunakan selalu `{ passive: true }` pada scroll event listener
- Batasi DOM manipulation di dalam rAF callback

---

## Referensi

- [MDN: position sticky](https://developer.mozilla.org/en-US/docs/Web/CSS/position#sticky)  
- [WCAG 2.4.1 - Bypass Blocks](https://www.w3.org/WAI/WCAG21/Understanding/bypass-blocks.html)  
- [WCAG 2.3.3 - Animation from Interactions](https://www.w3.org/WAI/WCAG21/Understanding/animation-from-interactions.html)  
- [CSS Tricks: Sticky Positioning](https://css-tricks.com/position-sticky-2/)  
- [Web.dev: Optimize Cumulative Layout Shift](https://web.dev/cls/)  
- [Smashing Magazine: Designing Perfect Sticky Headers](https://www.smashingmagazine.com/2012/09/sticky-menus-are-quicker-to-navigate/)  
- [Nielsen Norman Group: Sticky Headers](https://www.nngroup.com/articles/sticky-headers/)
