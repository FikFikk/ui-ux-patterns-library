# Skeleton Screen (Loading Placeholder)

## Deskripsi

Skeleton Screen adalah pattern UI yang menampilkan placeholder visual berbentuk kerangka (skeleton) dari konten yang sedang dimuat. Alih-alih menampilkan spinner atau loading bar generik, skeleton screen menunjukkan preview struktur layout konten yang akan muncul, memberikan kesan bahwa aplikasi lebih responsif dan cepat.

Pattern ini sangat efektif untuk meningkatkan **perceived performance** — membuat pengguna merasa aplikasi lebih cepat meskipun waktu loading sebenarnya sama.

## Kapan Menggunakan Pattern Ini

### ✅ Gunakan Skeleton Screen Ketika:

- **Loading konten dari API/database** yang membutuhkan waktu 1-10 detik
- **Struktur layout sudah diketahui** sebelum konten dimuat (misalnya card layout, list item, profile page)
- **Initial page load** dari aplikasi yang content-heavy (social media feed, e-commerce, dashboard)
- **Infinite scroll atau pagination** — saat user scroll ke bawah dan konten baru dimuat
- **Navigasi antar halaman** dalam SPA (Single Page Application)
- **Konten multimedia** seperti gambar atau video yang loading secara progresif

### ❌ Jangan Gunakan Skeleton Screen Ketika:

- Loading sangat cepat (< 300ms) — tidak perlu, bisa malah terlihat flicker
- Struktur konten sangat dinamis dan tidak predictable
- Loading action sederhana seperti submit form — gunakan button loading state
- Background process yang tidak mempengaruhi UI utama

## Keuntungan Pattern Ini

1. **Perceived Performance** — Pengguna merasa aplikasi lebih cepat hingga 30% berdasarkan penelitian
2. **Reduce Uncertainty** — Memberikan visual feedback yang jelas tentang apa yang akan muncul
3. **Better UX** — Mengurangi frustasi dan bounce rate saat loading
4. **Progressive Loading** — Mendukung strategi loading bertahap (prioritas konten penting dulu)
5. **Modern & Professional** — Memberikan kesan aplikasi yang well-designed

## Anatomi Skeleton Screen

```
┌─────────────────────────────────────┐
│ ████████ ░░░░░░░░░░░░░░░░░░░░      │  ← Avatar + Text placeholder
│                                      │
│ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  │  ← Title placeholder (darker)
│ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  │  ← Description (lighter)
│ ░░░░░░░░░░░░░░░░░░                  │
│                                      │
│ ┌──────────────┐  [░░░░░░] [░░░░]  │  ← Image + button placeholders
│ │              │                    │
│ │   ░░░░░░     │                    │
│ │              │                    │
│ └──────────────┘                    │
└─────────────────────────────────────┘
```

### Elemen Kunci:

1. **Bentuk yang Mirip Konten Asli** — Circle untuk avatar, rectangle untuk text/image
2. **Hierarki Visual** — Judul lebih tebal/gelap, description lebih tipis/terang
3. **Animasi Shimmer/Pulse** — Efek bergerak untuk indikasi loading
4. **Spacing yang Akurat** — Jarak antar elemen sesuai dengan konten final

## Implementasi Best Practices

### 1. Pilih Warna yang Tepat

```css
/* Base color lebih terang dari background */
background-color: #e0e0e0; /* Light theme */
background-color: #2a2a2a; /* Dark theme */

/* Shimmer gradient */
background: linear-gradient(
  90deg,
  #e0e0e0 25%,
  #f0f0f0 50%,
  #e0e0e0 75%
);
```

### 2. Animasi Smooth

```css
/* Shimmer effect - lebih modern */
@keyframes shimmer {
  0% { background-position: -1000px 0; }
  100% { background-position: 1000px 0; }
}

/* Pulse effect - alternatif lebih subtle */
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.6; }
}
```

### 3. Match Layout Asli

- Gunakan **dimensi yang sama** dengan konten final
- **Jumlah lines** untuk text harus sesuai estimasi
- **Aspect ratio** image placeholder harus match

### 4. Progressive Enhancement

