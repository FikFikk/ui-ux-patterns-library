# Visual Examples: Skeleton Screens in Production

## Industry Leaders Using Skeleton Screens

### 1. **LinkedIn Feed**

**Pattern**: Card-based skeleton with profile image circles and text lines

**Visual Structure**:
```
┌─────────────────────────────────────┐
│ ○  ████████                         │
│    ████████████                     │
│                                     │
│ ███████████████████████████         │
│ ████████████████                    │
│ ██████████████████████              │
│                                     │
│ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓         │ (shimmer animation)
│                                     │
│ Like • Comment • Share              │
└─────────────────────────────────────┘
```

**Key Features**:
- Circular avatar placeholder with pulse animation
- 2-3 text line placeholders of varying widths
- Image placeholder with subtle gradient shimmer
- Interaction buttons remain visible (grayed out)

**Why It Works**: Users immediately recognize the feed structure, reducing anxiety during network delays.

---

### 2. **Stripe Dashboard**

**Pattern**: Progressive data table skeleton

**Visual Structure**:
```
┌─────────────────────────────────────────────────────────┐
│  ████████    ████████    ████████    ████████           │
├─────────────────────────────────────────────────────────┤
│  ████████    ████████    ████████    ████████           │
│  ████████    ████████    ████████    ████████           │
│  ████████    ████████    ████████    ████████           │
│  ████████    ████████    ████████    ████████           │
└─────────────────────────────────────────────────────────┘
```

**Key Features**:
- Rows load progressively (first row appears after 100ms)
- Exact column width matching final table
- Subtle left-to-right shimmer effect
- Monospace-width placeholders for numeric columns

**Why It Works**: Financial data requires precision—skeleton matches exact dimensions preventing layout shift.

---

### 3. **Airbnb Search Results**

**Pattern**: Grid-based image + text skeleton

**Visual Structure**:
```
┌──────────┐  ┌──────────┐  ┌──────────┐
│▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│
│▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│
│▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│
├──────────┤  ├──────────┤  ├──────────┤
│████████  │  │████████  │  │████████  │
│██████    │  │██████    │  │██████    │
│★★★★★ ███│  │★★★★★ ███│  │★★★★★ ███│
└──────────┘  └──────────┘  └──────────┘
```

**Key Features**:
- 4:3 aspect ratio image placeholder (matches listing photos)
- Title line at 80% width, subtitle at 60%
- Star rating skeleton (5 gray stars)
- Price placeholder right-aligned
- Wave animation from top to bottom

**Why It Works**: Image-dominant pattern—visual hierarchy established immediately even without actual photos.

---

### 4. **YouTube Video Page**

**Pattern**: Content-aware skeleton with sidebar

**Visual Structure**:
```
┌────────────────────────────┐  ┌──────────┐
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │██████    │
│▓▓▓▓  16:9 VIDEO  ▓▓▓▓▓▓▓▓▓│  │██        │
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  ├──────────┤
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│  │▓▓▓▓▓▓▓▓▓▓│
├────────────────────────────┤  │██████    │
│██████████████              │  │██        │
│○ ██████████                │  └──────────┘
│████████████████████        │
│██████████                  │
└────────────────────────────┘
```

**Key Features**:
- 16:9 video player placeholder (exact aspect ratio)
- Channel avatar circle below video
- Title skeleton (2 lines, bold weight)
- Sidebar with smaller 16:9 thumbnails
- Staggered loading (main video first, sidebar after)

**Why It Works**: Respects sacred video aspect ratio—no unexpected height changes when video loads.

---

### 5. **Notion Page Loading**

**Pattern**: Block-based progressive skeleton

**Visual Structure**:
```
┌─────────────────────────────────────┐
│ ██████████████████                  │  ← H1 title
│                                     │
│ ███████████████████████████         │  ← Paragraph
│ ████████████████████                │
│ █████████████                       │
│                                     │
│ • ████████                          │  ← List item
│ • ██████████                        │
│ • ███████                           │
│                                     │
│ ┌─────────┬──────────┐             │  ← Table
│ │████████ │████████  │             │
│ │████████ │████████  │             │
│ └─────────┴──────────┘             │
└─────────────────────────────────────┘
```

**Key Features**:
- Semantic block-level skeletons (heading, paragraph, list, table)
- Hierarchical animation (top to bottom, 50ms delay between blocks)
- Adaptive width based on content type
- No shimmer—simple pulse effect

