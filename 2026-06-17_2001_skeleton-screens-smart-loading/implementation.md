# Implementation Guide: Skeleton Screens

## Basic HTML/CSS Implementation

### Simple Skeleton Component

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Skeleton Screen Example</title>
  <style>
    .skeleton {
      background: linear-gradient(
        90deg,
        #e0e0e0 0%,
        #e0e0e0 40%,
        #f5f5f5 50%,
        #e0e0e0 60%,
        #e0e0e0 100%
      );
      background-size: 200% 100%;
      animation: shimmer 1.5s infinite linear;
      border-radius: 4px;
    }

    @keyframes shimmer {
      0% {
        background-position: 200% 0;
      }
      100% {
        background-position: -200% 0;
      }
    }

    /* Respect user motion preferences */
    @media (prefers-reduced-motion: reduce) {
      .skeleton {
        animation: none;
        background: #e0e0e0;
      }
    }

    /* Dark mode support */
    @media (prefers-color-scheme: dark) {
      .skeleton {
        background: linear-gradient(
          90deg,
          #2a2a2a 0%,
          #2a2a2a 40%,
          #3a3a3a 50%,
          #2a2a2a 60%,
          #2a2a2a 100%
        );
      }
    }

    /* Skeleton variants */
    .skeleton-text {
      height: 16px;
      margin-bottom: 8px;
    }

    .skeleton-text.title {
      height: 24px;
      width: 60%;
    }

    .skeleton-circle {
      border-radius: 50%;
      width: 48px;
      height: 48px;
    }

    .skeleton-rect {
      width: 100%;
      height: 200px;
    }

    /* Card layout example */
    .skeleton-card {
      padding: 16px;
      border: 1px solid #e0e0e0;
      border-radius: 8px;
      max-width: 400px;
    }

    .skeleton-header {
      display: flex;
      align-items: center;
      gap: 12px;
      margin-bottom: 16px;
    }

    .skeleton-header-text {
      flex: 1;
    }
  </style>
</head>
<body>
  <div class="skeleton-card" role="status" aria-label="Loading content">
    <div class="skeleton-header">
      <div class="skeleton skeleton-circle"></div>
      <div class="skeleton-header-text">
        <div class="skeleton skeleton-text" style="width: 70%;"></div>
        <div class="skeleton skeleton-text" style="width: 40%;"></div>
      </div>
    </div>
    <div class="skeleton skeleton-rect" style="margin-bottom: 12px;"></div>
    <div class="skeleton skeleton-text"></div>
    <div class="skeleton skeleton-text" style="width: 90%;"></div>
    <div class="skeleton skeleton-text" style="width: 60%;"></div>
    <span class="sr-only">Loading, please wait...</span>
  </div>
</body>
</html>
```

---

## React Implementation

### Reusable Skeleton Component

```jsx
// Skeleton.jsx
import React from 'react';
import './Skeleton.css';

export const Skeleton = ({ 
  width = '100%', 
  height = 16, 
  circle = false,
  className = '',
  count = 1,
  style = {}
}) => {
  const skeletonStyle = {
    width,
    height: circle ? width : height,
    borderRadius: circle ? '50%' : '4px',
    ...style
  };

  const skeletons = Array.from({ length: count }, (_, index) => (
    <div 
      key={index}
      className={`skeleton ${className}`}
      style={skeletonStyle}
      aria-hidden="true"
    />
  ));

  return <>{skeletons}</>;
};

