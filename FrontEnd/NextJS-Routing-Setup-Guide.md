# Next.js Routing - Simple Guide

Next.js has **TWO routing systems**:
- **App Router** (Next.js 13+) - Modern, recommended for new projects
- **Pages Router** (Traditional) - Still widely used, simpler for beginners

Both are file-based routing (no need to write route config like React Router).

---

## Table of Contents
1. [App Router (Modern - Next.js 13+)](#app-router-nextjs-13)
2. [Pages Router (Traditional)](#pages-router-traditional)
3. [Comparison](#comparison-app-vs-pages-router)

---

# App Router (Next.js 13+)

## 1. Installation & Setup

Create a new Next.js project with App Router:
```bash
npx create-next-app@latest my-app

# During setup, choose:
# ✅ Use App Router? → Yes
```

**Project Structure:**
```
my-app/
  ├── app/                 ← All routes go here
  │   ├── layout.js       ← Root layout (wraps all pages)
  │   ├── page.js         ← Home page (/)
  │   ├── about/
  │   │   └── page.js     ← About page (/about)
  │   └── dashboard/
  │       ├── page.js     ← Dashboard (/dashboard)
  │       └── settings/
  │           └── page.js ← Settings (/dashboard/settings)
  ├── public/             ← Static files
  └── package.json
```

---

## 2. Simple Single-Level Routing (App Router)

### Home Page - `app/page.js`
```jsx
// This is the home page - shows at "/"
export default function HomePage() {
  return (
    <div>
      <h1>Home Page</h1>
      <p>Welcome to the home page!</p>
    </div>
  );
}
```

### About Page - `app/about/page.js`
```jsx
// This shows at "/about"
// File path: app/about/page.js → Route: /about
export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>
      <p>This is the about page</p>
    </div>
  );
}
```

### Contact Page - `app/contact/page.js`
```jsx
// This shows at "/contact"
// File path: app/contact/page.js → Route: /contact
export default function ContactPage() {
  return (
    <div>
      <h1>Contact Us</h1>
      <p>Email: contact@example.com</p>
    </div>
  );
}
```

### Root Layout - `app/layout.js`
```jsx
// This wraps ALL pages in your app
// You can add navigation, header, footer here
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        {/* Navigation that appears on all pages */}
        <nav style={{ padding: '20px', background: '#333', color: 'white' }}>
          <a href="/" style={{ marginRight: '15px', color: 'white' }}>Home</a>
          <a href="/about" style={{ marginRight: '15px', color: 'white' }}>About</a>
          <a href="/contact" style={{ color: 'white' }}>Contact</a>
        </nav>
        
        {/* children = the current page content */}
        <main style={{ padding: '20px' }}>
          {children}
        </main>
        
        {/* Footer that appears on all pages */}
        <footer style={{ padding: '20px', background: '#eee' }}>
          © 2026 My Website
        </footer>
      </body>
    </html>
  );
}
```

---

## 3. Dynamic Routing (App Router)

Dynamic routes use **square brackets** in folder names: `[paramName]`

### User Profile - `app/user/[id]/page.js`
```jsx
// This handles URLs like: /user/123, /user/abc, /user/john
// The [id] folder makes this dynamic

// params is an object containing the dynamic values
export default function UserProfilePage({ params }) {
  // params.id will be whatever is in the URL
  // /user/123 → params.id = "123"
  // /user/john → params.id = "john"
  
  return (
    <div>
      <h1>User Profile</h1>
      <p>User ID: {params.id}</p>
      <p>You are viewing the profile for user {params.id}</p>
    </div>
  );
}
```

### Product Page - `app/products/[category]/[productId]/page.js`
```jsx
// Multiple dynamic segments
// URL: /products/electronics/phone-123
// params = { category: "electronics", productId: "phone-123" }

export default function ProductPage({ params }) {
  return (
    <div>
      <h1>Product Details</h1>
      <p>Category: {params.category}</p>
      <p>Product ID: {params.productId}</p>
    </div>
  );
}
```

### Blog Post with Slug - `app/blog/[slug]/page.js`
```jsx
// URL: /blog/how-to-learn-nextjs
// params.slug = "how-to-learn-nextjs"

export default function BlogPostPage({ params }) {
  return (
    <div>
      <h1>Blog Post</h1>
      <p>Reading article: {params.slug}</p>
      {/* In real app, fetch blog content using params.slug */}
    </div>
  );
}
```

---

## 4. Nested Routing (App Router)

Nested routes are created by nesting folders. Layouts can wrap specific sections.

**Folder Structure:**
```
app/
  ├── page.js                    → Home (/)
  ├── layout.js                  → Root layout (wraps everything)
  └── dashboard/
      ├── layout.js              → Dashboard layout (wraps dashboard pages)
      ├── page.js                → Dashboard home (/dashboard)
      ├── profile/
      │   └── page.js            → Profile (/dashboard/profile)
      └── settings/
          ├── page.js            → Settings home (/dashboard/settings)
          └── security/
              └── page.js        → Security (/dashboard/settings/security)
```

### Dashboard Layout - `app/dashboard/layout.js`
```jsx
// This layout wraps ONLY dashboard pages
// It appears on /dashboard, /dashboard/profile, /dashboard/settings, etc.

export default function DashboardLayout({ children }) {
  return (
    <div style={{ display: 'flex' }}>
      {/* Sidebar - appears on all dashboard pages */}
      <aside style={{ width: '200px', background: '#f0f0f0', padding: '20px' }}>
        <h2>Dashboard Menu</h2>
        <ul style={{ listStyle: 'none', padding: 0 }}>
          <li><a href="/dashboard">Overview</a></li>
          <li><a href="/dashboard/profile">Profile</a></li>
          <li><a href="/dashboard/settings">Settings</a></li>
        </ul>
      </aside>
      
      {/* Main content area - the specific page renders here */}
      <main style={{ flex: 1, padding: '20px' }}>
        {children}
      </main>
    </div>
  );
}
```

### Dashboard Home - `app/dashboard/page.js`
```jsx
// Shows at: /dashboard
export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard Overview</h1>
      <p>Welcome to your dashboard!</p>
      <div>
        <h3>Quick Stats</h3>
        <p>Total Users: 1,234</p>
        <p>Revenue: $56,789</p>
      </div>
    </div>
  );
}
```

### Dashboard Profile - `app/dashboard/profile/page.js`
```jsx
// Shows at: /dashboard/profile
// Still wrapped by dashboard/layout.js
export default function ProfilePage() {
  return (
    <div>
      <h1>Your Profile</h1>
      <p>Name: John Doe</p>
      <p>Email: john@example.com</p>
      <button>Edit Profile</button>
    </div>
  );
}
```

### Dashboard Settings - `app/dashboard/settings/page.js`
```jsx
// Shows at: /dashboard/settings
export default function SettingsPage() {
  return (
    <div>
      <h1>Settings</h1>
      <p>Manage your account settings</p>
      <ul>
        <li><a href="/dashboard/settings/security">Security Settings</a></li>
      </ul>
    </div>
  );
}
```

### Security Settings - `app/dashboard/settings/security/page.js`
```jsx
// Shows at: /dashboard/settings/security
// This is deeply nested!
export default function SecuritySettingsPage() {
  return (
    <div>
      <h1>Security Settings</h1>
      <p>Change your password and security preferences</p>
      <button>Change Password</button>
    </div>
  );
}
```

---

## 5. Nested Dynamic Routes (App Router)

Combine nesting with dynamic segments.

**Structure:**
```
app/
  └── store/
      ├── page.js                          → /store
      └── [category]/
          ├── page.js                      → /store/electronics
          └── [productId]/
              └── page.js                  → /store/electronics/laptop-123
```

### Category Page - `app/store/[category]/page.js`
```jsx
// URL: /store/electronics
export default function CategoryPage({ params }) {
  return (
    <div>
      <h1>Category: {params.category}</h1>
      <p>Browse all products in {params.category}</p>
    </div>
  );
}
```

### Product Page - `app/store/[category]/[productId]/page.js`
```jsx
// URL: /store/electronics/laptop-123
// params = { category: "electronics", productId: "laptop-123" }

export default function ProductDetailPage({ params }) {
  return (
    <div>
      <h1>Product Details</h1>
      <p>Category: {params.category}</p>
      <p>Product ID: {params.productId}</p>
      <p>You are viewing product {params.productId} from {params.category}</p>
    </div>
  );
}
```

---

## 6. Catch-All Routes (App Router)

Use `[...slug]` to catch multiple path segments.

### Docs with Catch-All - `app/docs/[...slug]/page.js`
```jsx
// Matches:
// /docs/intro → params.slug = ["intro"]
// /docs/api/users → params.slug = ["api", "users"]
// /docs/guides/getting-started/installation → params.slug = ["guides", "getting-started", "installation"]

export default function DocsPage({ params }) {
  // params.slug is an array of path segments
  const path = params.slug.join('/');
  
  return (
    <div>
      <h1>Documentation</h1>
      <p>Current path: /docs/{path}</p>
      <p>Segments: {JSON.stringify(params.slug)}</p>
    </div>
  );
}
```

---

## 7. Optional Catch-All Routes (App Router)

Use `[[...slug]]` (double brackets) to also match the parent route.

### Docs with Optional Catch-All - `app/docs/[[...slug]]/page.js`
```jsx
// Matches:
// /docs → params.slug = undefined
// /docs/intro → params.slug = ["intro"]
// /docs/api/users → params.slug = ["api", "users"]

export default function DocsPage({ params }) {
  // Check if slug exists
  if (!params.slug) {
    return (
      <div>
        <h1>Documentation Home</h1>
        <p>Welcome to our docs!</p>
      </div>
    );
  }
  
  const path = params.slug.join('/');
  
  return (
    <div>
      <h1>Documentation</h1>
      <p>Reading: /docs/{path}</p>
    </div>
  );
}
```

---

## 8. Route Groups (App Router)

Use `(folderName)` to organize routes without affecting the URL.

**Structure:**
```
app/
  ├── (marketing)/           ← Parentheses = not in URL
  │   ├── about/
  │   │   └── page.js       → /about (not /marketing/about)
  │   └── contact/
  │       └── page.js       → /contact
  └── (shop)/
      ├── products/
      │   └── page.js       → /products
      └── cart/
          └── page.js       → /cart
```

### Marketing Layout - `app/(marketing)/layout.js`
```jsx
// This layout wraps about and contact pages
// But "marketing" doesn't appear in the URL
export default function MarketingLayout({ children }) {
  return (
    <div>
      <header style={{ background: 'blue', color: 'white', padding: '10px' }}>
        Marketing Header
      </header>
      {children}
    </div>
  );
}
```

---

## 9. Parallel Routes (App Router)

Use `@folderName` for multiple sections on one page.

**Structure:**
```
app/
  └── dashboard/
      ├── layout.js
      ├── page.js
      ├── @analytics/         ← @ makes it a slot
      │   └── page.js
      └── @notifications/
          └── page.js
```

### Dashboard Layout with Slots - `app/dashboard/layout.js`
```jsx
// Render multiple sections in parallel
export default function DashboardLayout({ children, analytics, notifications }) {
  return (
    <div>
      <div>{children}</div>
      <div style={{ display: 'flex' }}>
        <div style={{ flex: 1 }}>{analytics}</div>
        <div style={{ flex: 1 }}>{notifications}</div>
      </div>
    </div>
  );
}
```

---

## 10. Not Found Page (App Router)

### Custom 404 - `app/not-found.js`
```jsx
// This shows when no route matches
export default function NotFound() {
  return (
    <div style={{ textAlign: 'center', padding: '50px' }}>
      <h1>404 - Page Not Found</h1>
      <p>Sorry, the page you're looking for doesn't exist.</p>
      <a href="/">Go back home</a>
    </div>
  );
}
```

---

# Pages Router (Traditional)

## 1. Installation & Setup

Create a new Next.js project with Pages Router:
```bash
npx create-next-app@latest my-app

# During setup, choose:
# ❌ Use App Router? → No
```

**Project Structure:**
```
my-app/
  ├── pages/               ← All routes go here
  │   ├── index.js        ← Home page (/)
  │   ├── about.js        ← About page (/about)
  │   ├── _app.js         ← Root component (wraps all pages)
  │   └── api/            ← API routes
  ├── public/             ← Static files
  └── package.json
```

---

## 2. Simple Single-Level Routing (Pages Router)

### Home Page - `pages/index.js`
```jsx
// This is the home page - shows at "/"
// File: pages/index.js → Route: /

export default function HomePage() {
  return (
    <div>
      <h1>Home Page</h1>
      <p>Welcome to the home page!</p>
    </div>
  );
}
```

### About Page - `pages/about.js`
```jsx
// File: pages/about.js → Route: /about
export default function AboutPage() {
  return (
    <div>
      <h1>About Us</h1>
      <p>This is the about page</p>
    </div>
  );
}
```

### Contact Page - `pages/contact.js`
```jsx
// File: pages/contact.js → Route: /contact
export default function ContactPage() {
  return (
    <div>
      <h1>Contact Us</h1>
      <p>Email: contact@example.com</p>
    </div>
  );
}
```

### App Wrapper - `pages/_app.js`
```jsx
// This wraps ALL pages - add global stuff here
// Similar to layout.js in App Router

export default function MyApp({ Component, pageProps }) {
  return (
    <>
      {/* Navigation appears on all pages */}
      <nav style={{ padding: '20px', background: '#333', color: 'white' }}>
        <a href="/" style={{ marginRight: '15px', color: 'white' }}>Home</a>
        <a href="/about" style={{ marginRight: '15px', color: 'white' }}>About</a>
        <a href="/contact" style={{ color: 'white' }}>Contact</a>
      </nav>
      
      {/* Component = the current page */}
      <main style={{ padding: '20px' }}>
        <Component {...pageProps} />
      </main>
      
      {/* Footer appears on all pages */}
      <footer style={{ padding: '20px', background: '#eee' }}>
        © 2026 My Website
      </footer>
    </>
  );
}
```

---

## 3. Dynamic Routing (Pages Router)

Dynamic routes use **square brackets** in file names: `[paramName].js`

### User Profile - `pages/user/[id].js`
```jsx
// This handles URLs like: /user/123, /user/abc, /user/john
// File: pages/user/[id].js

import { useRouter } from 'next/router';

export default function UserProfilePage() {
  // useRouter hook gives access to the URL parameters
  const router = useRouter();
  
  // router.query contains all dynamic parameters
  // /user/123 → router.query.id = "123"
  const { id } = router.query;
  
  return (
    <div>
      <h1>User Profile</h1>
      <p>User ID: {id}</p>
      <p>You are viewing the profile for user {id}</p>
    </div>
  );
}
```

### Product Page - `pages/products/[category]/[productId].js`
```jsx
// Multiple dynamic segments
// URL: /products/electronics/phone-123
// File structure: pages/products/[category]/[productId].js

import { useRouter } from 'next/router';

export default function ProductPage() {
  const router = useRouter();
  
  // router.query has both parameters
  const { category, productId } = router.query;
  
  return (
    <div>
      <h1>Product Details</h1>
      <p>Category: {category}</p>
      <p>Product ID: {productId}</p>
    </div>
  );
}
```

### Blog Post - `pages/blog/[slug].js`
```jsx
// URL: /blog/how-to-learn-nextjs
import { useRouter } from 'next/router';

export default function BlogPostPage() {
  const router = useRouter();
  const { slug } = router.query;
  
  return (
    <div>
      <h1>Blog Post</h1>
      <p>Reading article: {slug}</p>
    </div>
  );
}
```

---

## 4. Nested Routing (Pages Router)

Nesting is done with folders, similar to App Router.

**Folder Structure:**
```
pages/
  ├── index.js                   → Home (/)
  ├── _app.js                    → Root wrapper
  └── dashboard/
      ├── index.js               → Dashboard home (/dashboard)
      ├── profile.js             → Profile (/dashboard/profile)
      └── settings/
          ├── index.js           → Settings home (/dashboard/settings)
          └── security.js        → Security (/dashboard/settings/security)
```

### Dashboard Home - `pages/dashboard/index.js`
```jsx
// Shows at: /dashboard
// index.js in a folder becomes the folder's default page

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard Overview</h1>
      <p>Welcome to your dashboard!</p>
      
      {/* Links to nested pages */}
      <ul>
        <li><a href="/dashboard/profile">Go to Profile</a></li>
        <li><a href="/dashboard/settings">Go to Settings</a></li>
      </ul>
    </div>
  );
}
```

### Dashboard Profile - `pages/dashboard/profile.js`
```jsx
// Shows at: /dashboard/profile
export default function ProfilePage() {
  return (
    <div>
      <h1>Your Profile</h1>
      <p>Name: John Doe</p>
      <p>Email: john@example.com</p>
      <button>Edit Profile</button>
    </div>
  );
}
```

### Settings Home - `pages/dashboard/settings/index.js`
```jsx
// Shows at: /dashboard/settings
export default function SettingsPage() {
  return (
    <div>
      <h1>Settings</h1>
      <p>Manage your account settings</p>
      <ul>
        <li><a href="/dashboard/settings/security">Security Settings</a></li>
      </ul>
    </div>
  );
}
```

### Security Settings - `pages/dashboard/settings/security.js`
```jsx
// Shows at: /dashboard/settings/security
export default function SecuritySettingsPage() {
  return (
    <div>
      <h1>Security Settings</h1>
      <p>Change your password and security preferences</p>
      <button>Change Password</button>
    </div>
  );
}
```

---

## 5. Nested Dynamic Routes (Pages Router)

**Structure:**
```
pages/
  └── store/
      ├── index.js                     → /store
      └── [category]/
          ├── index.js                 → /store/electronics
          └── [productId].js           → /store/electronics/laptop-123
```

### Category Page - `pages/store/[category]/index.js`
```jsx
// URL: /store/electronics
import { useRouter } from 'next/router';

export default function CategoryPage() {
  const router = useRouter();
  const { category } = router.query;
  
  return (
    <div>
      <h1>Category: {category}</h1>
      <p>Browse all products in {category}</p>
    </div>
  );
}
```

### Product Page - `pages/store/[category]/[productId].js`
```jsx
// URL: /store/electronics/laptop-123
import { useRouter } from 'next/router';

export default function ProductDetailPage() {
  const router = useRouter();
  const { category, productId } = router.query;
  
  return (
    <div>
      <h1>Product Details</h1>
      <p>Category: {category}</p>
      <p>Product ID: {productId}</p>
      <p>You are viewing product {productId} from {category}</p>
    </div>
  );
}
```

---

## 6. Catch-All Routes (Pages Router)

Use `[...slug].js` to catch multiple path segments.

### Docs with Catch-All - `pages/docs/[...slug].js`
```jsx
// Matches:
// /docs/intro → slug = ["intro"]
// /docs/api/users → slug = ["api", "users"]
// /docs/guides/getting-started/installation → slug = ["guides", "getting-started", "installation"]

import { useRouter } from 'next/router';

export default function DocsPage() {
  const router = useRouter();
  
  // router.query.slug is an array of path segments
  const { slug } = router.query;
  
  // Convert array to string path
  const path = slug ? slug.join('/') : '';
  
  return (
    <div>
      <h1>Documentation</h1>
      <p>Current path: /docs/{path}</p>
      <p>Segments: {JSON.stringify(slug)}</p>
    </div>
  );
}
```

---

## 7. Optional Catch-All Routes (Pages Router)

Use `[[...slug]].js` (double brackets) to also match the parent route.

### Docs with Optional Catch-All - `pages/docs/[[...slug]].js`
```jsx
// Matches:
// /docs → slug = undefined
// /docs/intro → slug = ["intro"]
// /docs/api/users → slug = ["api", "users"]

import { useRouter } from 'next/router';

export default function DocsPage() {
  const router = useRouter();
  const { slug } = router.query;
  
  // Check if slug exists
  if (!slug) {
    return (
      <div>
        <h1>Documentation Home</h1>
        <p>Welcome to our docs!</p>
      </div>
    );
  }
  
  const path = slug.join('/');
  
  return (
    <div>
      <h1>Documentation</h1>
      <p>Reading: /docs/{path}</p>
    </div>
  );
}
```

---

## 8. Custom 404 Page (Pages Router)

### Not Found - `pages/404.js`
```jsx
// This shows when no route matches
export default function NotFoundPage() {
  return (
    <div style={{ textAlign: 'center', padding: '50px' }}>
      <h1>404 - Page Not Found</h1>
      <p>Sorry, the page you're looking for doesn't exist.</p>
      <a href="/">Go back home</a>
    </div>
  );
}
```

---

## 9. Layout Pattern (Pages Router)

Since Pages Router doesn't have built-in layouts, you create them manually.

### Layout Component - `components/DashboardLayout.js`
```jsx
// Reusable layout component
export default function DashboardLayout({ children }) {
  return (
    <div style={{ display: 'flex' }}>
      {/* Sidebar */}
      <aside style={{ width: '200px', background: '#f0f0f0', padding: '20px' }}>
        <h2>Dashboard Menu</h2>
        <ul style={{ listStyle: 'none', padding: 0 }}>
          <li><a href="/dashboard">Overview</a></li>
          <li><a href="/dashboard/profile">Profile</a></li>
          <li><a href="/dashboard/settings">Settings</a></li>
        </ul>
      </aside>
      
      {/* Main content */}
      <main style={{ flex: 1, padding: '20px' }}>
        {children}
      </main>
    </div>
  );
}
```

### Using Layout - `pages/dashboard/index.js`
```jsx
import DashboardLayout from '../../components/DashboardLayout';

export default function DashboardPage() {
  return (
    <DashboardLayout>
      <h1>Dashboard Overview</h1>
      <p>Welcome to your dashboard!</p>
    </DashboardLayout>
  );
}
```

---

# Comparison: App vs Pages Router

## File Structure Comparison

### Simple Route
**App Router:**
```
app/about/page.js → /about
```
**Pages Router:**
```
pages/about.js → /about
```

### Dynamic Route
**App Router:**
```
app/user/[id]/page.js → /user/:id
```
**Pages Router:**
```
pages/user/[id].js → /user/:id
```

### Nested Route
**App Router:**
```
app/dashboard/settings/page.js → /dashboard/settings
```
**Pages Router:**
```
pages/dashboard/settings.js → /dashboard/settings
OR
pages/dashboard/settings/index.js → /dashboard/settings
```

---

## Key Differences

### 1. File Names
- **App Router**: Every page MUST be named `page.js`
- **Pages Router**: File name becomes the route

### 2. Getting URL Parameters
**App Router:**
```jsx
export default function Page({ params }) {
  return <div>{params.id}</div>
}
```

**Pages Router:**
```jsx
import { useRouter } from 'next/router';

export default function Page() {
  const router = useRouter();
  return <div>{router.query.id}</div>
}
```

### 3. Layouts
**App Router:**
```jsx
// app/dashboard/layout.js
export default function Layout({ children }) {
  return <div>{children}</div>
}
```

**Pages Router:**
```jsx
// Wrap manually in each page
import Layout from '../components/Layout';

export default function Page() {
  return <Layout><div>Content</div></Layout>
}
```

### 4. Navigation
**Both use Next.js Link component:**
```jsx
import Link from 'next/link';

// In your component
<Link href="/about">About</Link>
<Link href={`/user/${userId}`}>User Profile</Link>
```

---

## Which One Should You Use?

### Use App Router If:
- ✅ Starting a NEW project
- ✅ Want modern React features (Server Components)
- ✅ Need better layouts and nested routing
- ✅ Want improved performance

### Use Pages Router If:
- ✅ Working on an EXISTING project
- ✅ Need simpler mental model
- ✅ Prefer traditional React patterns
- ✅ Have existing Pages Router tutorials/resources

---

## Quick Reference

### Route Types Summary

| Route Type | App Router | Pages Router | Example URL |
|------------|------------|--------------|-------------|
| **Home** | `app/page.js` | `pages/index.js` | `/` |
| **About** | `app/about/page.js` | `pages/about.js` | `/about` |
| **Dynamic** | `app/user/[id]/page.js` | `pages/user/[id].js` | `/user/123` |
| **Nested** | `app/blog/post/page.js` | `pages/blog/post.js` | `/blog/post` |
| **Catch-all** | `app/docs/[...slug]/page.js` | `pages/docs/[...slug].js` | `/docs/a/b/c` |
| **Optional** | `app/docs/[[...slug]]/page.js` | `pages/docs/[[...slug]].js` | `/docs` or `/docs/a` |

### Special Files

| Purpose | App Router | Pages Router |
|---------|------------|--------------|
| **Root wrapper** | `app/layout.js` | `pages/_app.js` |
| **404 page** | `app/not-found.js` | `pages/404.js` |
| **Loading** | `app/loading.js` | N/A |
| **Error** | `app/error.js` | `pages/_error.js` |

---

## Summary

### App Router Key Points:
- All pages must be named `page.js`
- Layouts are built-in with `layout.js`
- Get params from `params` prop
- More powerful, modern approach
- Better for complex apps

### Pages Router Key Points:
- File name = route name
- Use `useRouter()` hook for params
- Manual layout implementation
- Simpler to understand
- Great for simple apps

**Both are great!** Choose based on your project needs. Next.js supports both, and you can even use them together in the same project (though not recommended).

Happy routing! 🚀
