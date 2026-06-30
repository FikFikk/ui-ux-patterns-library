# Breadcrumbs (Remah Roti)

## Apa itu Breadcrumbs?

Breadcrumbs adalah elemen navigasi sekunder yang menampilkan lokasi pengguna saat ini dalam hierarki situs web. Nama "breadcrumbs" (remah roti) berasal dari cerita Hansel dan Gretel yang meninggalkan jejak remah roti untuk menemukan jalan pulang.

Pattern ini menampilkan jalur dari halaman home hingga halaman saat ini, biasanya dalam format:
```
Home > Kategori > Sub-kategori > Halaman Saat Ini
```

## Mengapa Breadcrumbs Penting?

### 1. **Wayfinding (Orientasi)**
Membantu pengguna memahami di mana mereka berada dalam struktur website tanpa harus mengingat navigasi yang telah dilalui.

### 2. **Navigasi Cepat**
Pengguna dapat langsung melompat ke level hierarki yang lebih tinggi dengan satu klik, tanpa perlu menggunakan tombol back berkali-kali.

### 3. **Mengurangi Bounce Rate**
Penelitian Nielsen Norman Group menunjukkan breadcrumbs dapat mengurangi bounce rate hingga 25% dengan memberikan pengguna cara mudah untuk menjelajahi konten terkait.

### 4. **SEO-Friendly**
Google menampilkan breadcrumbs di hasil pencarian (rich snippets), meningkatkan CTR dan membantu search engine memahami struktur situs.

### 5. **Menghemat Ruang**
Breadcrumbs mengambil ruang minimal di interface namun memberikan nilai navigasi yang besar.

## Jenis-jenis Breadcrumbs

### 1. Location-based (Hierarki)
Menunjukkan posisi halaman dalam struktur situs.
```
Home > Elektronik > Smartphone > Samsung Galaxy
```
**Use case:** E-commerce, portal berita, dokumentasi

### 2. Attribute-based (Faceted)
Menampilkan atribut/filter yang dipilih pengguna.
```
Home > Sepatu > Wanita > Size 38 > Warna Hitam
```
**Use case:** Situs dengan filtering kompleks

### 3. Path-based (History)
Menunjukkan riwayat navigasi pengguna (jarang digunakan karena unpredictable).
```
Home > Blog > Kategori A > Home > Produk
```
**Use case:** Jarang direkomendasikan

## Kapan Menggunakan Breadcrumbs?

### ✅ Gunakan Breadcrumbs Ketika:
- Situs memiliki hierarki lebih dari 2 level
- Struktur konten jelas dan terorganisir
- Pengguna mungkin masuk dari halaman dalam (deep pages) via search engine
- Situs memiliki banyak kategori dan sub-kategori
- E-commerce dengan banyak produk

### ❌ Jangan Gunakan Breadcrumbs Ketika:
- Situs hanya memiliki 1-2 level navigasi
- Struktur situs linear (seperti checkout process)
- Single-page application tanpa hierarki
- Landing page atau microsites

## Prinsip Desain Breadcrumbs

### 1. **Posisi**
- Letakkan di bagian atas halaman, di bawah header/navigation utama
- Di atas judul halaman (page title)
- Konsisten di semua halaman

### 2. **Visual**
- Gunakan ukuran font yang lebih kecil dari navigasi utama (80-90%)
- Warna yang kontras namun tidak terlalu menonjol (secondary text color)
- Separator jelas: `/` `>` `→` `|` atau custom icon

### 3. **Interaktivitas**
- Semua item kecuali halaman saat ini harus clickable
- Halaman saat ini tidak perlu link (atau disabled)
- Hover state yang jelas
- Touch target minimum 44x44px untuk mobile

### 4. **Konten**
- Gunakan label yang singkat dan jelas
- Konsisten dengan label navigasi utama
- Tidak perlu mengulang domain name
- Maksimal 5-7 level (lebih dari itu pertimbangkan truncation)

## Accessibility Guidelines

### Semantic HTML
```html
<nav aria-label="Breadcrumb">
  <ol class="breadcrumb">
    <li><a href="/">Home</a></li>
    <li><a href="/products">Produk</a></li>
    <li aria-current="page">Detail Produk</li>
  </ol>
</nav>
```

### Checklist Aksesibilitas:
- ✅ Gunakan `<nav>` dengan `aria-label="Breadcrumb"`
- ✅ Gunakan `<ol>` untuk ordered list
- ✅ Tandai halaman saat ini dengan `aria-current="page"`
- ✅ Pastikan contrast ratio minimal 4.5:1 (WCAG AA)
- ✅ Keyboard navigable (Tab, Enter)
- ✅ Screen reader friendly

## Best Practices

### ✅ DO's

1. **Mulai dari Home**
   ```
   Home > Kategori > Sub-kategori
   ```

2. **Gunakan Separator yang Jelas**
   - `/` atau `>` lebih baik dari `-`
   - Konsisten di seluruh situs