// Skeleton.css
.skeleton {
  background: linear-gradient(
    90deg,
    #e0e0e0 0%,
    #e0e0e0 40%,
    #f5f5f5 50%,
    #e0e0e0 60%,
    #e0e0e0 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite linear;
  display: block;
  margin-bottom: 8px;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

@media (prefers-reduced-motion: reduce) {
  .skeleton {
    animation: none;
    background: #e0e0e0;
  }
}

@media (prefers-color-scheme: dark) {
  .skeleton {
    background: linear-gradient(
      90deg,
      #2a2a2a 0%,
      #2a2a2a 40%,
      #3a3a3a 50%,
      #2a2a2a 60%,
      #2a2a2a 100%
    );
  }
}
```

### User Profile Skeleton

```jsx
// ProfileSkeleton.jsx
import React from 'react';
import { Skeleton } from './Skeleton';

export const ProfileSkeleton = () => {
  return (
    <div className="profile-skeleton" role="status" aria-live="polite" aria-busy="true">
      <div style={{ display: 'flex', alignItems: 'center', gap: '16px', marginBottom: '24px' }}>
        <Skeleton circle width={80} height={80} />
        <div style={{ flex: 1 }}>
          <Skeleton width="60%" height={24} style={{ marginBottom: '12px' }} />
          <Skeleton width="40%" height={16} />
        </div>
      </div>
      
      <Skeleton width="100%" height={200} style={{ marginBottom: '16px' }} />
      
      <Skeleton count={3} height={16} />
      
      <span className="sr-only">Loading profile information</span>
    </div>
  );
};
```

### Smart Loading with Data Fetching

```jsx
// UserProfile.jsx
import React, { useState, useEffect } from 'react';
import { ProfileSkeleton } from './ProfileSkeleton';

export const UserProfile = ({ userId }) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('Failed to fetch user');
        const userData = await response.json();
        
        // Minimum display time to avoid flash
        await new Promise(resolve => setTimeout(resolve, 300));
        
        setData(userData);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  if (error) {
    return (
      <div className="error-state" role="alert">
        <p>Failed to load profile: {error}</p>
        <button onClick={() => window.location.reload()}>Retry</button>
      </div>
    );
  }

  if (loading) {
    return <ProfileSkeleton />;
  }

  return (
    <div className="profile">
      <div className="profile-header">
        <img src={data.avatar} alt={data.name} />
        <div>
          <h1>{data.name}</h1>
          <p>{data.bio}</p>
        </div>
      </div>
      <img src={data.coverImage} alt="" />
      <p>{data.description}</p>
    </div>
  );
};
```

---

## React with Suspense (Modern Approach)

```jsx
// UserProfile.jsx (Suspense version)
import React, { Suspense } from 'react';
import { ProfileSkeleton } from './ProfileSkeleton';
import { getUserData } from './api';

const ProfileContent = ({ userId }) => {
  // This "use" hook throws a promise, triggering Suspense
  const user = use(getUserData(userId));
  
  return (
    <div className="profile">
      <img src={user.avatar} alt={user.name} />
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
};

export const UserProfile = ({ userId }) => {
  return (
    <Suspense fallback={<ProfileSkeleton />}>
      <ProfileContent userId={userId} />
    </Suspense>
  );
};
```

---

## Vue 3 Implementation

```vue
<!-- Skeleton.vue -->
<template>
  <div 
    class="skeleton"
    :class="{ 'skeleton-circle': circle }"
    :style="skeletonStyle"
    aria-hidden="true"
  />
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  width: { type: [String, Number], default: '100%' },
  height: { type: [String, Number], default: 16 },
  circle: { type: Boolean, default: false }
});

const skeletonStyle = computed(() => ({
  width: typeof props.width === 'number' ? `${props.width}px` : props.width,
  height: props.circle 
    ? (typeof props.width === 'number' ? `${props.width}px` : props.width)
    : (typeof props.height === 'number' ? `${props.height}px` : props.height)
}));
</script>

