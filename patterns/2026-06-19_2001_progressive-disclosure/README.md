# Progressive Disclosure — Mengelola Kompleksitas dengan Bertahap

## Overview

Progressive Disclosure adalah prinsip desain interaksi yang menyembunyikan informasi atau fungsi kompleks hingga user membutuhkannya. Pattern ini menampilkan hanya hal-hal esensial di awal, kemudian secara bertahap mengungkap detail atau opsi advanced sesuai kebutuhan user. Tujuannya adalah mengurangi cognitive load, menyederhanakan interface, dan membuat aplikasi lebih approachable tanpa mengorbankan power dan fleksibilitas.

Pattern ini bukan tentang "menyembunyikan" fitur secara permanen, melainkan **mengatur timing pengungkapan informasi** agar sesuai dengan mental model dan progression user dalam menyelesaikan task.

## Prinsip Inti

### 1. Show, Don't Overwhelm
Tampilkan hanya informasi yang **immediately actionable** untuk user pada tahap tertentu. Detail tambahan ditawarkan saat konteks membuatnya relevan.

### 2. Guided Discovery
User secara natural menemukan fitur advanced saat mereka siap menggunakannya, bukan dibombardir di awal.

### 3. Reversible & Non-Destructive
Ekspansi atau collapse informasi harus mudah dibalik. User bisa kembali ke state sebelumnya tanpa kehilangan konteks.

## Kapan Menggunakan

**Ideal untuk:**
- **Forms kompleks** dengan banyak field opsional atau advanced options
- **Settings pages** dengan ratusan konfigurasi (separate basic vs advanced)
- **Onboarding flows** yang harus ramah pemula tapi fleksibel untuk experts
- **Data-heavy interfaces** (dashboards, reports) dengan multiple levels of detail
- **Feature-rich applications** yang melayani berbagai user skill levels
- **Multi-step processes** di mana setiap step memiliki dependencies

**Hindari jika:**
- Semua informasi sama pentingnya dan harus visible sekaligus
- User perlu membandingkan banyak opsi side-by-side
- Menyembunyikan informasi malah membuat task lebih sulit
- Audience adalah expert users yang butuh access cepat ke semua kontrol
- Interface sudah simpel dan tidak ada kompleksitas yang perlu dikelola

## Manfaat

### Cognitive Load Reduction
- Mengurangi overwhelm dengan menyajikan informasi dalam **digestible chunks**
- User fokus pada task utama tanpa distraksi dari opsi yang tidak relevan
- Menurunkan **decision paralysis** dari terlalu banyak choices di awal

### Improved Usability
- **Lower barrier to entry** untuk new users
- Onboarding lebih cepat karena learning curve yang gradual
- Task completion rate lebih tinggi untuk common use cases
- Error rate lebih rendah karena fewer options = fewer mistakes

### Scalability
- Interface tetap clean meski fitur bertambah banyak
- Mudah menambah advanced features tanpa membingungkan basic users
- Sama powerfull tapi lebih accessible

### Flexibility
- Melayani **multiple user personas** dengan satu interface
- Beginners dapat path yang simple, experts dapat shortcut ke advanced
- User bisa grow dengan aplikasi tanpa interface redesign

## Pattern Variations

### 1. Accordion / Collapsible Sections
Konten diorganisir dalam sections yang bisa expand/collapse.

**Use case:**
- FAQ pages
- Long forms dengan logical groupings
- Settings dengan banyak kategori
- Product specifications

**Karakteristik:**
- Click header untuk expand/collapse
- Biasanya hanya satu section terbuka (exclusive), atau multiple (non-exclusive)
- Visual indicator (chevron/arrow) untuk state

**Contoh:**
```
▼ Personal Information
  Name: [______]
  Email: [______]
  
▶ Shipping Address (optional)

▶ Advanced Preferences
```

### 2. Show More / Read More Links
Truncate konten panjang dengan option untuk melihat full version.

**Use case:**
- Article previews di feed
- Long descriptions di product listings
- Comment threads
- User bios

**Karakteristik:**
- Tampilkan 2-3 baris pertama + "... Read more"
- Click untuk expand inline atau navigate ke full page
- Sometimes reversible dengan "Show less"

### 3. Inline Expansion
Element expand di tempat untuk menampilkan detail.

**Use case:**
- Table rows dengan detail view
- List items dengan metadata
- Notification centers
- Search results dengan previews

**Karakteristik:**
- Click row/item untuk expand detail
- Detail muncul inline (tidak navigate away)
- Collapse dengan click lagi atau click outside

### 4. Steppers / Wizards
Multi-step process yang reveal pertanyaan/fields based on previous answers.

