# Dos and Don'ts: Skeleton Screens Best Practices

## ✅ DO: Match Content Structure Exactly

### Good Example
```jsx
// Skeleton matches actual card dimensions
<div className="card-skeleton">
  <Skeleton circle width={48} height={48} />  {/* Avatar */}
  <Skeleton width="70%" height={20} />         {/* Title */}
  <Skeleton width="50%" height={16} />         {/* Subtitle */}
  <Skeleton width="100%" height={200} />       {/* Image */}
</div>

// Actual content has same structure
<div className="card">
  <img className="avatar" width={48} height={48} src={user.avatar} />
  <h3 className="title">{user.name}</h3>
  <p className="subtitle">{user.role}</p>
  <img className="image" src={user.photo} />
</div>
```

**Why**: Prevents layout shift (CLS = 0). Users get accurate preview of content structure.

---

## ❌ DON'T: Use Generic Boxes

### Bad Example
```jsx
// Generic skeleton doesn't match content structure
<div className="loading">
  <Skeleton width="100%" height={300} />  {/* Single big box */}
</div>

// Actual content has multiple elements
<div className="content">
  <img src={avatar} />
  <h1>{title}</h1>
  <p>{description}</p>
  <img src={photo} />
</div>
```

**Why**: Large layout shift when content loads. Users can't anticipate what's coming. Feels broken, not loading.

---

## ✅ DO: Use Subtle Animation

### Good Example
```css
.skeleton {
  background: linear-gradient(
    90deg,
    #e0e0e0 0%,
    #f5f5f5 50%,
    #e0e0e0 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s ease-in-out infinite;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

/* Respect user preferences */
@media (prefers-reduced-motion: reduce) {
  .skeleton {
    animation: none;
  }
}
```

**Why**: 1.5-2s shimmer cycle is noticeable but not distracting. Respects accessibility preferences.

---

## ❌ DON'T: Over-Animate

### Bad Example
```css
.skeleton {
  animation: 
    shimmer 0.5s infinite,     /* Too fast */
    pulse 0.3s infinite,       /* Multiple animations */
    rotate 2s infinite;        /* Unnecessary complexity */
}

@keyframes shimmer {
  0% { opacity: 0.3; }
  50% { opacity: 1; }
  100% { opacity: 0.3; }
}
```

**Why**: Fast/complex animations are distracting and increase cognitive load. Can trigger motion sensitivity issues.

---

## ✅ DO: Load Content Progressively

### Good Example
```jsx
const UserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUsersStream((user) => {
      // Add users as they arrive
      setUsers(prev => [...prev, user]);
    }).finally(() => setLoading(false));
  }, []);

  return (
    <div>
      {users.map(user => <UserCard key={user.id} user={user} />)}
      {loading && <UserCardSkeleton count={3} />}
    </div>
  );
};
```

**Why**: Users see content sooner. Perceived performance is 20-40% faster than all-at-once loading.

---

## ❌ DON'T: Wait for All Data Before Showing Content

### Bad Example
```jsx
const UserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchAllUsers().then(allUsers => {
      // Wait for everything before showing anything
      setUsers(allUsers);
      setLoading(false);
    });
  }, []);

  return loading ? <Skeleton count={10} /> : <UserCards users={users} />;
};
```

**Why**: Users wait longer to see any content. Defeats the purpose of progressive rendering.

---

## ✅ DO: Preserve Aspect Ratios for Images

### Good Example
```jsx
// Skeleton matches image aspect ratio
<div className="image-skeleton" style={{ aspectRatio: '16/9', width: '100%' }}>
  <Skeleton width="100%" height="100%" />
</div>

// Actual image uses same aspect ratio
<img 
  src={photo} 
  style={{ aspectRatio: '16/9', width: '100%' }}
  alt={description}
/>
```

**Why**: Zero layout shift when image loads. Modern `aspect-ratio` CSS property handles this elegantly.

---

## ❌ DON'T: Use Fixed Heights Without Aspect Ratios

### Bad Example
```jsx
// Fixed height doesn't match actual image
<Skeleton width="100%" height={200} />

// Actual image has different dimensions
<img src={photo} /> {/* Could be 150px or 300px tall */}
```

**Why**: Causes layout shift. CLS score increases. Users experience visual jarring.

---

## ✅ DO: Implement Proper Accessibility

### Good Example
```jsx
<div 
  className="profile-skeleton" 
  role="status" 
  aria-live="polite"
  aria-busy="true"
  aria-label="Loading profile information"
>
  <Skeleton circle width={80} height={80} aria-hidden="true" />
  <Skeleton width="60%" height={24} aria-hidden="true" />
  <span className="sr-only">Loading user profile, please wait...</span>
</div>
```