<style scoped>
.skeleton {
  background: linear-gradient(
    90deg,
    #e0e0e0 0%, #e0e0e0 40%, #f5f5f5 50%, #e0e0e0 60%, #e0e0e0 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite linear;
  border-radius: 4px;
  display: block;
  margin-bottom: 8px;
}

.skeleton-circle {
  border-radius: 50%;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
</style>
```

```vue
<!-- UserProfile.vue -->
<template>
  <div v-if="loading" role="status" aria-live="polite">
    <div class="profile-skeleton">
      <Skeleton :width="80" :height="80" circle />
      <Skeleton width="60%" :height="24" />
      <Skeleton width="40%" :height="16" />
    </div>
  </div>
  
  <div v-else-if="error" role="alert" class="error">
    {{ error }}
  </div>
  
  <div v-else class="profile">
    <img :src="user.avatar" :alt="user.name" />
    <h1>{{ user.name }}</h1>
    <p>{{ user.bio }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Skeleton from './Skeleton.vue';

const props = defineProps({
  userId: { type: String, required: true }
});

const loading = ref(true);
const error = ref(null);
const user = ref(null);

onMounted(async () => {
  try {
    const response = await fetch(`/api/users/${props.userId}`);
    user.value = await response.json();
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
});
</script>
```

---

## Advanced: Progressive Loading

```jsx
// ProgressiveSkeleton.jsx
import React, { useState, useEffect } from 'react';
import { Skeleton } from './Skeleton';

export const ProgressiveUserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [loadedCount, setLoadedCount] = useState(0);

  useEffect(() => {
    const fetchUsers = async () => {
      const response = await fetch('/api/users');
      const stream = response.body.getReader();
      const decoder = new TextDecoder();
      
      let buffer = '';
      
      while (true) {
        const { done, value } = await stream.read();
        if (done) break;
        
        buffer += decoder.decode(value, { stream: true });
        const lines = buffer.split('\n');
        buffer = lines.pop(); // Keep incomplete line in buffer
        
        for (const line of lines) {
          if (line.trim()) {
            const user = JSON.parse(line);
            setUsers(prev => [...prev, user]);
            setLoadedCount(prev => prev + 1);
          }
        }
      }
      
      setLoading(false);
    };

    fetchUsers();
  }, []);

  const skeletonCount = Math.max(0, 10 - loadedCount);

  return (
    <div className="user-list">
      {users.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
      
      {loading && Array.from({ length: skeletonCount }, (_, i) => (
        <div key={`skeleton-${i}`} className="user-card-skeleton">
          <Skeleton circle width={48} height={48} />
          <Skeleton width="70%" height={20} />
          <Skeleton width="50%" height={16} />
        </div>
      ))}
    </div>
  );
};
```

---

## Tailwind CSS Implementation

```jsx
// Skeleton.jsx (Tailwind)
export const Skeleton = ({ className = '', circle = false }) => {
  const baseClasses = "animate-pulse bg-gray-200 dark:bg-gray-700";
  const shapeClasses = circle ? "rounded-full" : "rounded";
  
  return (
    <div 
      className={`${baseClasses} ${shapeClasses} ${className}`}
      aria-hidden="true"
    />
  );
};

// Usage
export const ProfileSkeleton = () => (
  <div className="space-y-4" role="status">
    <div className="flex items-center space-x-4">
      <Skeleton circle className="w-20 h-20" />
      <div className="flex-1 space-y-2">
        <Skeleton className="h-6 w-3/5" />
        <Skeleton className="h-4 w-2/5" />
      </div>
    </div>
    <Skeleton className="h-48 w-full" />
    <Skeleton className="h-4 w-full" />
    <Skeleton className="h-4 w-11/12" />
    <Skeleton className="h-4 w-4/5" />
  </div>
);

// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      animation: {
        'pulse': 'pulse 1.5s cubic-bezier(0.4, 0, 0.6, 1) infinite',
      },
    },
  },
  // Enable dark mode
  darkMode: 'media',
};
```

---

## TypeScript Types

```typescript
// types.ts
export interface SkeletonProps {
  width?: string | number;
  height?: string | number;
  circle?: boolean;
  className?: string;
  count?: number;
  style?: React.CSSProperties;
}

export interface LoadingState<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
}

// Skeleton.tsx
import React from 'react';
import { SkeletonProps } from './types';

export const Skeleton: React.FC<SkeletonProps> = ({ 
  width = '100%',
  height = 16,
  circle = false,
  className = '',
  count = 1,
  style = {}
}) => {
  // Implementation from earlier
};
```

---

## Performance Optimization

```jsx
// Memoized skeleton to prevent unnecessary re-renders
import React, { memo } from 'react';

export const Skeleton = memo(({ width, height, circle, className, style }) => {
  const skeletonStyle = {
    width,
    height: circle ? width : height,
    borderRadius: circle ? '50%' : '4px',
    ...style
  };

  return (
    <div 
      className={`skeleton ${className}`}
      style={skeletonStyle}
      aria-hidden="true"
    />
  );
});

Skeleton.displayName = 'Skeleton';
```

---

## Testing

```jsx
// Skeleton.test.jsx
import { render, screen } from '@testing-library/react';
import { Skeleton } from './Skeleton';

describe('Skeleton', () => {
  it('renders with default props', () => {
    const { container } = render(<Skeleton />);
    const skeleton = container.querySelector('.skeleton');
    expect(skeleton).toBeInTheDocument();
  });

  it('renders as circle when circle prop is true', () => {
    const { container } = render(<Skeleton circle />);
    const skeleton = container.querySelector('.skeleton');
    expect(skeleton).toHaveStyle({ borderRadius: '50%' });
  });

  it('renders multiple skeletons with count prop', () => {
    const { container } = render(<Skeleton count={3} />);
    const skeletons = container.querySelectorAll('.skeleton');
    expect(skeletons).toHaveLength(3);
  });

  it('is hidden from screen readers', () => {
    const { container } = render(<Skeleton />);
    const skeleton = container.querySelector('.skeleton');
    expect(skeleton).toHaveAttribute('aria-hidden', 'true');
  });
});
```

---

## Browser Compatibility

All implementations work on:
- Chrome/Edge 90+
- Firefox 88+
- Safari 14+
- Mobile browsers (iOS Safari 14+, Chrome Android 90+)

Fallback for older browsers:
```css
/* For browsers without animation support */
@supports not (animation: shimmer 1s) {
  .skeleton {
    background: #e0e0e0;
  }
}
```