**Use case:**
- Complex forms (tax filing, insurance quotes)
- Product configurators
- Onboarding flows
- Survey dengan conditional logic

**Karakteristik:**
- Step-by-step progression
- Next step depends pada current input
- Progress indicator
- Back navigation available

**Contoh:**
```
Step 1: Select project type
  → Web App selected
  
Step 2: Choose framework (shown because "Web App")
  → React, Vue, Angular, Svelte
```

### 5. Advanced Mode Toggle
Separate interface states untuk basic vs advanced users.

**Use case:**
- Code editors (visual vs code mode)
- Search (simple vs advanced filters)
- Settings (basic vs expert)
- Photo editors (auto vs manual)

**Karakteristik:**
- Explicit toggle atau switch
- Mode persisted dalam session
- Clear labeling untuk target audience

### 6. Tooltips & Popovers
Contextual help yang muncul on demand.

**Use case:**
- Field help text
- Feature explanations
- Terminology definitions
- Icon button labels

**Karakteristik:**
- Triggered by hover atau click
- Tidak block main content
- Dismissible
- Positioned near trigger element

### 7. Progressive Forms
Forms yang **add fields dynamically** based pada previous inputs.

**Use case:**
- Conditional questions
- Dynamic dropdowns (country → state → city)
- Type-specific fields (Business vs Individual)

**Karakteristik:**
- Start dengan minimal required fields
- New fields appear based pada selections
- Smooth transitions
- Validation per section

## Implementasi Best Practices

### Visual Design

**Indicators untuk Collapsed State:**
- Chevron/arrow icons (▶ collapsed, ▼ expanded)
- Plus/minus icons (+ show, − hide)
- "Show more" text links
- Gradient fade untuk truncated content
- Count badges (e.g., "3 more items")

**Transitions:**
```css
.collapsible-content {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.3s ease-out;
}

.collapsible-content.expanded {
  max-height: 1000px; /* atau auto dengan JS */
}
```

**Performance:**
- Use `max-height` untuk smooth transitions
- Lazy load content yang collapsed (especially untuk images/heavy data)
- Debounce accordion toggles
- Virtual scrolling untuk long lists

### Interaction Design

**Affordances:**
- Clickable headers harus **look clickable** (pointer cursor, hover state)
- Visual differentiation antara collapsed vs expanded state
- Clear labeling: "Show details", bukan ambiguous "More"
- Preview content yang di-truncate agar user tahu ada yang lebih

**Feedback:**
- Instant visual response pada click (tidak lag)
- Smooth animation (200-300ms ideal)
- Focus management (scroll expanded section into view jika perlu)
- State persistence (jika user refresh, remember expanded sections jika relevan)

**Keyboard Support:**
- Tab untuk navigate antar sections
- Enter/Space untuk toggle expand/collapse
- Arrow keys untuk navigate dalam expanded section
- Escape untuk collapse atau exit detail view

### Content Strategy

**What to Hide:**
- Advanced/rarely used options
- Detailed explanations (provide summary + expand)
- Optional fields
- Historical data or metadata
- Bulk actions atau power-user features

**What to Always Show:**
- Core task elements
- Primary actions
- Critical warnings atau errors
- Required fields
- Status indicators

**Progressive Clues:**
Berikan hint tentang hidden content:
```
▶ Advanced Options (12 settings)
▶ Shipping Details (address, delivery date)
▶ Read full article (~5 min read)
```

## Accessibility Considerations

### Screen Reader Support

**ARIA Patterns:**
```html
<!-- Accordion dengan proper ARIA -->
<div class="accordion">
  <h3>
    <button 
      aria-expanded="false" 
      aria-controls="panel-1"
      id="accordion-header-1"
    >
      Section Title
      <span class="icon" aria-hidden="true">▶</span>
    </button>
  </h3>
  <div 
    id="panel-1"
    role="region"
    aria-labelledby="accordion-header-1"
    hidden
  >
    Panel content here
  </div>
</div>
```

**Key Attributes:**
- `aria-expanded`: true/false untuk indicate state
- `aria-controls`: link button ke content yang di-control
- `aria-labelledby`: connect panel ke header
- `hidden`: native hide dari screen readers saat collapsed
- `aria-hidden="true"`: untuk decorative icons

### Keyboard Navigation

**Required Support:**
- Tab: navigate ke accordion headers
- Enter/Space: toggle expand/collapse
- Fokus visible dan clear
- Tidak ada keyboard traps

**Enhanced:**
- Arrow keys: navigasi antar accordion headers
- Home/End: jump ke first/last header
- Page Up/Down: scroll dalam expanded content

### Cognitive Accessibility