3. **Mobile: Gunakan Collapse atau Overflow**
   ```
   ... > Parent > Current Page
   ```
   Atau tampilkan hanya 2-3 level terakhir

4. **Schema Markup untuk SEO**
   ```json
   {
     "@type": "BreadcrumbList",
     "itemListElement": [...]
   }
   ```

5. **Truncate Text Panjang**
   ```
   Home > Kategori > Ini adalah label y...
   ```
   Tampilkan full text di tooltip

### ❌ DON'Ts

1. **Jangan Gantikan Primary Navigation**
   - Breadcrumbs adalah navigasi sekunder
   - Jangan jadikan satu-satunya cara navigasi

2. **Jangan Terlalu Besar atau Mencolok**
   - Ukuran font lebih kecil dari body text
   - Warna secondary, bukan primary

3. **Jangan Wrap ke Multiple Lines di Mobile**
   ```
   ❌ Home > Kategori >
       Sub-kategori > Halaman
   
   ✅ ... > Sub-kategori > Halaman
   ```

4. **Jangan Duplikasi URL Structure**
   ```
   ❌ /kategori/sub-kategori/produk
       Home > Kategori > Sub Kategori > Produk
   
   ✅ Gunakan label yang user-friendly
   ```

5. **Jangan Skip Level**
   ```
   ❌ Home > ... > Current Page
   
   ✅ Home > L1 > L2 > L3 > Current
   ```

## Patterns Terkait

- **Pagination**: Navigasi sekuensial antar halaman
- **Sitemap**: Overview seluruh struktur situs
- **Sidebar Navigation**: Navigasi hierarki dalam satu section
- **Tabs**: Switching antar konten di level yang sama

## Mobile Considerations

### Strategi untuk Mobile:

1. **Show Last 2-3 Levels Only**
   ```
   ... > Parent Category > Current Page
   ```

2. **Horizontal Scroll**
   - Biarkan breadcrumbs scrollable horizontal
   - Tambahkan fade effect di edges

3. **Collapse to Dropdown**
   ```
   [⋮] Parent Category > Current Page
   ```
   Tap icon untuk expand full path

4. **Back Button Alternative**
   - Di mobile app, breadcrumbs bisa disederhanakan
   - Native back button sudah cukup untuk linear navigation

### Touch Target Size:
- Minimum: 44x44px (Apple HIG)
- Recommended: 48x48px (Material Design)
- Spacing antar item: minimal 8px

## Contoh Implementasi Dunia Nyata

### E-commerce:
- **Amazon**: `All > Electronics > Computers & Accessories > Laptops`
- **Tokopedia**: `Home > Elektronik > Handphone & Tablet > Smartphone`

### Dokumentasi:
- **MDN Web Docs**: `References > JavaScript > Global Objects > Array`
- **React Docs**: `Learn React > Describing the UI > Your First Component`

### Portal Berita:
- **Kompas**: `Home > Nasional > Politik > Artikel`
- **Detik**: `Home > News > Berita Terkini > Detail`

### Gov & Education:
- **Gov.uk**: `Home > Education and learning > Schools > School curriculum`
- **Wikipedia**: `Main Page > Science > Biology > Genetics`

## Metrics & Testing

### Key Metrics:
- **Click-through rate (CTR)** pada breadcrumb links
- **Bounce rate** sebelum vs sesudah implementasi
- **Time on site** dan **pages per session**
- **Navigation paths** dari analytics

### A/B Testing Ideas:
- Style separator: `/` vs `>` vs `→`
- Color contrast: subtle vs clear
- Position: above title vs below
- Mobile strategy: collapse vs scroll vs back-only

## Resources & Tools

### Schema Markup Generator:
- [Google Rich Results Test](https://search.google.com/test/rich-results)
- [Schema.org BreadcrumbList](https://schema.org/BreadcrumbList)

### Inspiration:
- [Component Gallery - Breadcrumbs](https://component.gallery/components/breadcrumbs/)
- [Nielsen Norman Group - Breadcrumbs](https://www.nngroup.com/articles/breadcrumbs/)

### Accessibility Testing:
- WAVE Web Accessibility Evaluation Tool
- axe DevTools
- NVDA/JAWS screen readers

## Kesimpulan

Breadcrumbs adalah pattern navigasi yang sederhana namun powerful untuk meningkatkan wayfinding dan user experience. Implementasi yang baik:

1. **Mencerminkan struktur informasi situs** dengan akurat
2. **Konsisten** di seluruh situs
3. **Accessible** untuk semua pengguna
4. **Responsive** dengan strategi mobile yang tepat
5. **Tidak mengganggu** (subtle tapi functional)

Dengan biaya implementasi yang minimal dan benefit yang signifikan untuk UX dan SEO, breadcrumbs adalah salah satu pattern yang paling cost-effective untuk website dengan hierarki konten yang jelas.