```javascript
// Load critical content first
1. Show skeleton
2. Load above-the-fold content → replace skeleton
3. Load below-the-fold content → replace skeleton
4. Load non-critical assets (ads, recommendations)
```

## Accessibility Considerations

### ✅ WCAG Compliance

1. **ARIA Labels**
```html
<div class="skeleton" role="status" aria-label="Loading content" aria-live="polite">
  <!-- skeleton elements -->
</div>
```

2. **Screen Reader Announcement**
```html
<span class="sr-only">Loading user profile information, please wait...</span>
```

3. **Respect prefers-reduced-motion**
```css
@media (prefers-reduced-motion: reduce) {
  .skeleton {
    animation: none;
    opacity: 0.7; /* Static, tidak beranimasi */
  }
}
```

4. **Keyboard Navigation**
- Skeleton screen tidak boleh trap focus
- Focus harus skip ke konten yang sudah loaded

5. **Color Contrast**
- Pastikan skeleton visible untuk low-vision users
- Minimum contrast ratio 3:1 dengan background

### ⚠️ Perhatian Khusus

- **Jangan gunakan `aria-hidden="true"`** — screen reader perlu tahu ada proses loading
- **Timeout handling** — jika loading > 10 detik, berikan opsi retry atau error message
- **Announce completion** — beri tahu screen reader ketika konten sudah loaded

## Do's and Don'ts

### ✅ DO: Best Practices

1. **Match Content Structure**
   ```
   ✓ Skeleton card layout = Final card layout
   ✓ Jumlah skeleton items = estimasi jumlah items
   ```

2. **Use Subtle Animation**
   ```
   ✓ Shimmer speed: 1.5-2 seconds per cycle
   ✓ Pulse opacity: 0.6 - 1.0
   ```

3. **Progressive Disclosure**
   ```
   ✓ Load hero section → show content
   ✓ Continue loading below fold → show content
   ```

4. **Provide Context**
   ```
   ✓ "Loading your feed..."
   ✓ "Fetching latest products..."
   ```

5. **Handle Errors Gracefully**
   ```
   ✓ Skeleton → Error state with retry button
   ✓ Show partial content if available
   ```

### ❌ DON'T: Hindari Ini

1. **❌ Skeleton Terlalu Berbeda dari Konten**
   ```
   ✗ Skeleton: 3 cards
   ✗ Actual: List view 10 items
   ```

2. **❌ Animasi Terlalu Agresif**
   ```
   ✗ Shimmer speed < 0.5 detik (bikin pusing)
   ✗ Flash/blink effect
   ```

3. **❌ Skeleton untuk Loading Cepat**
   ```
   ✗ Data load < 300ms masih show skeleton (flicker)
   ✗ Better: langsung show content atau fade in
   ```

4. **❌ Tidak Ada Timeout**
   ```
   ✗ Skeleton tetap show selamanya jika API error
   ✗ Harus ada timeout → error state
   ```

5. **❌ Skeleton Generik untuk Semua**
   ```
   ✗ Generic gray box untuk semua konten
   ✓ Specific skeleton per content type
   ```

## Variasi Pattern

### 1. Text Skeleton
```
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  ← Full width
░░░░░░░░░░░░░░░░░░░░░░░  ← Full width
░░░░░░░░░░░░░░░░         ← Partial (60-80%)
```

### 2. Card Skeleton
```
┌────────────────┐
│ ░░░░░░░░░░░░   │  Image placeholder
│ ░░░░░░░░░░░░   │
├────────────────┤
│ ▓▓▓▓▓▓▓▓▓▓▓▓   │  Title
│ ░░░░░░░░░░░░   │  Description
│ ░░░░░░░        │
└────────────────┘
```

### 3. List Skeleton
```
● ░░░░░░░░░░░░░░░░░░░░░░
● ░░░░░░░░░░░░░░░░░░░░░░
● ░░░░░░░░░░░░░░░░░░░░░░
```

### 4. Avatar + Text
```
● ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  ░░░░░░░░░░░░░░░
```

