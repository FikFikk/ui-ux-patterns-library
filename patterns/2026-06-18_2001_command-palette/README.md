# Command Palette — Navigasi Cepat dengan Keyboard

## Overview

Command Palette (juga dikenal sebagai Command Menu atau Omnibar) adalah interface pattern yang menggabungkan pencarian, navigasi, dan aksi dalam satu dialog terpusat yang dapat dipanggil dengan keyboard shortcut. Pattern ini memungkinkan pengguna untuk mengakses fungsi-fungsi aplikasi dengan cepat tanpa perlu mengingat lokasi menu atau mengklik berkali-kali.

Command Palette populer di aplikasi developer tools (VS Code, GitHub, Vercel) dan kini meluas ke aplikasi produktivitas mainstream (Slack, Linear, Notion, Figma) karena efektivitasnya dalam meningkatkan kecepatan dan efisiensi workflow.

## Karakteristik Utama

- **Keyboard-first**: Dipanggil dengan shortcut universal (biasanya `Cmd/Ctrl + K` atau `Cmd/Ctrl + P`)
- **Fuzzy search**: Pencarian toleran terhadap typo dan urutan karakter
- **Contextual**: Menampilkan aksi yang relevan dengan state aplikasi saat ini
- **Multi-purpose**: Menggabungkan navigation, search, dan actions dalam satu interface
- **Quick feedback**: Real-time filtering saat user mengetik
- **Keyboard navigation**: Arrow keys untuk navigasi, Enter untuk eksekusi, Esc untuk tutup

## Kapan Menggunakan

**Ideal untuk:**
- Aplikasi dengan banyak fitur dan halaman (>15 navigasi items)
- Tools yang digunakan power users atau profesional
- Aplikasi dengan workflow berulang yang butuh efisiensi
- Interface dengan hirarki menu yang dalam (>3 levels)
- Aplikasi yang sudah keyboard-heavy (IDE, terminal, dashboard)
- Platform dengan banyak shortcut yang sulit diingat

**Hindari jika:**
- Aplikasi sederhana dengan <10 fitur utama
- Target user jarang menggunakan keyboard (mobile-first, casual users)
- Fungsi aplikasi sangat visual dan tidak cocok dengan text-based search
- User base mayoritas non-technical dan tidak familiar dengan keyboard shortcuts

## Manfaat

### Produktivitas
- **30-50% lebih cepat** untuk akses fungsi dibanding navigasi menu tradisional
- Mengurangi context switching dan pergerakan mouse
- Memungkinkan chaining actions tanpa meninggalkan keyboard
- User yang mahir bisa mengakses fungsi tanpa melihat UI

### Discoverability
- Menampilkan semua aksi yang tersedia dalam satu tempat
- Membantu user menemukan fitur yang jarang terlihat di UI
- Menampilkan keyboard shortcuts untuk setiap aksi, mendorong pembelajaran
- Contextual suggestions memperkenalkan fitur baru yang relevan

### Aksesibilitas
- Excellent untuk keyboard-only users
- Screen reader friendly dengan proper ARIA labels
- Mengurangi ketergantungan pada motor skills untuk navigasi mouse
- Clear visual hierarchy dan focus states

### User Experience
- Mengurangi cognitive load — user tidak perlu mengingat lokasi menu
- Konsisten dengan pattern yang sudah familiar dari dev tools
- Memberikan "sense of mastery" saat user jadi lebih mahir
- Fallback yang baik ketika user lupa lokasi fitur

## Prinsip Desain

### 1. Accessibility & Focus Management
```
- Dialog muncul di center screen dengan backdrop semi-transparent
- Focus langsung ke input field saat dibuka
- Trap focus dalam dialog (Tab cycle dalam dialog)
- Esc untuk menutup dan return focus ke elemen sebelumnya
- Clear visual focus indicators pada setiap item
```

### 2. Search & Filtering
```
- Fuzzy matching: "crtprj" → "Create Project"
- Highlight matched characters dalam hasil
- Real-time filtering (debounce 100-150ms optimal)
- Show recent/frequent actions saat input kosong
- Typo tolerance dan substring matching
```

### 3. Visual Hierarchy
```
- Group results by category (Actions, Navigation, Recent)
- Show keyboard shortcuts di sebelah kanan
- Use icons untuk quick visual scanning
- Breadcrumb atau context info untuk nested items
- Max 7-8 visible items dengan scroll
```

### 4. Keyboard Navigation
```
- Arrow Up/Down: navigasi items
- Enter: execute selected action
- Esc: close palette
- Tab: cycle focus (jika ada multiple zones)
- Ctrl/Cmd + Number: quick select (1-9)
- Type "/" untuk filter by category
```

