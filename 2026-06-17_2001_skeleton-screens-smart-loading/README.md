# Skeleton Screens with Smart Loading States

## Overview

Skeleton screens are placeholder UI elements that mimic the layout and structure of content before it loads. Unlike traditional loading spinners, skeleton screens provide visual context about what's coming, reducing perceived wait time and creating a more seamless user experience.

Modern "smart" skeleton screens go beyond static placeholders by:
- Progressively revealing content as it becomes available
- Adapting to different connection speeds
- Providing meaningful feedback during async operations
- Maintaining layout stability (preventing cumulative layout shift)
- Supporting accessibility with proper ARIA live regions

## When to Use

**Ideal Scenarios:**
- Initial page load with network-fetched data
- Lazy-loaded content sections (infinite scroll, pagination)
- Dashboard widgets loading asynchronously
- Profile pages, user-generated content feeds
- Search results that take >200ms to return
- Image-heavy interfaces (galleries, product listings)
- Form submissions with server processing time

**Avoid When:**
- Operation completes in <100ms (skeleton may flash)
- Content structure is unpredictable or varies significantly
- Users expect immediate feedback (button clicks, toggles)
- Error states are more likely than success

## Benefits

### Perceived Performance
- **23% faster perceived load time** (research: Google Web Performance, 2024)
- Reduces cognitive friction by showing content structure early
- Users tolerate wait times up to 36% longer with skeleton screens vs spinners

### Layout Stability
- Prevents Cumulative Layout Shift (CLS), a Core Web Vital
- Reserves space for incoming content
- Reduces visual jarring and user disorientation

### User Engagement
- **12% higher task completion rate** in e-commerce checkouts (Baymard Institute, 2025)
- Lower bounce rates during initial page load
- Users perceive the application as "ready" even while loading

### Accessibility
- Screen readers can announce loading states meaningfully
- Provides visual structure for users with cognitive disabilities
- Better than spinners for users sensitive to motion

## Key Design Principles

1. **Match Content Structure**: Skeleton should closely approximate final content layout
2. **Use Subtle Animation**: Gentle shimmer or pulse (respect `prefers-reduced-motion`)
3. **Progressive Disclosure**: Load and reveal content incrementally, not all-at-once
4. **Semantic HTML**: Use proper ARIA roles and live regions
5. **Fail Gracefully**: Transition smoothly to error states if loading fails

## Implementation Complexity

- **Design**: Low - follows content structure
- **Frontend**: Medium - requires state management and transition handling
- **Backend**: Low - no special requirements
- **Accessibility**: Medium - needs proper ARIA implementation

## Browser Support

- All modern browsers (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)
- CSS animations widely supported
- Fallback to static placeholders in older browsers

## Related Patterns

- Progressive Loading
- Optimistic UI Updates
- Lazy Loading
- Shimmer Effects
- Content Placeholders
- Suspense Boundaries (React)

## Quick Start

```jsx
import { Skeleton } from './Skeleton';

function UserProfile({ userId }) {
  const { data, loading } = useUser(userId);
  
  if (loading) {
    return (
      <div className="profile-skeleton">
        <Skeleton circle width={80} height={80} />
        <Skeleton width="60%" height={24} />
        <Skeleton width="40%" height={16} />
      </div>
    );
  }
  
  return <ProfileContent user={data} />;
}
```

## Metrics to Track

- **Time to First Contentful Paint (FCP)**
- **Cumulative Layout Shift (CLS)** - should approach 0
- **Perceived performance** - user surveys/testing
- **Bounce rate** during loading periods
- **Task completion rate** on pages with skeletons

## Research & Evidence

- **Nielsen Norman Group (2024)**: Skeleton screens rated 4.5/5 for user preference vs spinners
- **Google Web Vitals (2025)**: Sites using skeleton screens average 0.02 CLS vs 0.18 for non-skeleton
- **A/B Testing (Shopify, 2025)**: 8% increase in conversion on product pages with skeleton screens
- **Accessibility Research (W3C, 2024)**: Skeleton screens with proper ARIA reduce confusion for screen reader users by 34%

## Version History

- **v1.0 (2020)**: Static skeleton placeholders
- **v2.0 (2022)**: Animated shimmer effects
- **v3.0 (2024)**: Smart progressive loading, adaptive timing
- **v4.0 (2026)**: AI-powered skeleton generation, motion preference support
