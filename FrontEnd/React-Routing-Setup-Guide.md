# React Routing - Simple Guide

## 1. Installation

First, install React Router:
```bash
npm install react-router-dom
```

---

## 2. Basic Setup with Provider

The `BrowserRouter` is the Provider that wraps your entire app to enable routing.

**index.js or main.jsx**
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
  {/* BrowserRouter is the Provider - it wraps your App */}
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

---

## 3. Simple Single-Level Routing

**App.js**
```jsx
import React from 'react';
import { Routes, Route } from 'react-router-dom';

// Simple page components
function Home() {
  return <h1>Home Page</h1>;
}

function About() {
  return <h1>About Page</h1>;
}

function Contact() {
  return <h1>Contact Page</h1>;
}

function App() {
  return (
    <div>
      {/* Routes container holds all your Route definitions */}
      <Routes>
        {/* Each Route maps a path to a component */}
        {/* path="/" means this shows on the home page */}
        <Route path="/" element={<Home />} />
        
        {/* path="/about" means this shows when URL is /about */}
        <Route path="/about" element={<About />} />
        
        <Route path="/contact" element={<Contact />} />
        
        {/* Catch-all route for 404 pages */}
        <Route path="*" element={<h1>404 - Page Not Found</h1>} />
      </Routes>
    </div>
  );
}

export default App;
```

---

## 4. Route with URL Parameters

```jsx
import { useParams } from 'react-router-dom';

// Component that uses URL parameter
function UserProfile() {
  // useParams hook extracts parameters from the URL
  const { userId } = useParams();
  
  return (
    <div>
      <h1>User Profile</h1>
      <p>User ID: {userId}</p>
    </div>
  );
}

// In your Routes:
<Route path="/user/:userId" element={<UserProfile />} />

// When URL is /user/123, userId will be "123"
```

---

## 5. Nested Routing - Method 1 (Using Outlet)

This is the recommended way for nested routing.

**App.js**
```jsx
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import Dashboard from './Dashboard';
import Profile from './Profile';
import Settings from './Settings';

function Home() {
  return <h1>Home Page</h1>;
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      
      {/* Parent route */}
      <Route path="/dashboard" element={<Dashboard />}>
        {/* Child routes - these render inside <Outlet /> in Dashboard */}
        {/* index means this is the default child route */}
        <Route index element={<div>Dashboard Overview</div>} />
        
        {/* These are nested routes */}
        <Route path="profile" element={<Profile />} />
        <Route path="settings" element={<Settings />} />
      </Route>
    </Routes>
  );
}

export default App;
```

**Dashboard.js** (Parent Component)
```jsx
import React from 'react';
import { Outlet } from 'react-router-dom';

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      {/* 
        Outlet is a placeholder where child routes will render
        - /dashboard shows "Dashboard Overview"
        - /dashboard/profile shows Profile component
        - /dashboard/settings shows Settings component
      */}
      <Outlet />
    </div>
  );
}

export default Dashboard;
```

**Profile.js**
```jsx
function Profile() {
  return <h2>Your Profile Page</h2>;
}

export default Profile;
```

**Settings.js**
```jsx
function Settings() {
  return <h2>Settings Page</h2>;
}

export default Settings;
```

---

## 6. Nested Routing - Method 2 (Routes inside Component)

**App.js**
```jsx
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import Dashboard from './Dashboard';

function Home() {
  return <h1>Home Page</h1>;
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      
      {/* 
        /dashboard/* allows nested routes inside Dashboard component
        The * means "match anything after /dashboard"
      */}
      <Route path="/dashboard/*" element={<Dashboard />} />
    </Routes>
  );
}

export default App;
```