- **Clear Labels**: "Show shipping options" bukan hanya "More"
- **Predictable Behavior**: konsisten across interface
- **Visual Indicators**: tidak rely pada color alone
- **Undo/Redo**: easy untuk collapse kembali
- **Memory Load**: tidak hide critical info yang user perlu refer back frequently

## Do's and Don'ts

### ✅ DO

**Design:**
- Prioritaskan konten berdasarkan user research, bukan assumptions
- Gunakan clear visual indicators untuk collapsed vs expanded state
- Group related information dalam logical sections
- Berikan preview atau hint tentang hidden content
- Maintain vertical rhythm saat expand/collapse
- Animate transitions untuk clarity (tidak instant disappear)

**Content:**
- Default ke state yang paling common use case (biasanya collapsed untuk advanced)
- Sediakan escape hatches (easy untuk access full version)
- Label descriptive: "Show 15 more comments" bukan "Load more"
- Progressive clues: jumlah items, estimated time, etc.

**Interaction:**
- Buat collapsed sections look interactive (affordances)
- Respond instantly pada user action
- Scroll expanded content into view jika partially offscreen
- Remember state jika user kembali (session persistence)
- Support keyboard navigation fully

**Testing:**
- Test dengan real users — especially new users vs power users
- Measure task completion time before/after
- A/B test default states (expanded vs collapsed)
- Validate dengan screen readers
- Check mobile touch targets (min 44x44px)

### ❌ DON'T

**Design:**
- Jangan hide critical information yang user perlu di every task
- Jangan gunakan progressive disclosure untuk menutupi poor IA
- Jangan buat terlalu banyak levels (max 2-3 levels)
- Jangan hide error messages atau validation
- Jangan buat indicators yang tidak jelas (ambiguous icons)
- Jangan lupa loading states untuk lazy-loaded content

**Content:**
- Jangan truncate mid-sentence tanpa ellipsis
- Jangan hide required fields atau primary actions
- Jangan generic labels ("Click here", "More")
- Jangan assume user tahu ada content tersembunyi

**Interaction:**
- Jangan buat expand/collapse yang slow (>500ms transition)
- Jangan trap keyboard focus dalam expanded section
- Jangan lupa close other sections jika exclusive accordion
- Jangan navigate user away saat expand (stay in place)
- Jangan disable browser back untuk collapsed state

**Accessibility:**
- Jangan rely pada hover alone (tidak accessible di mobile)
- Jangan lupa ARIA attributes
- Jangan gunakan color sebagai sole indicator
- Jangan block keyboard navigation
- Jangan lupa focus management

## Anti-Patterns (Kesalahan Umum)

### 1. Mystery Meat Navigation
Menyembunyikan fitur tanpa indicator bahwa ada yang tersembunyi.

**Contoh buruk:**
```
Settings
  Profile
  Notifications
  [Advanced options ada tapi tidak ada hint]
```

**Fix:**
Berikan indicator explicit: "Show advanced settings (8 options)"

### 2. Premature Disclosure
Menampilkan advanced options terlalu dini sebelum user pahami basics.

**Contoh buruk:**
Signup form dengan 20 fields optional langsung visible.

**Fix:**
Start dengan 3-4 core fields, offer "Customize your profile" setelah signup.

### 3. Hidden Required Fields
Menyembunyikan required fields dalam collapsed sections.

**Contoh buruk:**
Billing address required tapi di-collapse di "Additional Info".

**Fix:**
Required fields harus visible by default atau expanded otomatis.

### 4. Nested Disclosure Hell
Terlalu banyak levels nested collapse (click → expand → click → expand → ...).

**Contoh buruk:**
```
▶ Settings
  ▶ Advanced
    ▶ Performance
      ▶ Cache Options
        Actual settings
```

**Fix:**
Flatten hierarchy, gunakan tabs atau single-level collapse.

### 5. Inconsistent Behavior
Beberapa sections exclusive (one open), lainnya non-exclusive.

**Fix:**
Konsisten dalam satu context. Exclusive untuk navigation, non-exclusive untuk content browsing.

## Real-World Examples

### Gmail — Compose Email
- **Basic**: To, Subject, Message body
- **Progressive**: CC/BCC muncul on click "CC/BCC" link
- **Advanced**: Formatting toolbar collapsed, expand dengan toggle
- **Power**: Confidential mode, scheduling hidden dalam menu
- **Learning**: Start simple, progressive untuk features yang <20% users gunakan

### Apple iOS Settings
- **Top Level**: Major categories (Wi-Fi, Bluetooth, Notifications)
- **Second Level**: Settings per app/feature
- **Detail View**: Toggle switches + ">" untuk nested config
- **Learning**: Clear hierarchy, never more than 3 levels deep

