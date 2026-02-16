# Next.js Complete Guide - From React to Next.js

**A Simple Guide for React Developers Learning Next.js**

---

## Table of Contents
1. [Introduction & Benefits](#introduction--benefits)
2. [Rendering Methods](#rendering-methods)
3. [Server vs Client Components](#server-vs-client-components)
4. [Routing - Page Router vs App Router](#routing---page-router-vs-app-router)
5. [Image Optimization](#image-optimization)
6. [Quick Reference](#quick-reference)

---

## Introduction & Benefits

### Why Use Next.js?

**Next.js** is a React framework that makes your React apps faster, better for SEO, and easier to build.

**Key Benefits:**

1. **Better Performance** → Pages load faster for users
2. **SEO Friendly** → Google can read your content easily
3. **File-based Routing** → No need to setup routes manually
4. **Built-in Optimizations** → Images, fonts, scripts automatically optimized
5. **Full-stack Ready** → Write backend APIs in the same project
6. **Great Developer Experience** → Fast refresh, TypeScript support

---

## Rendering Methods

### Understanding the 4 Rendering Methods

#### 1. CSR (Client-Side Rendering)
**Traditional React** - Everything renders in the browser using JavaScript.

- ❌ Slow initial page load
- ❌ Bad for SEO
- ✅ Good for apps after login

---

#### 2. SSR (Server-Side Rendering)
**Fresh data on every request** - HTML is generated on the server each time someone visits.

**When to use:** User dashboards, personalized content, real-time data

**Example 1: Using export const dynamic**

```typescript
// app/user/dashboard/page.tsx

// Force this page to always render on server for every request
export const dynamic = 'force-dynamic';

async function Dashboard() {
  // This fetch happens on server on EVERY request
  // User always gets fresh data
  const user = await fetch('https://api.example.com/user/profile');
  
  // Component returns JSX...
}
```

**Example 2: Using cache option**

```typescript
// app/orders/page.tsx

async function OrdersPage() {
  // cache: 'no-store' = SSR (no caching, always fresh)
  const orders = await fetch('https://api.example.com/orders', {
    cache: 'no-store' // Always fetch fresh data from server
  });
  
  const data = await orders.json();
  
  // Component returns JSX...
}
```

**How SSR Works:**
1. User visits page → Request goes to Next.js server (Node.js)
2. Server executes your code → Server fetches data from API
3. Server converts React to HTML → Server sends complete HTML
4. Browser displays content immediately → User sees page fast!
5. JavaScript loads in background → Page becomes interactive

---

#### 3. SSG (Static Site Generation)
**Built once at build time** - HTML is created when you deploy, not when users visit.

**When to use:** Blog posts, documentation, marketing pages (content that rarely changes)

**Example 1: Using export const dynamic**

```typescript
// app/about/page.tsx

// Force this page to be static (built at build time only)
export const dynamic = 'force-static';

async function AboutPage() {
  // This fetch happens ONLY during build time
  // Data is frozen at build time
  const company = await fetch('https://api.example.com/company-info');
  
  // Component returns JSX...
}
```

**Example 2: Using cache option**

```typescript
// app/blog/page.tsx

async function BlogPage() {
  // cache: 'force-cache' = SSG (cache forever)
  const posts = await fetch('https://api.example.com/posts', {
    cache: 'force-cache' // Data cached forever at build time
  });
  
  const data = await posts.json();
  
  // Component returns JSX...
}
```

**How SSG Works:**
1. During build (`npm run build`) → Next.js generates HTML files
2. HTML stored on server/CDN → When user visits, just send the file
3. Super fast (no computation needed) → Same HTML for all users

---

#### 4. ISR (Incremental Static Regeneration)
**Static pages that auto-update** - Built once, but regenerates after a time period.

**When to use:** Product listings, news feeds, content that updates periodically

**Example 1: Using export const revalidate**

```typescript
// app/products/page.tsx

// Regenerate this page every 60 seconds
export const revalidate = 60;

async function ProductsPage() {
  // First request after 60 seconds triggers rebuild
  // User sees old data, next user sees fresh data
  const products = await fetch('https://api.example.com/products');
  
  // Component returns JSX...
}
```

**Example 2: Using next.revalidate in fetch**

```typescript
// app/news/page.tsx

async function NewsPage() {
  // This specific fetch revalidates every 120 seconds
  const news = await fetch('https://api.example.com/news', {
    next: { revalidate: 120 } // Updates every 2 minutes
  });
  
  const data = await news.json();
  
  // Component returns JSX...
}
```

**How ISR Works:**
1. First build → Creates static HTML
2. User visits → Gets cached HTML (fast!)
3. After 60 seconds → Next request triggers background rebuild
4. New HTML generated → Future visitors get updated content

---

### Quick Comparison Table

| Method | When Data Fetched | Use Case | Speed |
|--------|------------------|----------|-------|
| **CSR** | In browser | Interactive apps | Slow initial load |
| **SSR** | Every request on server | Dashboards, personalized | Medium |
| **SSG** | At build time | Blogs, docs | Very fast |
| **ISR** | At build + periodic updates | Product pages, news | Fast + Fresh |

---

## Server vs Client Components

### Server Components (Default in App Router)

**Run only on the server** - Code never goes to the browser.

```typescript
// app/products/page.tsx
// NO 'use client' = Server Component (default)

async function ProductsPage() {
  // ✅ Can fetch data directly
  // ✅ Can access database
  // ✅ Can use secret API keys (secure!)
  // ✅ Zero JavaScript sent to browser
  // ❌ Cannot use useState, useEffect
  // ❌ Cannot use onClick, onChange
  // ❌ Cannot access browser APIs (window, localStorage)
  
  const data = await fetch('https://api.example.com/products', {
    headers: {
      'Authorization': process.env.SECRET_KEY // Safe! Never exposed
    }
  });
  
  // Component returns JSX...
}
```

**Benefits:**
- Smaller JavaScript bundle (faster page load)
- Direct database access
- Secure (sensitive code stays on server)

---

### Client Components (Interactive)

**Run in browser** - Needed for interactivity.

```typescript
// app/components/Counter.tsx
'use client' // 👈 This keyword makes it a Client Component!

import { useState } from 'react';

export default function Counter() {
  // ✅ Can use all React hooks
  // ✅ Can use onClick, onChange, onSubmit
  // ✅ Can access window, localStorage, browser APIs
  // ❌ Larger JavaScript bundle
  // ❌ Cannot directly access database
  
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

**When to use 'use client':**
- Need useState, useEffect, or any React hooks
- Need event handlers (onClick, onChange)
- Need browser APIs (localStorage, window)
- Need third-party libraries that use browser APIs

---

### Best Practice: Mix Both

```typescript
// app/dashboard/page.tsx (Server Component)

import UserStats from './UserStats'; // Client Component

async function Dashboard() {
  // Server: Fetch data (secure, no client JS)
  const stats = await fetch('https://api.example.com/stats', {
    headers: { 'Authorization': process.env.API_KEY }
  }).then(res => res.json());
  
  return (
    <div>
      <h1>Dashboard</h1>
      {/* Pass data to Client Component for interactivity */}
      <UserStats data={stats} />
    </div>
  );
}
```

```typescript
// app/dashboard/UserStats.tsx (Client Component)
'use client'

import { useState } from 'react';

export default function UserStats({ data }) {
  const [view, setView] = useState('chart');
  
  return (
    <div>
      <button onClick={() => setView('chart')}>Chart View</button>
      <button onClick={() => setView('table')}>Table View</button>
      {view === 'chart' ? <Chart data={data} /> : <Table data={data} />}
    </div>
  );
}
```

**Rule of Thumb:** Use Server Components by default, add 'use client' only when needed!

---

## Routing - Page Router vs App Router

### Page Router (Old - Next.js 12 and below)

Uses `pages/` folder for routing.

```
pages/
├── index.tsx              → /
├── about.tsx              → /about
├── blog/
│   ├── index.tsx          → /blog
│   └── [slug].tsx         → /blog/post-1
└── products/
    └── [id].tsx           → /products/123
```

**Example: SSG in Page Router**

```typescript
// pages/blog/index.tsx

// Function to fetch data at build time
export async function getStaticProps() {
  const posts = await fetch('https://api.example.com/posts');
  const data = await posts.json();
  
  return {
    props: { posts: data },
    revalidate: 60 // ISR - regenerate every 60 seconds
  };
}

function BlogPage({ posts }) {
  // Component receives data as props
  return <div>{/* Render posts */}</div>;
}

export default BlogPage;
```

**Example: SSR in Page Router**

```typescript
// pages/dashboard.tsx

// Function runs on every request (SSR)
export async function getServerSideProps() {
  const user = await fetch('https://api.example.com/user');
  const data = await user.json();
  
  return {
    props: { user: data }
  };
}

function Dashboard({ user }) {
  return <div>{/* Render user data */}</div>;
}

export default Dashboard;
```

---

### App Router (New - Next.js 13+) ⭐ Recommended

Uses `app/` folder for routing.

```
app/
├── page.tsx               → /
├── about/
│   └── page.tsx           → /about
├── blog/
│   ├── page.tsx           → /blog
│   └── [slug]/
│       └── page.tsx       → /blog/post-1
└── products/
    └── [id]/
        └── page.tsx       → /products/123
```

**Example: SSG in App Router**

```typescript
// app/blog/page.tsx

// Simpler! Just use async function
async function BlogPage() {
  const posts = await fetch('https://api.example.com/posts', {
    cache: 'force-cache' // SSG
  });
  const data = await posts.json();
  
  // No need for getStaticProps!
  return <div>{/* Render posts */}</div>;
}

export default BlogPage;
```

**Example: SSR in App Router**

```typescript
// app/dashboard/page.tsx

// Much cleaner than Page Router!
async function Dashboard() {
  const user = await fetch('https://api.example.com/user', {
    cache: 'no-store' // SSR
  });
  const data = await user.json();
  
  // No need for getServerSideProps!
  return <div>{/* Render user data */}</div>;
}

export default Dashboard;
```

---

### Special Files in App Router

```
app/
├── layout.tsx             → Shared layout for all pages
├── loading.tsx            → Loading UI (shown while page loads)
├── error.tsx              → Error UI (shown when error occurs)
├── not-found.tsx          → 404 page
└── page.tsx               → Actual page content
```

**Example: Layout (Shared UI)**

```typescript
// app/layout.tsx

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <header>My Website</header>
        <nav>{/* Navigation */}</nav>
        <main>{children}</main> {/* Page content goes here */}
        <footer>© 2026</footer>
      </body>
    </html>
  );
}
```

**Benefits of App Router:**
- Cleaner syntax (no getStaticProps, getServerSideProps)
- Better layouts (shared UI without re-rendering)
- Better loading states
- Server Components by default
- Easier to understand

---

## Image Optimization

Next.js automatically optimizes images!

### Why Use Next.js Image Component?

**Without optimization (regular `<img>`):**
- ❌ Large file sizes (slow loading)
- ❌ Wrong formats (no WebP)
- ❌ No lazy loading (loads all at once)
- ❌ No responsive sizing

**With Next.js `<Image>` component:**
- ✅ Automatic resizing
- ✅ Automatic format conversion (WebP)
- ✅ Lazy loading (loads when needed)
- ✅ Prevents layout shift
- ✅ Responsive images

---

### Image Component Examples

```typescript
// app/gallery/page.tsx
import Image from 'next/image';

export default function Gallery() {
  return (
    <div>
      {/* Example 1: Local image */}
      <Image
        src="/photos/beach.jpg"     // Image in public/photos/
        alt="Beach sunset"
        width={800}                  // Required
        height={600}                 // Required
        priority                     // Load immediately (for hero images)
      />
      {/* Auto converts to WebP, optimizes size */}
      
      {/* Example 2: Remote image */}
      <Image
        src="https://example.com/photo.jpg"
        alt="Remote photo"
        width={500}
        height={300}
        // Lazy loaded by default (good for below-fold images)
      />
      
      {/* Example 3: Fill container (responsive) */}
      <div style={{ position: 'relative', width: '100%', height: '400px' }}>
        <Image
          src="/banner.jpg"
          alt="Banner"
          fill                       // Fills parent container
          style={{ objectFit: 'cover' }}
        />
      </div>
      
      {/* ❌ DON'T USE: Regular img tag */}
      <img src="/photo.jpg" alt="Not optimized" />
      {/* No optimization, no lazy loading, large file size */}
    </div>
  );
}
```

---

### Image Configuration

```typescript
// next.config.js

module.exports = {
  images: {
    // Allow images from external domains
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'example.com',
      },
      {
        protocol: 'https',
        hostname: 'cdn.example.com',
      },
    ],
    
    // Image formats (default: ['image/webp'])
    formats: ['image/webp', 'image/avif'],
    
    // Custom image sizes
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
  },
};
```

---

### Image Best Practices

1. **Use `priority` for above-fold images** (hero images, logos)
2. **Let other images lazy load** (images below the fold)
3. **Always provide `alt` text** (for accessibility and SEO)
4. **Use `fill` for responsive images**
5. **Configure `remotePatterns`** for external images

---

## SEO Optimization with Metadata

### Making Pages SEO-Friendly

Next.js provides a simple Metadata API - just export a metadata object from your page component!

---

### Basic Static Metadata

```typescript
// app/about/page.tsx
import { Metadata } from 'next';

// Export metadata - Next.js adds it to <head> automatically
export const metadata: Metadata = {
  title: 'About Us - My Company',
  description: 'Learn about our company history and mission',
  keywords: ['about', 'company', 'mission', 'team'],
};

export default function AboutPage() {
  return <div>About content...</div>;
}
```

---

### Homepage Example

```typescript
// app/page.tsx
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'My Company - Best Products Online',
  description: 'Shop the best products with free shipping and great prices',
  keywords: ['products', 'online shopping', 'free shipping'],
};

export default function HomePage() {
  return <div>Home content...</div>;
}
```

---

### Contact Page Example

```typescript
// app/contact/page.tsx
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Contact Us - My Company',
  description: 'Get in touch with our team for support and inquiries',
  keywords: ['contact', 'support', 'customer service'],
};