**Dashboard.js** (Has its own Routes)
```jsx
import React from 'react';
import { Routes, Route } from 'react-router-dom';

function DashboardHome() {
  return <h2>Dashboard Overview</h2>;
}

function Profile() {
  return <h2>Your Profile</h2>;
}

function Settings() {
  return <h2>Settings</h2>;
}

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      {/* Nested Routes defined here */}
      <Routes>
        {/* / here means /dashboard (relative to parent) */}
        <Route path="/" element={<DashboardHome />} />
        
        {/* profile means /dashboard/profile */}
        <Route path="profile" element={<Profile />} />
        
        {/* settings means /dashboard/settings */}
        <Route path="settings" element={<Settings />} />
      </Routes>
    </div>
  );
}

export default Dashboard;
```

---

## 7. Routes Defined in Separate File (Array Configuration)

**routes.js** (Configuration file)
```jsx
import Home from './pages/Home';
import About from './pages/About';
import Dashboard from './pages/Dashboard';
import Profile from './pages/Profile';
import Settings from './pages/Settings';

// Define all routes as an array of objects
const routes = [
  {
    path: '/',
    element: <Home />
  },
  {
    path: '/about',
    element: <About />
  },
  {
    // Parent route with nested children
    path: '/dashboard',
    element: <Dashboard />,
    // Children routes
    children: [
      {
        // index: true means this is the default child
        index: true,
        element: <div>Dashboard Overview</div>
      },
      {
        path: 'profile', // Will be /dashboard/profile
        element: <Profile />
      },
      {
        path: 'settings', // Will be /dashboard/settings
        element: <Settings />
      }
    ]
  },
  {
    // 404 route
    path: '*',
    element: <h1>Page Not Found</h1>
  }
];

export default routes;
```

**App.js - Method 1: Using useRoutes Hook**
```jsx
import React from 'react';
import { useRoutes } from 'react-router-dom';
import routes from './routes';

function App() {
  // useRoutes automatically converts array to route elements
  const routing = useRoutes(routes);
  
  return (
    <div>
      {/* This renders all routes from the array */}
      {routing}
    </div>
  );
}

export default App;
```

**App.js - Method 2: Using Helper Function**
```jsx
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import routes from './routes';

function App() {
  // Helper function to render routes from array
  const renderRoutes = (routeArray) => {
    return routeArray.map((route, index) => {
      // Check if route has children (nested routes)
      if (route.children) {
        return (
          // Parent route with children
          <Route key={index} path={route.path} element={route.element}>
            {/* Loop through all children and render them */}
            {route.children.map((child, childIndex) => (
              <Route
                key={childIndex}
                index={child.index} // For default child route
                path={child.path}   // Child path
                element={child.element} // Child component
              />
            ))}
          </Route>
        );
      }
      
      // Simple route without children
      return (
        <Route
          key={index}
          path={route.path}
          element={route.element}
        />
      );
    });
  };

  return (
    <div>
      <Routes>
        {/* Call helper function to render all routes */}
        {renderRoutes(routes)}
      </Routes>
    </div>
  );
}

export default App;
```

**How the Helper Function Works:**

1. **routeArray.map()** - Loops through each route object in the array
2. **Checks if route has children** - If yes, it's a nested route
3. **For nested routes:**
   - Creates parent `<Route>` with path and element
   - Maps through `children` array
   - Creates child `<Route>` for each child object
4. **For simple routes:**
   - Just creates a single `<Route>` with path and element
5. **key prop** - React needs unique key for each item in a loop

**index.js** (Don't forget the Provider)
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

---

## Summary

### Key Components:
1. **BrowserRouter** - Provider that wraps your app (put in index.js)
2. **Routes** - Container for all Route components
3. **Route** - Maps a path to a component
4. **Outlet** - Placeholder for nested child routes
5. **useParams** - Hook to get URL parameters from the path
6. **useRoutes** - Hook to use array-based route configuration

### Path Examples:
- `/` - Home page
- `/about` - About page
- `/user/:userId` - Dynamic route with parameter
- `/dashboard` - Parent route
- `/dashboard/profile` - Nested route
- `*` - Catch-all for 404 pages

### Nested Route Notes:
- Use `index` for default child route
- Use `<Outlet />` in parent component to show child routes
- Child paths are relative to parent (no need to repeat `/dashboard`)