**Why**: Screen readers announce loading state meaningfully. Skeleton elements hidden from assistive tech.

---

## ❌ DON'T: Ignore Accessibility

### Bad Example
```jsx
// No ARIA attributes, screen readers confused
<div className="skeleton-content">
  <Skeleton />
  <Skeleton />
  <Skeleton />
</div>
```

**Why**: Screen readers may announce decorative elements or provide no feedback at all. Poor experience for visually impaired users.

---

## ✅ DO: Support Dark Mode

### Good Example
```css
.skeleton {
  background: linear-gradient(90deg, #e0e0e0 0%, #f5f5f5 50%, #e0e0e0 100%);
}

@media (prefers-color-scheme: dark) {
  .skeleton {
    background: linear-gradient(90deg, #2a2a2a 0%, #3a3a3a 50%, #2a2a2a 100%);
  }
}
```

**Why**: Light skeletons on dark backgrounds are glaring and uncomfortable. Respect user theme preference.

---

## ❌ DON'T: Use Light Skeletons in Dark Mode

### Bad Example
```css
.skeleton {
  background: #e0e0e0; /* Always light gray */
}
```

**Why**: Creates harsh contrast in dark mode. Reduces readability and user comfort.

---

## ✅ DO: Handle Errors Gracefully

### Good Example
```jsx
const UserProfile = ({ userId }) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetchUser(userId)
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);

  if (error) {
    return (
      <div className="error-state" role="alert">
        <p>Failed to load profile</p>
        <button onClick={() => window.location.reload()}>Retry</button>
      </div>
    );
  }

  return loading ? <ProfileSkeleton /> : <ProfileContent data={data} />;
};
```

**Why**: Infinite skeleton screens are confusing. Clear error states with retry options improve UX.

---

## ❌ DON'T: Show Skeletons Forever on Errors

### Bad Example
```jsx
const UserProfile = ({ userId }) => {
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId).then(() => setLoading(false));
    // No error handling - loading stays true forever
  }, [userId]);

  return loading ? <ProfileSkeleton /> : <ProfileContent />;
};
```

**Why**: Users don't know if content is loading or failed. Creates support burden and frustration.

---

## ✅ DO: Set Minimum Display Time

### Good Example
```jsx
useEffect(() => {
  const fetchData = async () => {
    setLoading(true);
    const [data] = await Promise.all([
      fetch('/api/data'),
      new Promise(resolve => setTimeout(resolve, 300)) // Min 300ms
    ]);
    setData(data);
    setLoading(false);
  };
  fetchData();
}, []);
```

**Why**: Prevents skeleton "flash" on fast connections (< 100ms). 300ms minimum feels smooth, not janky.

---

## ❌ DON'T: Flash Skeletons on Fast Loads

### Bad Example
```jsx
useEffect(() => {
  setLoading(true);
  fetch('/api/data').then(data => {
    setData(data); // Completes in 50ms
    setLoading(false); // Skeleton flashes briefly
  });
}, []);
```

**Why**: Brief skeleton flash (< 200ms) is more distracting than no skeleton at all.

---

## ✅ DO: Use Semantic HTML

### Good Example
```jsx
<article className="post-skeleton" role="status">
  <header>
    <Skeleton circle width={40} height={40} />
    <div>
      <Skeleton as="h2" width="60%" height={20} />
      <Skeleton as="time" width="30%" height={16} />
    </div>
  </header>
  <section>
    <Skeleton as="p" width="100%" height={16} count={3} />
  </section>
</article>
```

**Why**: Maintains document structure. Screen readers can navigate skeleton using semantic landmarks.

---

## ❌ DON'T: Use Only Divs

### Bad Example
```jsx
<div className="skeleton">
  <div className="skeleton-line"></div>
  <div className="skeleton-line"></div>
  <div className="skeleton-line"></div>
</div>
```

**Why**: No semantic meaning. Screen readers can't distinguish content type. Poor document structure.

---

## ✅ DO: Match Real Content Width Variance

### Good Example
```jsx
<div className="text-skeleton">
  <Skeleton width="85%" height={16} />  {/* Varied widths */}
  <Skeleton width="92%" height={16} />
  <Skeleton width="78%" height={16} />
  <Skeleton width="60%" height={16} />  {/* Last line shorter */}
</div>
```

**Why**: Looks natural. Mimics how text actually flows (last paragraph line usually shorter).

---

## ❌ DON'T: Use Uniform Widths