export default function ContactPage() {
  return <div>Contact form...</div>;
}
```

---

### SEO Best Practices

1. **Unique title for each page** - Keep it 50-60 characters
2. **Clear description** - 150-160 characters works best
3. **Relevant keywords** - Use 3-5 keywords naturally
4. **Be specific** - Describe exactly what's on the page

---

## Quick Reference

### Export Constants Cheat Sheet

```typescript
// SSR - Always fresh data
export const dynamic = 'force-dynamic';
export const revalidate = 0;

// SSG - Static forever
export const dynamic = 'force-static';
export const revalidate = false;

// ISR - Revalidate after time
export const revalidate = 60; // seconds
```

---

### Fetch Options Cheat Sheet

```typescript
// SSR - No caching
fetch(url, { cache: 'no-store' })

// SSG - Cache forever
fetch(url, { cache: 'force-cache' })

// ISR - Revalidate after time
fetch(url, { next: { revalidate: 60 } })
```

---

### When to Use What?

| Scenario | Method | Configuration |
|----------|--------|---------------|
| User dashboard | SSR | `cache: 'no-store'` |
| Blog posts | SSG | `cache: 'force-cache'` |
| Product listings | ISR | `revalidate: 300` |
| News feed | ISR | `revalidate: 60` |
| Marketing page | SSG | `dynamic: 'force-static'` |
| Real-time data | SSR | `dynamic: 'force-dynamic'` |

---

### Component Type Decision Tree

```
Do you need interactivity? (onClick, useState, etc.)
├─ YES → Use Client Component ('use client')
└─ NO → Use Server Component (default)
           └─ Better performance, smaller bundle
```

---

### Router Comparison

| Feature | Page Router | App Router |
|---------|-------------|------------|
| Data Fetching | `getStaticProps`, `getServerSideProps` | `async` functions |
| Layouts | Custom `_app.tsx` | Built-in `layout.tsx` |
| Loading States | Custom | Built-in `loading.tsx` |
| Error Handling | Custom | Built-in `error.tsx` |
| Syntax | More boilerplate | Cleaner, simpler |
| Recommendation | Legacy projects | New projects ⭐ |

---

## Summary

### Key Takeaways

1. **Next.js makes React apps faster and better for SEO**
2. **Use SSR for dynamic content, SSG for static, ISR for periodic updates**
3. **Server Components by default (better performance)**
4. **Add 'use client' only when you need interactivity**
5. **App Router is simpler and more powerful than Page Router**
6. **Always use `<Image>` component for automatic optimization**
7. **Control rendering with export constants and fetch options**

### Getting Started

```bash
# Create new Next.js app
npx create-next-app@latest my-app

# Navigate to project
cd my-app

# Run development server
npm run dev

# Build for production
npm run build

# Start production server
npm start
```

---

**Happy coding with Next.js! 🚀**

---

*Last Updated: February 2026*
*For more info: https://nextjs.org/docs*