### Amazon Checkout
- **Step 1**: Shipping address (collapsed setelah filled)
- **Step 2**: Shipping options (revealed after address)
- **Step 3**: Payment (revealed after shipping)
- **Review**: Collapsed sections dengan "Edit" untuk revise
- **Learning**: Sequential disclosure — next step unlocks setelah previous completed

### GitHub Pull Request
- **Default**: Title, description, files changed
- **Collapsed**: Checks, reviewers (expandable)
- **Progressive**: Individual file diffs (collapsible)
- **On Demand**: Outdated comments, resolved threads
- **Learning**: Most important info visible, details available tanpa overwhelming

### Stripe Payment Forms
- **Basic Mode**: Card number, expiry, CVC
- **Progressive**: Billing address muncul jika required by card
- **Advanced**: ZIP, country appear based on card type detection
- **Learning**: Dynamic field revealing based on input, not manual toggle

### Figma Properties Panel
- **Context Aware**: Panel berubah based on selected object
- **Grouped**: Properties dalam collapsible categories
- **Common First**: Layout, appearance visible; effects, exports collapsed
- **Learning**: Contextual disclosure — show relevant controls only

### TurboTax
- **Wizard**: Step-by-step dengan conditional questions
- **Progressive**: Additional forms muncul based on previous answers
- **Smart**: Skip sections yang tidak applicable
- **Summary**: All entered data reviewable dalam collapsed sections
- **Learning**: Complex domain made approachable dengan progressive revelation

## Metrics untuk Evaluasi

### Quantitative

**Task Efficiency:**
- Time to complete common tasks (should decrease)
- Number of clicks/interactions to reach goal
- Task completion rate (should increase)
- Error rate (should decrease untuk basic tasks)

**Engagement:**
- % users yang expand advanced sections
- Frequency advanced features digunakan
- Bounce rate pada complex pages (should decrease)

**Learning Curve:**
- Time to first successful action (new users)
- Feature discovery rate over time
- Help/support requests (should decrease)

### Qualitative

**User Feedback:**
- Perceived ease of use (surveys)
- Frustration points (user interviews)
- Satisfaction scores
- Feature discoverability feedback

**Observational:**
- Usability testing — where users get stuck
- Do users notice collapsed sections?
- Do users know how to expand?
- Do they feel options are hidden vs. organized?

## Research & Referensi

### Seminal Work
- **Jakob Nielsen (2006)**: "Progressive Disclosure" — foundational article
- **Jared Spool**: "The $300 Million Button" — case study on form simplification
- **Luke Wroblewski**: "Web Form Design" — best practices for progressive forms

### Design Systems
- **Material Design**: Expansion panels guidelines
- **IBM Carbon**: Accordion component patterns
- **Atlassian Design**: Progressive disclosure dalam forms
- **Microsoft Fluent**: Disclosure patterns

### Books
- **Don't Make Me Think** (Steve Krug) — simplicity principles
- **The Design of Everyday Things** (Don Norman) — affordances, discoverability
- **100 Things Every Designer Needs to Know About People** (Susan Weinschenk) — cognitive load

### Research Papers
- "The Magical Number Seven, Plus or Minus Two" (Miller, 1956) — cognitive limits
- "Progressive Disclosure in User Interfaces" (Nielsen Norman Group)
- WCAG 2.2 — Accessibility guidelines untuk disclosure patterns

### Tools & Libraries
- **Headless UI** (Tailwind): Accessible accordion/disclosure components
- **Radix UI**: Collapsible primitive
- **Reach UI**: Disclosure component
- **Chakra UI**: Accordion dengan built-in accessibility

## Implementasi Timeline

### Phase 1: Foundation (Week 1-2)
- Inventory semua content/features yang ada
- User research — identify what's critical vs. nice-to-have
- Information architecture — logical grouping
- Wireframe default vs. expanded states

### Phase 2: Design (Week 3-4)
- Visual design untuk collapsed/expanded states
- Animation specs
- Interaction patterns
- Accessibility audit plan

### Phase 3: Development (Week 5-6)
- Implement collapse/expand mechanics
- Lazy loading untuk hidden content
- Keyboard support
- ARIA attributes

### Phase 4: Testing (Week 7-8)
- Usability testing dengan 5-8 users
- Accessibility testing (screen readers, keyboard-only)
- Performance testing (animation smoothness)
- A/B test default states

### Phase 5: Iteration (Ongoing)
- Monitor analytics (expansion rates, task completion)
- Collect user feedback
- Adjust defaults based on data
- Refine based on observed patterns

---

**Last Updated**: June 2026  
**Pattern Category**: Information Architecture & Interaction  
**Complexity**: Medium  
**WCAG Level**: AA (when implemented correctly)  
**Browser Support**: Universal (with progressive enhancement)