**Why It Works**: Mirrors document structure exactly—users can start reading the "shape" of the document before content arrives.

---

### 6. **Shopify Product Page**

**Pattern**: E-commerce layout with image gallery

**Visual Structure**:
```
┌──────────┐  ┌────────────────────────┐
│▓▓▓▓▓▓▓▓▓▓│  │ ███████████████        │
├──────────┤  │                        │
│▓▓▓▓▓▓▓▓▓▓│  │ ████                   │
├──────────┤  │                        │
│▓▓▓▓▓▓▓▓▓▓│  │ ██████████████         │
├──────────┤  │ ████████████████████   │
│▓▓▓▓▓▓▓▓▓▓│  │ ████████               │
└──────────┘  │                        │
              │ [████ Add to Cart ████]│
              └────────────────────────┘
```

**Key Features**:
- Left: 4 thumbnail placeholders (square aspect ratio)
- Right: Product title (2 lines), price, description (3 lines)
- CTA button placeholder (always visible, disabled state)
- Gallery loads first, text content streams in after

**Why It Works**: Critical buying decision elements (image, price, CTA) reserve space—no disruptive shifts during checkout consideration.

---

### 7. **Slack Message Loading**

**Pattern**: Chat message skeleton

**Visual Structure**:
```
┌─────────────────────────────────────┐
│ ○ ████████  ███                     │
│   █████████████████                 │
│   ██████████                        │
│                                     │
│ ○ ████████  ███                     │
│   ███████████████████████           │
│                                     │
│ ○ ████████  ███                     │
│   ██████████████                    │
└─────────────────────────────────────┘
```

**Key Features**:
- User avatar circle + name + timestamp on first line
- Message text skeleton (1-3 lines, variable width)
- Repeats for multiple messages
- Real messages replace skeleton from top down as they load

**Why It Works**: Conversational context matters—skeleton shows there ARE messages, reducing perceived emptiness.

---

### 8. **GitHub Repository Page**

**Pattern**: Code file list skeleton

**Visual Structure**:
```
┌─────────────────────────────────────────┐
│ 📁 ████████        ████  2 weeks ago    │
│ 📁 ████████        ████  1 month ago    │
│ 📄 ████████████    ████  3 days ago     │
│ 📄 ██████          ████  1 week ago     │
│ 📄 ████████        ████  yesterday      │
└─────────────────────────────────────────┘
```

**Key Features**:
- File icons load immediately (static assets)
- Filename skeletons vary in width (mimics real file name lengths)
- Commit message skeletons (short, 30-40% width)
- Timestamp skeletons (fixed width, right-aligned)

**Why It Works**: Developer-focused—predictable table structure with variable-length data clearly indicated.

---

## Design Pattern Analysis

### Animation Timing
- **LinkedIn, Airbnb**: 1.5s shimmer loop
- **Stripe, GitHub**: 2s pulse fade
- **YouTube**: 1s wave sweep
- **Notion**: No animation (respects `prefers-reduced-motion` by default)

### Color Palettes
- **Light Mode**: `#E0E0E0` base, `#F5F5F5` highlight
- **Dark Mode**: `#2A2A2A` base, `#3A3A3A` highlight
- **Shimmer**: 20-30% opacity gradient overlay

### Aspect Ratios Preserved
- Video: 16:9 (YouTube, Vimeo)
- Product images: 1:1 or 4:5 (Instagram-style)
- Listing photos: 4:3 (Airbnb, real estate)
- Profile avatars: 1:1 circles

### Progressive Loading Strategies
1. **Top-down**: Notion, Slack (reading order)
2. **Critical-first**: Shopify (images before text)
3. **Row-by-row**: Stripe, GitHub (table data)
4. **All-at-once-replace**: LinkedIn (card units)

## Common Pitfalls to Avoid

❌ **Skeleton doesn't match content**: Width/height mismatch causes layout shift
❌ **Too much animation**: Distracting shimmer effects (>2s loops)
❌ **No animation**: Static gray boxes look broken, not loading
❌ **Wrong aspect ratios**: Images load at different size than skeleton
❌ **Ignoring accessibility**: No ARIA live region announcements

## Mobile Adaptations

All examples above adapt on mobile by:
- Single column layouts (LinkedIn, Airbnb grid → vertical stack)
- Smaller skeleton elements (40px avatar → 32px)
- Reduced animation intensity (battery/performance)
- Faster animation loops (1s vs 1.5s on desktop)