### 5. Performance
```
- Lazy load heavy data saat dialog dibuka
- Debounce search input (100-150ms)
- Virtualize long lists (>100 items)
- Preload recent/frequent actions
- Cache search results untuk session
```

## Komponen Anatomi

```
┌─────────────────────────────────────────────┐
│  🔍  [Type a command or search...]          │ ← Input dengan icon
├─────────────────────────────────────────────┤
│                                             │
│  RECENT                                     │ ← Category header
│  › Create new project              ⌘N      │ ← Item dengan shortcut
│  › Open settings                   ⌘,      │
│                                             │
│  ACTIONS                                    │
│  › Export data                     ⌘E      │
│  › Import from file                        │
│  › Share workspace                         │
│                                             │
│  NAVIGATION                                 │
│  › Go to Dashboard            →    ⌘⇧D    │
│  › Go to Projects             →    ⌘⇧P    │
│                                             │
└─────────────────────────────────────────────┘
         ↑ Selected item highlighted
```

## Implementasi Teknis

### HTML Structure
```html
<div class="command-palette-backdrop" role="dialog" aria-modal="true">
  <div class="command-palette" role="combobox" aria-expanded="true">
    <input 
      type="text" 
      role="searchbox"
      aria-label="Command palette"
      placeholder="Type a command or search..."
    />
    <ul role="listbox">
      <li role="presentation">
        <span class="category-header">Recent</span>
      </li>
      <li role="option" aria-selected="true">
        <span class="item-label">Create new project</span>
        <kbd>⌘N</kbd>
      </li>
    </ul>
  </div>
</div>
```

### Keyboard Shortcut Registration
```javascript
// Global listener untuk memicu palette
document.addEventListener('keydown', (e) => {
  if ((e.metaKey || e.ctrlKey) && e.key === 'k') {
    e.preventDefault();
    openCommandPalette();
  }
});
```

### Search Algorithm
```javascript
function fuzzyMatch(query, text) {
  const pattern = query.toLowerCase().split('').join('.*');
  const regex = new RegExp(pattern);
  return regex.test(text.toLowerCase());
}

function scoreMatch(query, text) {
  // Prioritas: exact > starts with > contains > fuzzy
  const q = query.toLowerCase();
  const t = text.toLowerCase();
  
  if (t === q) return 100;
  if (t.startsWith(q)) return 80;
  if (t.includes(q)) return 60;
  if (fuzzyMatch(q, t)) return 40;
  return 0;
}
```

### Focus Management
```javascript
function openCommandPalette() {
  // Simpan focus sebelumnya
  previousFocus = document.activeElement;
  
  // Tampilkan dan focus input
  palette.classList.add('active');
  input.focus();
  
  // Trap focus
  palette.addEventListener('keydown', trapFocus);
}

function closeCommandPalette() {
  palette.classList.remove('active');
  
  // Return focus
  previousFocus?.focus();
  
  // Cleanup
  palette.removeEventListener('keydown', trapFocus);
}
```

## Considerations Aksesibilitas

### Screen Reader Support
- Gunakan `role="combobox"` untuk wrapper
- `role="searchbox"` untuk input
- `role="listbox"` untuk container hasil
- `role="option"` untuk setiap item
- `aria-selected="true"` pada item terpilih
- `aria-activedescendant` untuk melacak selection

### Keyboard-only Users
- Focus trap yang proper dalam dialog
- Clear visual focus indicators (outline, background)
- Support semua navigasi dengan keyboard
- Provide skip links untuk long lists

### Visual Accessibility
- High contrast antara text dan background
- Minimum 4.5:1 contrast ratio (WCAG AA)
- Large tap targets untuk hybrid input (min 44x44px)
- Clear visual feedback untuk hover, focus, selected states

### Announcements
```javascript
// Live region untuk screen reader
<div role="status" aria-live="polite" aria-atomic="true">
  {resultsCount} results found
</div>
```

## Do's and Don'ts

### ✅ DO

**Design**
- Letakkan di center screen untuk quick scanning
- Gunakan familiar keyboard shortcuts (⌘K, ⌘P)
- Tampilkan shortcuts di setiap item untuk edukasi user
- Group items by category untuk organization
- Highlight matched characters dalam hasil search
- Tunjukkan recent/frequent actions saat input kosong

**Functionality**
- Support fuzzy matching untuk typo tolerance
- Prioritize frequently used actions di hasil search
- Allow chaining commands (e.g., "create" → select type)
- Cache recent searches untuk quick access
- Provide visual feedback saat action executing
- Close palette otomatis setelah action executed

**Accessibility**
- Proper focus management dan trap
- ARIA labels yang descriptive
- Keyboard navigation yang intuitive
- Visual feedback yang jelas
- Support high contrast mode
- Test dengan screen reader

### ❌ DON'T