### Bad Example
```jsx
<div className="text-skeleton">
  <Skeleton width="100%" height={16} />
  <Skeleton width="100%" height={16} />
  <Skeleton width="100%" height={16} />
  <Skeleton width="100%" height={16} />
</div>
```

**Why**: Looks robotic and unnatural. Doesn't reflect how actual text renders.

---

## ✅ DO: Consider Mobile-Specific Skeletons

### Good Example
```jsx
const ProfileSkeleton = () => {
  const isMobile = useMediaQuery('(max-width: 768px)');
  
  return (
    <div className="profile-skeleton">
      <Skeleton 
        circle 
        width={isMobile ? 60 : 80}   // Smaller on mobile
        height={isMobile ? 60 : 80} 
      />
      <Skeleton width="80%" height={isMobile ? 18 : 24} />
    </div>
  );
};
```

**Why**: Mobile screens have less space. Smaller skeletons reduce visual clutter and improve performance.

---

## ❌ DON'T: Use Desktop Dimensions on Mobile

### Bad Example
```jsx
<div className="profile-skeleton">
  <Skeleton circle width={120} height={120} />  {/* Too big for mobile */}
  <Skeleton width="60%" height={32} />
</div>
```

**Why**: Oversized skeletons on small screens feel clunky and waste viewport space.

---

## ✅ DO: Test with Real Network Conditions

### Good Example
```bash
# Chrome DevTools Network Throttling
- Fast 3G: 1.6 Mbps, 562ms RTT
- Slow 3G: 400 Kbps, 2000ms RTT
- Offline: Test error states

# Test skeleton appears/disappears smoothly across conditions
```

**Why**: Fast wifi ≠ user reality. Skeletons designed for fast connections may behave poorly on slow networks.

---

## ❌ DON'T: Only Test on Fast Connections

### Bad Example
```
✓ Works on office wifi (100 Mbps)
✗ Never tested on 3G/4G
✗ Never tested offline
```

**Why**: Users on slow connections are MOST affected by loading states. They need skeletons more than anyone.

---

## ✅ DO: Combine with Other Loading Optimizations

### Good Example
```jsx
// Skeleton + optimistic UI + caching
const updateProfile = async (data) => {
  // 1. Update UI optimistically
  setProfile(data);
  
  // 2. Send to server
  try {
    await api.updateProfile(data);
  } catch (error) {
    // 3. Revert on error
    setProfile(originalProfile);
    showError(error);
  }
};

// Skeleton for initial load
const ProfilePage = () => {
  const { data, loading } = useProfile({ 
    staleTime: 5 * 60 * 1000  // Cache for 5 min
  });
  
  return loading ? <ProfileSkeleton /> : <Profile data={data} />;
};
```

**Why**: Skeleton screens are one tool. Combine with caching, optimistic updates, and code splitting for best results.

---

## ❌ DON'T: Use Skeletons as Only Performance Strategy

### Bad Example
```jsx
// No caching, no optimization, just skeleton
const SlowPage = () => {
  const [data, loading] = useSlowAPI();  // Always fetches fresh
  return loading ? <Skeleton /> : <Content data={data} />;
};
```

**Why**: Skeleton screens improve perceived performance, not actual performance. Optimize both.

---

## Quick Reference Checklist

**Before Shipping Skeleton Screens:**

- [ ] Skeleton dimensions match actual content (< 5px difference)
- [ ] Animation duration is 1.5-2s (not too fast/slow)
- [ ] `prefers-reduced-motion` respected
- [ ] Dark mode colors defined
- [ ] ARIA attributes added (`role="status"`, `aria-live`, `aria-busy`)
- [ ] Skeleton elements have `aria-hidden="true"`
- [ ] Screen reader announcement added (`.sr-only` text)
- [ ] Error states implemented (no infinite skeletons)
- [ ] Minimum display time set (300ms)
- [ ] Tested on 3G network throttling
- [ ] Mobile-specific sizing considered
- [ ] Aspect ratios preserved for images
- [ ] Progressive loading implemented (where applicable)
- [ ] CLS score measured (target: < 0.05)
- [ ] Works in all supported browsers

---

## Performance Budget

**Target Metrics:**
- Cumulative Layout Shift (CLS): < 0.05
- First Contentful Paint (FCP): < 1.8s
- Time to Interactive (TTI): < 3.5s
- Skeleton CSS size: < 2KB gzipped
- Animation CPU usage: < 5%

**If you exceed these, reconsider:**
- Simpler animation (pulse instead of shimmer)
- Fewer skeleton elements
- Remove skeleton on fast connections (< 100ms load time)