### 5. Data Table Skeleton
```
┌──────┬──────┬──────┬──────┐
│ ▓▓▓▓ │ ▓▓▓▓ │ ▓▓▓▓ │ ▓▓▓▓ │  Header
├──────┼──────┼──────┼──────┤
│ ░░░░ │ ░░░░ │ ░░░░ │ ░░░░ │  Row 1
│ ░░░░ │ ░░░░ │ ░░░░ │ ░░░░ │  Row 2
│ ░░░░ │ ░░░░ │ ░░░░ │ ░░░░ │  Row 3
└──────┴──────┴──────┴──────┘
```

## Real-World Examples

### 🌟 Implementasi Excellent

1. **Facebook Feed**
   - Skeleton cards dengan avatar circle + text lines
   - Shimmer animation dari kiri ke kanan
   - Progressive loading: post paling atas dulu

2. **LinkedIn**
   - Detail skeleton untuk profile cards
   - Network graph skeleton saat loading connections
   - Smooth transition dari skeleton ke content

3. **YouTube**
   - Thumbnail rectangle + title/description lines
   - Grid layout skeleton matching final layout
   - Sidebar skeleton untuk recommendations

4. **Airbnb**
   - Property card skeleton dengan image + details
   - Map view skeleton untuk loading listings
   - Filters skeleton di sidebar

5. **Medium**
   - Article skeleton dengan avatar + title + paragraphs
   - Reading list skeleton
   - Responsive: mobile vs desktop layout

6. **Slack**
   - Message thread skeleton
   - Channel list skeleton
   - Workspace switching skeleton

### 📊 Data & Research

- **Luke Wroblewski Research**: Skeleton screens dapat meningkatkan perceived performance hingga 30%
- **Google Research**: Users lebih toleran terhadap loading jika ada visual feedback
- **Nielsen Norman Group**: Skeleton screens mengurangi bounce rate pada loading > 3 detik

## Kombinasi dengan Pattern Lain

### 1. Skeleton + Lazy Loading
```
User scroll → Show skeleton → Load image → Replace skeleton
```

### 2. Skeleton + Optimistic UI
```
User action → Show skeleton + previous data → Update with real data
```

### 3. Skeleton + Progressive Enhancement
```
Load HTML → Show skeleton → Load CSS → Load JS → Show content
```

### 4. Skeleton + Infinite Scroll
```
Bottom reached → Append skeleton → Load next page → Replace skeleton
```

## Implementasi Teknis

Lihat file `example.html` untuk implementasi lengkap dengan:
- ✅ Shimmer animation yang smooth
- ✅ Berbagai variasi skeleton (card, list, text, avatar)
- ✅ Dark mode support
- ✅ Accessibility compliant (ARIA, prefers-reduced-motion)
- ✅ Responsive design
- ✅ Simulasi loading dan transition ke konten real

## Referensi

### Artikel & Guidelines

1. **Luke Wroblewski** - "Mobile Design Details: Avoid The Spinner"
   https://www.lukew.com/ff/entry.asp?1797

2. **CSS-Tricks** - "How to Create Skeleton Screens"
   https://css-tricks.com/building-skeleton-screens-css-custom-properties/

3. **Smashing Magazine** - "Skeleton Screens With CSS"
   https://www.smashingmagazine.com/2020/04/skeleton-screens-react/

4. **Nielsen Norman Group** - "Progress Indicators"
   https://www.nngroup.com/articles/progress-indicators/

5. **Material Design** - "Empty States & Skeleton Screens"
   https://material.io/design/communication/empty-states.html

### Libraries & Tools

- **react-loading-skeleton** - https://github.com/dvtng/react-loading-skeleton
- **vue-content-loader** - https://github.com/egoist/vue-content-loader
- **angular-skeleton-loader** - https://github.com/willmendesneto/ngx-skeleton-loader
- **skeleton-elements** - https://github.com/nolimits4web/skeleton-elements

### Design Systems yang Menggunakan Pattern Ini

- Material Design (Google)
- Ant Design
- Carbon Design System (IBM)
- Polaris (Shopify)
- Lightning Design System (Salesforce)

---

**Pattern Type**: Feedback / Loading State  
**Difficulty**: Intermediate  
**Maintenance**: Low (once implemented, rarely changes)  
**Browser Support**: All modern browsers  
**Mobile**: Essential untuk mobile apps dengan network latency

**Tags**: #loading #skeleton #placeholder #perceived-performance #ux #feedback #progressive-loading #accessibility