**Design**
- Jangan gunakan shortcuts yang conflict dengan browser/OS
- Jangan tampilkan terlalu banyak items (>50 tanpa virtualization)
- Jangan hide critical info dalam nested menus
- Jangan gunakan icon-only tanpa labels
- Jangan lupa tampilkan "no results" state
- Jangan buat input lag (debounce terlalu lama)

**Functionality**
- Jangan require exact spelling untuk matching
- Jangan lupa handle actions yang slow/async
- Jangan execute destructive actions tanpa confirmation
- Jangan cache data sensitive
- Jangan block main thread saat search
- Jangan lupa cleanup listeners saat unmount

**Accessibility**
- Jangan lupa return focus setelah close
- Jangan gunakan color-only untuk indikasi selection
- Jangan block scroll pada body (trap focus properly)
- Jangan skip keyboard testing
- Jangan assume semua user pakai mouse
- Jangan lupa escape key untuk close

## Real-World Examples

### GitHub Command Palette
- **Shortcut**: `Cmd/Ctrl + K` atau `Cmd/Ctrl + /`
- **Fitur**: Search repos, issues, PRs, navigation, settings
- **Strength**: Excellent scope management (filter by repo/org), rich preview
- **Learning**: Contextual scoping sangat membantu di large codebases

### VS Code Command Palette
- **Shortcut**: `Cmd/Ctrl + Shift + P` (commands), `Cmd/Ctrl + P` (files)
- **Fitur**: All editor commands, extensions, settings, file navigation
- **Strength**: Kategori yang jelas, keyboard shortcuts training
- **Learning**: Pemisahan file vs command search mengurangi noise

### Linear Command Menu
- **Shortcut**: `Cmd/Ctrl + K`
- **Fitur**: Create issues, search, navigation, change status
- **Strength**: Beautiful design, contextual actions, chaining commands
- **Learning**: Visual polish meningkatkan perceived speed

### Slack Command Palette
- **Shortcut**: `Cmd/Ctrl + K`
- **Fitur**: Quick switcher untuk channels, DMs, files, mentions
- **Strength**: Blazing fast, recent conversations prioritized
- **Learning**: Speed adalah priority #1 untuk messaging

### Vercel Command Menu
- **Shortcut**: `Cmd/Ctrl + K`
- **Fitur**: Project navigation, deployment actions, docs search
- **Strength**: Cross-project search, deployment shortcuts
- **Learning**: Integration dengan external resources (docs)

## Referensi & Research

### Pattern Libraries
- **Material Design**: [App bars: top — Command search](https://m3.material.io/)
- **Carbon Design System**: [UI Shell - Command palette](https://carbondesignsystem.com/)
- **Atlassian Design System**: [Quick search pattern](https://atlassian.design/)

### Articles & Research
- Nielsen Norman Group: "Command-Line Interfaces in GUI Applications" (2024)
- Smashing Magazine: "Designing Better Command Palettes" (2025)
- Laws of UX: Hick's Law — reducing choices increases decision speed

### Accessibility Guidelines
- WCAG 2.2: Success Criterion 2.1.1 (Keyboard)
- ARIA Authoring Practices: Combobox Pattern
- WebAIM: Keyboard Accessibility

### Implementation References
- **cmdk** by Vercel: https://github.com/pacocoursey/cmdk
- **kbar** by Tim Neutkens: https://github.com/timc1/kbar
- **ninja-keys** Web Component: https://github.com/ssleptsov/ninja-keys

## Metrics untuk Success

### Quantitative
- **Adoption rate**: % users yang gunakan palette vs traditional navigation
- **Time to action**: Waktu dari trigger hingga action executed
- **Error rate**: % failed searches atau incorrect selections
- **Frequency**: Average penggunaan per session
- **Discovery rate**: % fitur yang ditemukan via palette vs UI exploration

### Qualitative
- User feedback tentang ease of use
- Perceived productivity improvement
- Learning curve feedback
- Feature discoverability improvement
- Frustration points dalam usage

## Iterasi & Improvement

### Phase 1: Basic Implementation
- Single-level command list
- Exact text matching
- Basic keyboard navigation
- Essential accessibility support

### Phase 2: Enhanced Search
- Fuzzy matching algorithm
- Recent/frequent item prioritization
- Category grouping
- Keyboard shortcuts display

### Phase 3: Advanced Features
- Nested command menus (sub-palettes)
- Contextual actions based on state
- Visual previews untuk navigation
- Analytics untuk usage patterns

### Phase 4: Optimization
- Performance tuning untuk large datasets
- Personalization based on user behavior
- Machine learning untuk result ranking
- A/B testing untuk different layouts

---

**Last Updated**: June 2026  
**Pattern Category**: Navigation & Interaction  
**Complexity**: Medium  
**Browser Support**: Modern browsers (ES6+, CSS Grid)
