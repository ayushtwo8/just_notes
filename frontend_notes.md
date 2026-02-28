# 🎨 Complete Frontend Development Notes
### From Basics to Advanced | Real-World Examples Included

---

# TABLE OF CONTENTS

1. [Reconcilers & Frontend Frameworks](#1-reconcilers--frontend-frameworks)
2. [React Beginner to Advanced](#2-react-beginner-to-advanced)
3. [Internals of State & Context API](#3-internals-of-state--context-api)
4. [State Management with Recoil](#4-state-management-with-recoil)
5. [CSS You Need to Know — Flexbox & Styling](#5-css-you-need-to-know--flexbox--styling)
6. [Frontend UI Frameworks & Tailwind CSS Deep Dive](#6-frontend-ui-frameworks--tailwind-css-deep-dive)
7. [Containerization & Docker](#7-containerization--docker)
8. [Next.js](#8-nextjs)
9. [Custom Hooks](#9-custom-hooks)
10. [In-House Auth using NextAuth](#10-in-house-auth-using-nextauth)

---

# 1. Reconcilers & Frontend Frameworks

## 1.1 The Problem Before Modern Frameworks

Before frameworks like React, developers manipulated the DOM directly using vanilla JavaScript. This worked but caused major problems at scale.

```html
<!-- Old way: Direct DOM manipulation -->
<div id="counter">0</div>
<button onclick="increment()">+</button>

<script>
  let count = 0;

  function increment() {
    count++;
    // You had to manually find and update DOM elements
    document.getElementById('counter').textContent = count;

    // In real apps this became chaos:
    // - Update counter text
    // - Update button disabled state
    // - Update a summary elsewhere on the page
    // - Sync with server
    // - Handle edge cases
    // → Bugs everywhere, impossible to maintain at scale
  }
</script>
```

**Problems with direct DOM manipulation:**
- State scattered across global variables
- No single source of truth for UI
- Manual DOM updates = bugs when you forget to update one element
- Impossible to reuse UI pieces
- Performance issues from excessive DOM operations

---

## 1.2 What is a Frontend Framework?

A framework solves these problems by providing:
- **Component model** — reusable, self-contained UI pieces
- **Declarative UI** — you describe *what* the UI should look like, the framework figures out *how* to update the DOM
- **Reactive state** — when data changes, the UI updates automatically

```
Imperative (old):                    Declarative (modern):
"Find the element,                   "The UI should look like
 change its text,                     this when count = 5"
 update the class,                   → Framework figures out
 check if button should               what DOM changes to make
 be disabled..."
```

---

## 1.3 Virtual DOM

React (and similar frameworks) use a **Virtual DOM** — a lightweight JavaScript copy of the real DOM.

```
Real DOM:    Heavy, slow to update, causes reflows/repaints
Virtual DOM: Just plain JavaScript objects, very fast to create and diff

Process:
1. State changes → React creates new Virtual DOM tree
2. React diffs new VDOM against previous VDOM (reconciliation)
3. React calculates minimum DOM operations needed
4. React batches and applies only necessary real DOM changes
```

```js
// What JSX compiles to (Virtual DOM nodes)
// <div className="card"><h1>Hello</h1></div>
// becomes:
React.createElement('div', { className: 'card' },
  React.createElement('h1', null, 'Hello')
)
// which creates a plain JS object like:
{
  type: 'div',
  props: { className: 'card' },
  children: [{
    type: 'h1',
    props: null,
    children: ['Hello']
  }]
}
```

---

## 1.4 The Reconciler

The **reconciler** is the algorithm that determines what changed between two Virtual DOM trees and what real DOM operations are needed.

React's reconciler is called **Fiber** (since React 16).

**Diffing rules React uses:**

```
Rule 1: If element type changes, destroy the old tree and build new one
  <div>...</div>  →  <span>...</span>
  Everything inside is re-created from scratch

Rule 2: If element type stays the same, just update changed attributes
  <div className="old"> → <div className="new">
  Only the className attribute changes in the DOM

Rule 3: Lists need `key` prop so React can match old and new items
  Without key: React matches items by position → wrong
  With key: React matches by identity → correct
```

```jsx
// ❌ Without keys — React gets confused when list order changes
{users.map(user => <UserCard user={user} />)}

// ✅ With stable unique keys
{users.map(user => <UserCard key={user.id} user={user} />)}

// ❌ Never use array index as key when list can reorder
{users.map((user, i) => <UserCard key={i} user={user} />)}
// If user[0] is deleted, all other keys shift → React re-renders everything wrong

// ✅ Index as key is ok for static lists that never reorder
{['Home', 'About', 'Contact'].map((page, i) => <NavLink key={i} to={page} />)}
```

---

## 1.5 React Fiber — How It Works

Fiber is React's reconciler that makes rendering **interruptible and prioritized**.

```
Old reconciler (before React 16):
  Render was synchronous — once started, it couldn't stop
  → Large renders would block the browser → janky UI

Fiber:
  Breaks render work into tiny "units of work" (fibers)
  Can pause, abort, or resume work
  High-priority updates (user clicks) interrupt low-priority ones (data fetching)

Priority levels (high to low):
  1. Discrete (click, keypress) — must respond immediately
  2. Continuous (hover, scroll) — can be slightly delayed
  3. Default (data fetches, state updates)
  4. Idle (prefetching, background work)
```

**React 18 Concurrent Features** (built on Fiber):

```jsx
import { useTransition, useDeferredValue, Suspense } from 'react';

// useTransition — mark a state update as non-urgent
function SearchPage() {
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    setQuery(e.target.value); // urgent — update input immediately

    startTransition(() => {
      setSearchResults(search(e.target.value)); // non-urgent — can wait
    });
  };

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <ResultsList />
    </>
  );
}

// useDeferredValue — defer expensive re-renders
function ProductList({ searchTerm }) {
  const deferredTerm = useDeferredValue(searchTerm);
  // deferredTerm lags behind searchTerm while typing
  // ProductList re-renders only when React has time
  return <ExpensiveList filter={deferredTerm} />;
}
```

---

## 1.6 Framework Comparison

| Feature | React | Vue | Angular | Svelte |
|---------|-------|-----|---------|--------|
| Type | Library | Framework | Full Framework | Compiler |
| Learning Curve | Medium | Low | High | Low |
| Virtual DOM | Yes | Yes | No (incremental DOM) | No (compiles away) |
| Bundle Size | ~45KB | ~33KB | ~130KB | Very small |
| Language | JSX + JS/TS | Templates + JS/TS | TypeScript | Svelte syntax |
| State Mgmt | External (Redux, Recoil, Zustand) | Built-in (Pinia) | Built-in (Services) | Built-in stores |
| Used by | Meta, Airbnb, Netflix | Alibaba, Gitlab | Google, Microsoft | Smaller teams |

**Why React dominates the industry:** Largest ecosystem, most jobs, backed by Meta, works everywhere (web, mobile with RN, desktop with Electron, server with Next.js).

---

# 2. React Beginner to Advanced

## 2.1 JSX

JSX lets you write HTML-like syntax in JavaScript. It's not HTML — it compiles to `React.createElement()` calls.

```jsx
// JSX rules:
// 1. Must return a single root element (or Fragment)
// 2. className instead of class (class is a JS keyword)
// 3. htmlFor instead of for (for is a JS keyword)
// 4. All tags must close (<input /> not <input>)
// 5. JavaScript expressions inside {}
// 6. Inline style = object with camelCase keys

function UserCard({ user }) {
  const isAdmin = user.role === 'admin';

  return (
    <>  {/* Fragment — no extra DOM element */}
      <div
        className={`card ${isAdmin ? 'card--admin' : ''}`}
        style={{ borderColor: isAdmin ? '#ff0000' : '#ccc', padding: '1rem' }}
      >
        <h2>{user.name}</h2>
        <p className="email">{user.email}</p>

        {/* Conditional rendering */}
        {isAdmin && <span className="badge">Admin</span>}
        {user.isPremium ? <PremiumBadge /> : <FreeBadge />}

        {/* Lists */}
        <ul>
          {user.skills.map(skill => (
            <li key={skill.id}>{skill.name}</li>
          ))}
        </ul>

        {/* Expression */}
        <p>Member since {new Date(user.createdAt).getFullYear()}</p>
      </div>
    </>
  );
}
```

---

## 2.2 Components

Components are the building blocks of React UIs. They're just functions that return JSX.

```jsx
// Functional component (modern React — use these always)
function Button({ children, onClick, variant = 'primary', disabled = false, isLoading = false }) {
  return (
    <button
      onClick={onClick}
      disabled={disabled || isLoading}
      className={`btn btn--${variant} ${isLoading ? 'btn--loading' : ''}`}
    >
      {isLoading ? <Spinner size="sm" /> : children}
    </button>
  );
}

// Usage
<Button onClick={handleSubmit} variant="danger" isLoading={submitting}>
  Delete Account
</Button>

// Props are immutable — never mutate them!
// ❌ props.count = 5;
// ✅ Call setState or lift state up

// Children prop
function Card({ children, title, footer }) {
  return (
    <div className="card">
      {title && <div className="card__header"><h3>{title}</h3></div>}
      <div className="card__body">{children}</div>
      {footer && <div className="card__footer">{footer}</div>}
    </div>
  );
}

// Usage — children passed between opening/closing tags
<Card title="User Details" footer={<Button>Save</Button>}>
  <UserForm />
</Card>
```

---

## 2.3 useState

`useState` is the most fundamental hook — it adds state to a component.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0); // [state, setter] = useState(initialValue)

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(c => c - 1)}>-</button> {/* Functional update — safer */}
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

**Always use functional updates when new state depends on old state:**

```jsx
// ❌ WRONG — stale closure problem
const [count, setCount] = useState(0);
const handleTripleClick = () => {
  setCount(count + 1); // all three read the same stale count!
  setCount(count + 1);
  setCount(count + 1);
  // Result: count goes from 0 to 1, not 3
};

// ✅ CORRECT — functional update always gets latest value
const handleTripleClick = () => {
  setCount(c => c + 1);
  setCount(c => c + 1);
  setCount(c => c + 1);
  // Result: count goes from 0 to 3 ✓
};
```

**Complex state — objects and arrays:**

```jsx
function UserForm() {
  const [form, setForm] = useState({
    name: '',
    email: '',
    role: 'user'
  });

  // ❌ WRONG — direct mutation, React won't re-render
  const handleChange = (field, value) => {
    form[field] = value; // mutation!
    setForm(form);       // same reference, no re-render
  };

  // ✅ CORRECT — create new object with spread
  const handleChange = (field, value) => {
    setForm(prev => ({ ...prev, [field]: value }));
  };

  // Arrays
  const [items, setItems] = useState([]);

  const addItem = (item) => setItems(prev => [...prev, item]);
  const removeItem = (id) => setItems(prev => prev.filter(item => item.id !== id));
  const updateItem = (id, updates) => setItems(prev =>
    prev.map(item => item.id === id ? { ...item, ...updates } : item)
  );

  return (
    <form>
      <input
        value={form.name}
        onChange={e => handleChange('name', e.target.value)}
      />
      <input
        value={form.email}
        onChange={e => handleChange('email', e.target.value)}
      />
    </form>
  );
}
```

---

## 2.4 useEffect

`useEffect` lets you perform side effects (data fetching, subscriptions, timers, DOM manipulation).

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Effect runs after every render by default
    // With dependency array: runs only when deps change
    // With empty []: runs only once on mount

    let cancelled = false; // prevent setState on unmounted component

    async function fetchUser() {
      try {
        setLoading(true);
        setError(null);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('Failed to fetch user');
        const data = await response.json();

        if (!cancelled) setUser(data); // only update if still mounted
      } catch (err) {
        if (!cancelled) setError(err.message);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }

    fetchUser();

    // Cleanup function — runs before next effect or on unmount
    return () => {
      cancelled = true;
    };
  }, [userId]); // re-run when userId changes

  if (loading) return <Spinner />;
  if (error) return <ErrorMessage message={error} />;
  if (!user) return null;

  return <UserCard user={user} />;
}
```

**Common useEffect patterns:**

```jsx
// 1. Run once on mount (componentDidMount equivalent)
useEffect(() => {
  initAnalytics();
  loadFromLocalStorage();
}, []);

// 2. Run on every render (no deps array) — RARELY needed
useEffect(() => {
  document.title = `Page - ${Math.random()}`;
});

// 3. Cleanup — subscriptions, timers, event listeners
useEffect(() => {
  const timer = setInterval(() => setTime(new Date()), 1000);
  return () => clearInterval(timer); // cleanup on unmount
}, []);

useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);

useEffect(() => {
  const ws = new WebSocket('wss://api.example.com');
  ws.onmessage = (e) => setMessages(prev => [...prev, JSON.parse(e.data)]);
  return () => ws.close();
}, []);

// 4. Sync with external system
useEffect(() => {
  if (isPlaying) {
    audioRef.current.play();
  } else {
    audioRef.current.pause();
  }
}, [isPlaying]);
```

**useEffect Mistakes:**

```jsx
// ❌ MISTAKE: Missing dependency — stale closure
const [user, setUser] = useState(null);

useEffect(() => {
  fetchUser(userId); // if userId is from props, it should be in deps!
}, []); // eslint-disable-line — suppressing warning is a red flag

// ❌ MISTAKE: Object/array in deps — re-runs every render
useEffect(() => {
  fetchData(options);
}, [options]); // options = {} creates new reference every render!

// ✅ FIX: Use primitive values as deps, or useMemo/useCallback
useEffect(() => {
  fetchData(options);
}, [options.page, options.limit, options.filter]); // primitives ✓

// ❌ MISTAKE: Async function directly in useEffect
useEffect(async () => {  // WRONG — async effect returns Promise, not cleanup fn
  const data = await fetchData();
}, []);

// ✅ FIX: Define async function inside effect
useEffect(() => {
  const load = async () => {
    const data = await fetchData();
    setData(data);
  };
  load();
}, []);
```

---

## 2.5 useRef

`useRef` gives you a mutable reference that doesn't trigger re-renders.

```jsx
import { useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const videoRef = useRef(null); // reference to DOM element

  useEffect(() => {
    if (isPlaying) {
      videoRef.current.play();
    } else {
      videoRef.current.pause();
    }
  }, [isPlaying]);

  return <video ref={videoRef} src={src} />;
}

// Store mutable values that don't trigger re-renders
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef(null); // store interval ID without triggering re-render

  const start = () => {
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
  };

  const stop = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <p>{seconds}s</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  );
}

// Store previous value
function usePrevious(value) {
  const ref = useRef(undefined);
  useEffect(() => {
    ref.current = value; // runs after render, so ref.current is previous value during render
  });
  return ref.current;
}

function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);

  return <p>Current: {count}, Previous: {prevCount}</p>;
}
```

---

## 2.6 useMemo & useCallback

Performance optimization hooks — prevent expensive calculations and function recreations on every render.

```jsx
import { useMemo, useCallback } from 'react';

// useMemo — memoize expensive computed values
function ProductList({ products, searchTerm, category }) {
  // ❌ Recalculates on every render, even when unrelated state changes
  const filtered = products
    .filter(p => p.category === category)
    .filter(p => p.name.toLowerCase().includes(searchTerm.toLowerCase()))
    .sort((a, b) => a.price - b.price);

  // ✅ Only recalculates when products, searchTerm, or category change
  const filtered = useMemo(() => {
    return products
      .filter(p => p.category === category)
      .filter(p => p.name.toLowerCase().includes(searchTerm.toLowerCase()))
      .sort((a, b) => a.price - b.price);
  }, [products, searchTerm, category]);

  return filtered.map(p => <ProductCard key={p.id} product={p} />);
}

// useCallback — memoize functions (prevent child re-renders)
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  // ❌ New function reference on every render → ChildComponent always re-renders
  const handleDelete = (id) => {
    deleteItem(id);
  };

  // ✅ Same function reference unless deps change → ChildComponent skips re-render
  const handleDelete = useCallback((id) => {
    deleteItem(id);
  }, []); // no deps — function never changes

  const handleUpdate = useCallback((id, data) => {
    updateItem(id, { ...data, updatedBy: name }); // uses name from closure
  }, [name]); // re-creates when name changes

  return (
    <>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      {/* ChildComponent is memo-wrapped, so it only re-renders when its props change */}
      <ChildComponent onDelete={handleDelete} onUpdate={handleUpdate} />
    </>
  );
}

// React.memo — skip re-render if props haven't changed
const ChildComponent = React.memo(function ChildComponent({ onDelete, onUpdate }) {
  console.log('ChildComponent rendered'); // should only log when needed
  return <div>...</div>;
});
```

**When NOT to use useMemo/useCallback:**
- Simple calculations — overhead of memo > benefit
- When component always re-renders anyway (parent isn't memoized)
- For primitive values — React already optimizes these

---

## 2.7 Error Boundaries

Catch JavaScript errors in the component tree and show fallback UI instead of crashing the whole app.

```jsx
// Class component — only way to create error boundaries (no hook equivalent yet)
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Log to error tracking service (Sentry, etc.)
    errorTracker.capture(error, { extra: errorInfo });
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <button onClick={() => this.setState({ hasError: false, error: null })}>
            Try Again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}

// Usage — wrap sections of your app
function App() {
  return (
    <ErrorBoundary fallback={<FullPageError />}>
      <Header />
      <ErrorBoundary fallback={<p>Dashboard unavailable</p>}>
        <Dashboard />
      </ErrorBoundary>
      <ErrorBoundary fallback={<p>Sidebar unavailable</p>}>
        <Sidebar />
      </ErrorBoundary>
    </ErrorBoundary>
  );
}
```

---

## 2.8 React.Suspense & Lazy Loading

Code-split your app so users only download what they need.

```jsx
import { Suspense, lazy } from 'react';

// ❌ Eager load — entire app downloaded upfront
import Dashboard from './Dashboard';
import Settings from './Settings';

// ✅ Lazy load — downloaded only when user navigates there
const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));
const AdminPanel = lazy(() => import('./AdminPanel'));

function App() {
  return (
    <Router>
      <Suspense fallback={<PageLoader />}>
        <Routes>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/settings" element={<Settings />} />
          <Route path="/admin" element={<AdminPanel />} />
        </Routes>
      </Suspense>
    </Router>
  );
}

// Data Suspense (React 18 + data libraries like React Query)
function UserProfile({ userId }) {
  return (
    <Suspense fallback={<ProfileSkeleton />}>
      <ProfileData userId={userId} />
    </Suspense>
  );
}
```

---

# 3. Internals of State & Context API

## 3.1 How React State Actually Works

When you call `setState`, React doesn't immediately re-render. It **schedules** a re-render.

```
setState called
     │
React schedules re-render (batches multiple setState calls)
     │
React runs your component function again (re-render)
     │
React gets new Virtual DOM
     │
React diffs new VDOM vs old VDOM (reconciliation)
     │
React updates only changed parts of real DOM
     │
useEffect cleanup runs (if deps changed)
     │
Browser paints
     │
useEffect runs (if deps changed)
```

**Batching — React groups multiple setState calls:**

```jsx
// React 18 — all setState calls inside event handlers, setTimeout, promises are batched
function handleClick() {
  setCount(c => c + 1);  // React doesn't re-render yet
  setName('Alice');       // React doesn't re-render yet
  setLoading(false);      // React doesn't re-render yet
  // → React re-renders ONCE with all three updates applied (React 18+)
}

// React 17 and below — only event handlers were batched
// setTimeout, promises, native event listeners each caused separate re-renders
// React 18 fixed this with automatic batching everywhere
```

**State is captured in closures — this causes the "stale closure" problem:**

```jsx
function SearchBox() {
  const [query, setQuery] = useState('');

  useEffect(() => {
    const timer = setTimeout(() => {
      // ❌ query here is the value at the time this closure was created!
      // If user types fast, this will always search the first value
      console.log('Searching for:', query);
    }, 500);
    return () => clearTimeout(timer);
  }, []); // Missing query in deps → stale closure

  // ✅ Include query in deps — effect re-runs each time query changes
  useEffect(() => {
    const timer = setTimeout(() => {
      console.log('Searching for:', query);
    }, 500);
    return () => clearTimeout(timer);
  }, [query]);
}
```

---

## 3.2 Context API

Context lets you pass data through the component tree without prop drilling.

**The Problem (Prop Drilling):**

```jsx
// ❌ Prop drilling — passing theme through every level
function App() {
  const [theme, setTheme] = useState('light');
  return <Layout theme={theme} setTheme={setTheme} />;
}
function Layout({ theme, setTheme }) {
  return <Sidebar theme={theme} setTheme={setTheme} />;
}
function Sidebar({ theme, setTheme }) {
  return <Button theme={theme} setTheme={setTheme} />;
}
function Button({ theme, setTheme }) {
  // Finally uses it here, but had to pass through 3 components
  return <button className={theme}>Toggle</button>;
}
```

**The Solution (Context):**

```jsx
import { createContext, useContext, useState, useCallback } from 'react';

// 1. Create context
const ThemeContext = createContext(null);

// 2. Create a provider component
export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = useCallback(() => {
    setTheme(t => t === 'light' ? 'dark' : 'light');
  }, []);

  const value = useMemo(() => ({
    theme,
    toggleTheme,
    isDark: theme === 'dark'
  }), [theme, toggleTheme]);

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Create a custom hook for consuming the context
export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// 4. Wrap your app
function App() {
  return (
    <ThemeProvider>
      <Router>
        <AppRoutes />
      </Router>
    </ThemeProvider>
  );
}

// 5. Use anywhere — no prop drilling!
function Button() {
  const { theme, toggleTheme } = useTheme();
  return (
    <button className={`btn btn--${theme}`} onClick={toggleTheme}>
      Switch to {theme === 'light' ? 'dark' : 'light'} mode
    </button>
  );
}
```

---

## 3.3 Real-World Context Example — Auth Context

```tsx
interface User {
  id: string;
  name: string;
  email: string;
  role: 'user' | 'admin';
}

interface AuthContextType {
  user: User | null;
  isLoading: boolean;
  login: (email: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
  isAuthenticated: boolean;
}

const AuthContext = createContext<AuthContextType | null>(null);

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  // Check if user is already logged in on mount
  useEffect(() => {
    async function initAuth() {
      try {
        const token = localStorage.getItem('token');
        if (token) {
          const me = await api.get('/auth/me');
          setUser(me.data);
        }
      } catch {
        localStorage.removeItem('token');
      } finally {
        setIsLoading(false);
      }
    }
    initAuth();
  }, []);

  const login = useCallback(async (email: string, password: string) => {
    const { data } = await api.post('/auth/login', { email, password });
    localStorage.setItem('token', data.accessToken);
    setUser(data.user);
  }, []);

  const logout = useCallback(async () => {
    await api.post('/auth/logout');
    localStorage.removeItem('token');
    setUser(null);
  }, []);

  const value = useMemo(() => ({
    user,
    isLoading,
    login,
    logout,
    isAuthenticated: !!user
  }), [user, isLoading, login, logout]);

  if (isLoading) return <FullPageLoader />;

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

export const useAuth = () => {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error('useAuth must be used within AuthProvider');
  return ctx;
};

// Protected Route component
function ProtectedRoute({ children }: { children: React.ReactNode }) {
  const { isAuthenticated } = useAuth();
  return isAuthenticated ? <>{children}</> : <Navigate to="/login" />;
}
```

---

## 3.4 Context Performance Pitfalls

Context re-renders ALL consumers when the value changes.

```jsx
// ❌ PROBLEM: Putting unrelated state in one context
const AppContext = createContext();

function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  const [cartItems, setCartItems] = useState([]);
  const [notifications, setNotifications] = useState([]);

  // Every time notifications change, ALL consumers re-render
  // even components that only care about theme!
  const value = { user, setUser, theme, setTheme, cartItems, setCartItems, notifications };

  return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}

// ✅ SOLUTION: Split into separate contexts by concern
const UserContext = createContext();
const ThemeContext = createContext();
const CartContext = createContext();
const NotificationContext = createContext();

// Now theme changes only re-render theme consumers
// cart changes only re-render cart consumers
```

---

# 4. State Management with Recoil

## 4.1 Why Recoil?

Context API limitations:
- Re-renders all consumers on any change
- Not ideal for high-frequency updates
- Hard to share state between unrelated components

Recoil provides:
- **Atoms** — minimal units of state (like useState, but global)
- **Selectors** — derived state (like useMemo, but global)
- Only components using a specific atom re-render when that atom changes

## 4.2 Setup

```bash
npm install recoil
```

```jsx
// index.jsx — Wrap app with RecoilRoot
import { RecoilRoot } from 'recoil';

function App() {
  return (
    <RecoilRoot>
      <MyApp />
    </RecoilRoot>
  );
}
```

## 4.3 Atoms

```jsx
import { atom, useRecoilState, useRecoilValue, useSetRecoilState } from 'recoil';

// Define atoms — global state pieces
const userAtom = atom({
  key: 'user',         // unique key — must be unique across your whole app
  default: null,       // initial value
});

const themeAtom = atom({
  key: 'theme',
  default: 'light',
});

const cartAtom = atom({
  key: 'cart',
  default: [],
});

// Use atoms in components
function Header() {
  const user = useRecoilValue(userAtom);         // read only — no re-render on write
  const [theme, setTheme] = useRecoilState(themeAtom); // read + write
  const setCart = useSetRecoilState(cartAtom);   // write only — no re-render on read

  return (
    <header>
      <span>Welcome, {user?.name}</span>
      <button onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </header>
  );
}
```

## 4.4 Selectors — Derived State

```jsx
import { selector, selectorFamily } from 'recoil';

// Selector — derived from one or more atoms
const cartTotalSelector = selector({
  key: 'cartTotal',
  get: ({ get }) => {
    const cart = get(cartAtom);
    return cart.reduce((total, item) => total + item.price * item.quantity, 0);
  }
});

const cartItemCountSelector = selector({
  key: 'cartItemCount',
  get: ({ get }) => {
    const cart = get(cartAtom);
    return cart.reduce((count, item) => count + item.quantity, 0);
  }
});

// selectorFamily — parameterized selectors
const productSelector = selectorFamily({
  key: 'product',
  get: (productId) => async ({ get }) => {
    const response = await fetch(`/api/products/${productId}`);
    return response.json();
  }
});

// Usage
function CartSummary() {
  const total = useRecoilValue(cartTotalSelector);
  const count = useRecoilValue(cartItemCountSelector);

  return (
    <div>
      <span>{count} items</span>
      <span>Total: ${total.toFixed(2)}</span>
    </div>
  );
}

function ProductPage({ productId }) {
  const product = useRecoilValue(productSelector(productId));
  return <ProductDetails product={product} />;
}
```

## 4.5 Real-World Recoil — E-commerce Cart

```tsx
// atoms/cart.ts
export const cartAtom = atom<CartItem[]>({
  key: 'cart',
  default: [],
  // Persist to localStorage
  effects: [
    ({ setSelf, onSet }) => {
      const saved = localStorage.getItem('cart');
      if (saved) setSelf(JSON.parse(saved));

      onSet((newValue, _, isReset) => {
        if (isReset) {
          localStorage.removeItem('cart');
        } else {
          localStorage.setItem('cart', JSON.stringify(newValue));
        }
      });
    }
  ]
});

// hooks/useCart.ts
export function useCart() {
  const [cart, setCart] = useRecoilState(cartAtom);

  const addItem = useCallback((product: Product, quantity = 1) => {
    setCart(prev => {
      const existing = prev.find(item => item.productId === product.id);
      if (existing) {
        return prev.map(item =>
          item.productId === product.id
            ? { ...item, quantity: item.quantity + quantity }
            : item
        );
      }
      return [...prev, { productId: product.id, product, quantity }];
    });
  }, [setCart]);

  const removeItem = useCallback((productId: string) => {
    setCart(prev => prev.filter(item => item.productId !== productId));
  }, [setCart]);

  const updateQuantity = useCallback((productId: string, quantity: number) => {
    if (quantity <= 0) {
      removeItem(productId);
      return;
    }
    setCart(prev => prev.map(item =>
      item.productId === productId ? { ...item, quantity } : item
    ));
  }, [setCart, removeItem]);

  const clearCart = useCallback(() => setCart([]), [setCart]);

  return { cart, addItem, removeItem, updateQuantity, clearCart };
}
```

---

# 5. CSS You Need to Know — Flexbox & Styling

## 5.1 The Box Model

Every element is a rectangular box with:

```
┌─────────────────────────────────────┐
│              MARGIN                 │  ← Space outside element
│  ┌───────────────────────────────┐  │
│  │           BORDER              │  │  ← Element border
│  │  ┌─────────────────────────┐  │  │
│  │  │         PADDING         │  │  │  ← Space inside element
│  │  │  ┌───────────────────┐  │  │  │
│  │  │  │      CONTENT      │  │  │  │  ← Actual content
│  │  │  └───────────────────┘  │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

```css
/* box-sizing: border-box — makes sizing intuitive */
* {
  box-sizing: border-box;
  /* width = content + padding + border (NOT + extra)  */
}

.card {
  width: 300px;       /* Total width = exactly 300px */
  padding: 20px;      /* Included in 300px */
  border: 2px solid;  /* Included in 300px */
  /* Without border-box, actual width = 300 + 40 + 4 = 344px */
}
```

---

## 5.2 Flexbox — The Layout Workhorse

Flexbox makes it easy to lay out items in a row or column.

```css
.container {
  display: flex;

  /* Main axis direction */
  flex-direction: row;            /* → (default) */
  flex-direction: row-reverse;    /* ← */
  flex-direction: column;         /* ↓ */
  flex-direction: column-reverse; /* ↑ */

  /* Alignment on main axis (row = horizontal, column = vertical) */
  justify-content: flex-start;    /* items at start */
  justify-content: flex-end;      /* items at end */
  justify-content: center;        /* items in center */
  justify-content: space-between; /* equal space between */
  justify-content: space-around;  /* equal space around */
  justify-content: space-evenly;  /* perfectly even gaps */

  /* Alignment on cross axis (row = vertical, column = horizontal) */
  align-items: stretch;     /* default — stretch to fill */
  align-items: flex-start;  /* align to start */
  align-items: flex-end;    /* align to end */
  align-items: center;      /* center vertically */
  align-items: baseline;    /* align text baselines */

  /* Wrapping */
  flex-wrap: nowrap;  /* default — items overflow */
  flex-wrap: wrap;    /* items wrap to next line */

  /* Gap between items */
  gap: 16px;          /* row and column gap */
  gap: 8px 16px;      /* row-gap column-gap */
}

/* Flex child properties */
.item {
  flex-grow: 0;    /* don't grow (default) */
  flex-grow: 1;    /* grow to fill available space */
  flex-grow: 2;    /* grow twice as fast as flex-grow: 1 items */

  flex-shrink: 1;  /* can shrink (default) */
  flex-shrink: 0;  /* don't shrink — useful for fixed-width items */

  flex-basis: auto;   /* default — use content size */
  flex-basis: 200px;  /* start at 200px before growing/shrinking */

  /* Shorthand */
  flex: 1;          /* flex: 1 1 0 — grow, shrink, start at 0 */
  flex: 0 0 200px;  /* fixed 200px, don't grow or shrink */

  /* Override align-items for just this item */
  align-self: center;

  /* Order */
  order: -1; /* move before other items */
  order: 1;  /* move after */
}
```

**Real-World Flexbox Patterns:**

```css
/* Navbar with logo left, links right */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 24px;
  height: 64px;
}

/* Center anything — most common flexbox trick */
.centered {
  display: flex;
  justify-content: center;
  align-items: center;
  /* For full-page centering: */
  min-height: 100vh;
}

/* Card grid with equal-height cards */
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 24px;
}
.card {
  flex: 1 1 300px; /* grow, shrink, start at 300px — creates responsive grid */
  max-width: 400px;
}

/* Sticky footer pattern */
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
main {
  flex: 1; /* grows to push footer down */
}
footer {
  /* stays at bottom */
}

/* Sidebar layout */
.layout {
  display: flex;
  gap: 24px;
}
.sidebar {
  flex: 0 0 280px; /* fixed 280px, never grow or shrink */
}
.main-content {
  flex: 1; /* take all remaining space */
}
```

---

## 5.3 CSS Grid

Grid is for two-dimensional layouts (rows AND columns simultaneously).

```css
.grid {
  display: grid;

  /* Define columns */
  grid-template-columns: 200px 1fr 200px;   /* sidebar | content | sidebar */
  grid-template-columns: repeat(3, 1fr);    /* 3 equal columns */
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); /* responsive! */

  /* Define rows */
  grid-template-rows: auto 1fr auto;  /* header | content | footer */

  /* Gap */
  gap: 24px;

  /* Named template areas */
  grid-template-areas:
    "header  header  header"
    "sidebar content content"
    "footer  footer  footer";
}

/* Assign elements to areas */
.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer  { grid-area: footer; }

/* Place items manually */
.featured {
  grid-column: 1 / 3;     /* span from column line 1 to 3 */
  grid-row: 1 / 3;        /* span from row line 1 to 3 */
  grid-column: span 2;    /* simpler: span 2 columns */
}
```

---

## 5.4 CSS Custom Properties (Variables)

```css
/* Define variables — usually on :root for global scope */
:root {
  /* Colors */
  --color-primary: #6366f1;
  --color-primary-dark: #4f46e5;
  --color-secondary: #ec4899;
  --color-text: #1f2937;
  --color-text-muted: #6b7280;
  --color-bg: #ffffff;
  --color-bg-secondary: #f9fafb;
  --color-border: #e5e7eb;
  --color-error: #ef4444;
  --color-success: #10b981;

  /* Typography */
  --font-sans: 'Inter', system-ui, sans-serif;
  --font-mono: 'Fira Code', 'Cascadia Code', monospace;
  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;

  /* Spacing */
  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-4: 1rem;     /* 16px */
  --space-8: 2rem;     /* 32px */

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);

  /* Border radius */
  --radius-sm: 4px;
  --radius: 8px;
  --radius-lg: 16px;
  --radius-full: 9999px;

  /* Transitions */
  --transition-fast: 150ms ease;
  --transition: 250ms ease;
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --color-text: #f9fafb;
    --color-bg: #111827;
    --color-bg-secondary: #1f2937;
    --color-border: #374151;
  }
}

/* Or toggle with class */
.dark {
  --color-text: #f9fafb;
  --color-bg: #111827;
}

/* Usage */
.button {
  background: var(--color-primary);
  color: white;
  border-radius: var(--radius);
  padding: var(--space-2) var(--space-4);
  font-family: var(--font-sans);
  transition: background var(--transition-fast);
}
.button:hover {
  background: var(--color-primary-dark);
}
```

---

## 5.5 Responsive Design

```css
/* Mobile-first approach — start with mobile, add complexity for larger screens */

/* Base styles = mobile */
.container {
  padding: 16px;
  width: 100%;
}

/* Tablet and up */
@media (min-width: 768px) {
  .container {
    padding: 24px;
    max-width: 768px;
    margin: 0 auto;
  }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .container {
    padding: 32px;
    max-width: 1200px;
  }
}

/* Common breakpoints */
/* xs:  < 640px   (mobile)        */
/* sm:  ≥ 640px   (large mobile)  */
/* md:  ≥ 768px   (tablet)        */
/* lg:  ≥ 1024px  (desktop)       */
/* xl:  ≥ 1280px  (large desktop) */
/* 2xl: ≥ 1536px  (4K / ultrawide)*/

/* Responsive grid */
.product-grid {
  display: grid;
  grid-template-columns: 1fr;              /* 1 col on mobile */
  gap: 16px;
}
@media (min-width: 640px) {
  .product-grid {
    grid-template-columns: repeat(2, 1fr); /* 2 cols on tablet */
  }
}
@media (min-width: 1024px) {
  .product-grid {
    grid-template-columns: repeat(4, 1fr); /* 4 cols on desktop */
    gap: 24px;
  }
}

/* Or use auto-fit for fully responsive without media queries */
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 24px;
}
```

---

# 6. Frontend UI Frameworks & Tailwind CSS Deep Dive

## 6.1 Why Tailwind?

Traditional CSS:

```css
/* You write CSS in a separate file */
.button {
  display: inline-flex;
  align-items: center;
  padding: 8px 16px;
  background-color: #6366f1;
  color: white;
  border-radius: 6px;
  font-weight: 500;
  /* etc. */
}
```

Tailwind:

```jsx
/* You apply utility classes directly in JSX — no separate CSS file needed */
<button className="inline-flex items-center px-4 py-2 bg-indigo-500 text-white rounded-md font-medium hover:bg-indigo-600 transition-colors">
  Click me
</button>
```

**Pros:** No naming things, no CSS file context switching, predictable, purges unused styles automatically, great for design systems.

**Cons:** Long class names (solved with component extraction), requires learning utility names.

---

## 6.2 Tailwind Fundamentals

```jsx
{/* Spacing — p=padding, m=margin, number = 4px * n */}
<div className="p-4">    {/* padding: 16px */}
<div className="px-4">   {/* padding-left + right: 16px */}
<div className="py-2">   {/* padding-top + bottom: 8px */}
<div className="pt-8">   {/* padding-top: 32px */}
<div className="m-auto"> {/* margin: auto — centering */}
<div className="mt-4 mb-8"> {/* margin-top: 16px, bottom: 32px */}

{/* Sizing */}
<div className="w-full">    {/* width: 100% */}
<div className="w-1/2">     {/* width: 50% */}
<div className="w-64">      {/* width: 256px */}
<div className="max-w-4xl"> {/* max-width: 896px */}
<div className="h-screen">  {/* height: 100vh */}
<div className="min-h-screen"> {/* min-height: 100vh */}

{/* Typography */}
<p className="text-sm">     {/* font-size: 14px */}
<p className="text-xl">     {/* font-size: 20px */}
<p className="text-2xl">    {/* font-size: 24px */}
<p className="font-bold">   {/* font-weight: 700 */}
<p className="font-medium"> {/* font-weight: 500 */}
<p className="text-gray-500"> {/* color */}
<p className="text-center">   {/* text-align: center */}
<p className="truncate">      {/* overflow: ellipsis */}
<p className="leading-relaxed"> {/* line-height: 1.625 */}
<p className="tracking-wide">   {/* letter-spacing: 0.025em */}

{/* Colors — {color}-{shade} */}
<div className="bg-white">          {/* background: white */}
<div className="bg-gray-100">       {/* light gray background */}
<div className="bg-indigo-600">     {/* indigo background */}
<div className="text-gray-900">     {/* dark text */}
<div className="border-gray-200">   {/* border color */}

{/* Flexbox */}
<div className="flex items-center justify-between gap-4">
<div className="flex flex-col gap-2">
<div className="flex-1">  {/* flex: 1 1 0 */}
<div className="flex-none"> {/* flex: none */}

{/* Grid */}
<div className="grid grid-cols-3 gap-6">
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4"> {/* responsive! */}
<div className="col-span-2"> {/* span 2 columns */}

{/* Borders */}
<div className="border border-gray-200">        {/* 1px border */}
<div className="border-2 border-indigo-500">    {/* 2px indigo border */}
<div className="rounded">    {/* border-radius: 4px */}
<div className="rounded-md"> {/* border-radius: 6px */}
<div className="rounded-xl"> {/* border-radius: 12px */}
<div className="rounded-full"> {/* border-radius: 9999px (circle/pill) */}

{/* Shadows */}
<div className="shadow-sm">  {/* subtle shadow */}
<div className="shadow">     {/* default shadow */}
<div className="shadow-lg">  {/* large shadow */}
<div className="shadow-xl">  {/* extra large shadow */}

{/* Position */}
<div className="relative">
<div className="absolute top-0 right-0">
<div className="fixed bottom-4 right-4">
<div className="sticky top-0 z-10">
```

## 6.3 Responsive Prefixes

```jsx
{/* Mobile-first: apply from this breakpoint up */}
{/* sm:640px  md:768px  lg:1024px  xl:1280px  2xl:1536px */}

<div className="
  grid
  grid-cols-1      {/* 1 col on mobile */}
  sm:grid-cols-2   {/* 2 cols at 640px+ */}
  lg:grid-cols-4   {/* 4 cols at 1024px+ */}
">

<p className="text-sm md:text-base lg:text-lg">
  Responsive text size
</p>

<div className="
  flex flex-col    {/* stack on mobile */}
  md:flex-row      {/* side by side on tablet+ */}
  gap-4
">
```

## 6.4 State Variants

```jsx
{/* Hover */}
<button className="bg-indigo-500 hover:bg-indigo-600 transition-colors">

{/* Focus */}
<input className="border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 outline-none">

{/* Active */}
<button className="active:scale-95 transition-transform">

{/* Disabled */}
<button className="bg-indigo-500 disabled:bg-gray-300 disabled:cursor-not-allowed">

{/* Dark mode */}
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">

{/* Group hover — hover on parent affects children */}
<div className="group">
  <img className="group-hover:scale-105 transition-transform" />
  <p className="group-hover:text-indigo-600">Caption</p>
</div>

{/* First/last child */}
<li className="border-b last:border-b-0">

{/* Odd/even */}
<tr className="even:bg-gray-50">
```

## 6.5 Building a Real Component with Tailwind

```jsx
// Production-quality card component
function ProductCard({ product }) {
  const [isWishlisted, setIsWishlisted] = useState(false);

  return (
    <div className="group relative bg-white rounded-xl shadow-sm hover:shadow-md transition-shadow duration-200 overflow-hidden border border-gray-100">
      {/* Image */}
      <div className="relative aspect-square overflow-hidden bg-gray-50">
        <img
          src={product.image}
          alt={product.name}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
        />
        {product.badge && (
          <span className="absolute top-2 left-2 bg-red-500 text-white text-xs font-semibold px-2 py-1 rounded-full">
            {product.badge}
          </span>
        )}
        <button
          onClick={() => setIsWishlisted(w => !w)}
          className="absolute top-2 right-2 p-2 bg-white rounded-full shadow-sm opacity-0 group-hover:opacity-100 transition-opacity"
        >
          <HeartIcon className={`w-4 h-4 ${isWishlisted ? 'text-red-500 fill-red-500' : 'text-gray-400'}`} />
        </button>
      </div>

      {/* Content */}
      <div className="p-4">
        <p className="text-xs text-gray-400 uppercase tracking-wider mb-1">
          {product.category}
        </p>
        <h3 className="font-semibold text-gray-900 text-sm leading-tight mb-2 line-clamp-2">
          {product.name}
        </h3>

        {/* Rating */}
        <div className="flex items-center gap-1 mb-3">
          {[...Array(5)].map((_, i) => (
            <StarIcon
              key={i}
              className={`w-3 h-3 ${i < Math.floor(product.rating) ? 'text-yellow-400 fill-yellow-400' : 'text-gray-200 fill-gray-200'}`}
            />
          ))}
          <span className="text-xs text-gray-400 ml-1">({product.reviews})</span>
        </div>

        {/* Price + CTA */}
        <div className="flex items-center justify-between">
          <div>
            <span className="text-lg font-bold text-gray-900">
              ${product.price}
            </span>
            {product.originalPrice && (
              <span className="text-sm text-gray-400 line-through ml-2">
                ${product.originalPrice}
              </span>
            )}
          </div>
          <button className="bg-indigo-600 hover:bg-indigo-700 active:scale-95 text-white text-sm font-medium px-3 py-1.5 rounded-lg transition-all duration-150">
            Add to Cart
          </button>
        </div>
      </div>
    </div>
  );
}
```

## 6.6 Tailwind with clsx/cn

```tsx
// Install: npm install clsx class-variance-authority

// cn utility — merge tailwind classes cleanly (handles conflicts)
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// cva — class variance authority — for component variants
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  // Base styles (always applied)
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:opacity-50 disabled:cursor-not-allowed',
  {
    variants: {
      variant: {
        primary: 'bg-indigo-600 text-white hover:bg-indigo-700',
        secondary: 'bg-gray-100 text-gray-900 hover:bg-gray-200',
        destructive: 'bg-red-600 text-white hover:bg-red-700',
        outline: 'border border-gray-300 bg-white hover:bg-gray-50',
        ghost: 'hover:bg-gray-100 text-gray-700',
        link: 'text-indigo-600 underline-offset-4 hover:underline',
      },
      size: {
        sm: 'h-8 px-3 text-xs',
        md: 'h-10 px-4 text-sm',
        lg: 'h-12 px-6 text-base',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  isLoading?: boolean;
}

function Button({ variant, size, isLoading, className, children, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      disabled={isLoading || props.disabled}
      {...props}
    >
      {isLoading ? <Spinner className="w-4 h-4 mr-2" /> : null}
      {children}
    </button>
  );
}

// Usage
<Button variant="destructive" size="lg">Delete</Button>
<Button variant="outline" size="sm" isLoading>Saving...</Button>
<Button className="w-full">Custom class merged correctly</Button>
```

---

# 7. Containerization & Docker

## 7.1 Why Docker for Frontend Devs?

Docker solves "it works on my machine" problems by packaging your app with all its dependencies into a **container** that runs identically everywhere.

```
Without Docker:
  Dev machine: Node 20, works fine
  CI server: Node 18, build fails
  Production: Node 16, different behavior

With Docker:
  Everyone uses the exact same environment
  Dev = CI = Production = Identical
```

## 7.2 Core Docker Concepts

```
Image   = Blueprint (recipe)
Container = Running instance of an image (meal made from recipe)
Registry  = Where images are stored (Docker Hub, AWS ECR, etc.)

Dockerfile → docker build → Image → docker run → Container
```

## 7.3 Dockerfile for a Next.js App

```dockerfile
# Multi-stage build — production-optimized

# Stage 1: Install dependencies
FROM node:20-alpine AS deps
WORKDIR /app

# Copy package files first (leverage layer caching)
COPY package.json package-lock.json* ./
RUN npm ci --frozen-lockfile

# Stage 2: Build the app
FROM node:20-alpine AS builder
WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Build-time environment variables
ENV NEXT_TELEMETRY_DISABLED=1
ENV NODE_ENV=production

RUN npm run build

# Stage 3: Production image (much smaller!)
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

# Don't run as root — security best practice
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
USER nextjs

# Copy only what's needed for production
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

EXPOSE 3000

CMD ["node", "server.js"]
```

```dockerfile
# Dockerfile for a React (Vite) app — serve with nginx
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build  # outputs to /app/dist

# Production: serve with nginx
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```nginx
# nginx.conf for SPA (React Router needs this)
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;

  # Serve static assets with caching
  location ~* \.(js|css|png|jpg|svg|ico|woff2)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
  }

  # Always serve index.html for unmatched routes (SPA routing)
  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

## 7.4 Docker Compose — Local Development

```yaml
# docker-compose.yml — run everything together
version: '3.8'

services:
  # Frontend
  frontend:
    build:
      context: ./apps/frontend
      target: deps  # use deps stage for dev (hot reload)
    volumes:
      - ./apps/frontend:/app  # mount source code for hot reload
      - /app/node_modules      # don't override container's node_modules
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://localhost:4000
    depends_on:
      - api
    command: npm run dev

  # Backend API
  api:
    build:
      context: ./apps/api
    volumes:
      - ./apps/api:/app
      - /app/node_modules
    ports:
      - "4000:4000"
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
      - REDIS_URL=redis://redis:6379
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started

  # PostgreSQL
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  # Redis
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

```bash
# Start everything
docker compose up -d

# View logs
docker compose logs -f frontend

# Rebuild after Dockerfile changes
docker compose up --build

# Stop everything
docker compose down

# Destroy volumes (reset DB)
docker compose down -v
```

## 7.5 .dockerignore

```
node_modules
.next
dist
.git
.env
.env.local
*.log
README.md
.DS_Store
coverage
.nyc_output
```

---

# 8. Next.js

## 8.1 Why Next.js?

React is a UI library — it doesn't include routing, API routes, SSR, or build optimization. Next.js adds all of this and more.

| Feature | React (Vite) | Next.js |
|---------|-------------|---------|
| Routing | External (React Router) | Built-in (file-based) |
| SSR / SSG | Manual | Built-in |
| API Routes | Separate backend needed | Built-in |
| Image Optimization | Manual | `<Image />` component |
| SEO | Limited (CSR) | Excellent (SSR/SSG) |
| Code Splitting | Manual | Automatic |

---

## 8.2 Next.js App Router (v13+)

The App Router uses a file-system based routing in the `app/` directory.

```
app/
├── layout.tsx           → Root layout (wraps all pages)
├── page.tsx             → / (homepage)
├── loading.tsx          → Loading UI for this segment
├── error.tsx            → Error UI for this segment
├── not-found.tsx        → 404 for this segment
├── blog/
│   ├── page.tsx         → /blog
│   └── [slug]/
│       └── page.tsx     → /blog/any-slug (dynamic route)
├── dashboard/
│   ├── layout.tsx       → Nested layout (wraps dashboard pages)
│   ├── page.tsx         → /dashboard
│   ├── settings/
│   │   └── page.tsx     → /dashboard/settings
│   └── (auth)/          → Route group (doesn't affect URL)
│       ├── login/
│       │   └── page.tsx → /login
│       └── register/
│           └── page.tsx → /register
└── api/
    └── users/
        └── route.ts     → /api/users (API endpoint)
```

---

## 8.3 Server Components vs Client Components

**Server Components** (default in App Router):
- Run on the server
- Can access DB, filesystem, secrets directly
- Smaller bundle (no JS sent to client)
- Can't use hooks, event listeners, browser APIs

**Client Components** (`'use client'` directive):
- Run in the browser
- Can use hooks, state, events
- Must be declared with `'use client'`

```tsx
// app/users/page.tsx — Server Component (default)
// Runs on server — can access DB directly, no API call needed!
import { db } from '@/lib/db';

export default async function UsersPage() {
  // Direct DB access — no API layer needed!
  const users = await db.user.findMany({
    select: { id: true, name: true, email: true },
    orderBy: { createdAt: 'desc' }
  });

  return (
    <main>
      <h1>Users</h1>
      <UserList users={users} />  {/* Server component */}
      <AddUserButton />           {/* Client component — needs interactivity */}
    </main>
  );
}
```

```tsx
// components/AddUserButton.tsx — Client Component
'use client';

import { useState } from 'react';
import { createUser } from '@/app/actions'; // Server Action

export function AddUserButton() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Add User</button>
      {isOpen && <AddUserModal onClose={() => setIsOpen(false)} />}
    </>
  );
}
```

**Component tree strategy:**
```
page.tsx (Server)
  └── UserList (Server) — renders user data
  └── UserSearch (Client) — needs onChange event
  └── UserTable (Server)
       └── UserActions (Client) — needs onClick
```

---

## 8.4 Data Fetching in Next.js

```tsx
// 1. Static data — fetched at build time, cached forever
export default async function StaticPage() {
  const data = await fetch('https://api.example.com/products', {
    cache: 'force-cache' // default — static
  });
  const products = await data.json();
  return <ProductList products={products} />;
}

// 2. Dynamic data — fetched on every request (SSR)
export default async function DynamicPage() {
  const data = await fetch('https://api.example.com/prices', {
    cache: 'no-store' // always fresh
  });
  const prices = await data.json();
  return <PriceList prices={prices} />;
}

// 3. Revalidate — refetch in background every N seconds (ISR)
export default async function ISRPage() {
  const data = await fetch('https://api.example.com/news', {
    next: { revalidate: 60 } // re-fetch every 60 seconds
  });
  const news = await data.json();
  return <NewsFeed news={news} />;
}

// 4. Dynamic route pages
// app/blog/[slug]/page.tsx
export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await db.post.findUnique({ where: { slug: params.slug } });

  if (!post) notFound(); // shows not-found.tsx

  return <PostContent post={post} />;
}

// Pre-generate known slugs at build time
export async function generateStaticParams() {
  const posts = await db.post.findMany({ select: { slug: true } });
  return posts.map(p => ({ slug: p.slug }));
}
```

---

## 8.5 API Routes

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url);
  const page = parseInt(searchParams.get('page') ?? '1');
  const limit = parseInt(searchParams.get('limit') ?? '10');

  const [users, total] = await Promise.all([
    db.user.findMany({
      skip: (page - 1) * limit,
      take: limit,
      orderBy: { createdAt: 'desc' }
    }),
    db.user.count()
  ]);

  return NextResponse.json({
    users,
    pagination: { page, limit, total, totalPages: Math.ceil(total / limit) }
  });
}

export async function POST(request: NextRequest) {
  const body = await request.json();

  const result = createUserSchema.safeParse(body);
  if (!result.success) {
    return NextResponse.json({ error: result.error.flatten() }, { status: 422 });
  }

  const user = await db.user.create({ data: result.data });
  return NextResponse.json(user, { status: 201 });
}

// app/api/users/[id]/route.ts
export async function GET(request: NextRequest, { params }: { params: { id: string } }) {
  const user = await db.user.findUnique({ where: { id: params.id } });
  if (!user) return NextResponse.json({ error: 'Not found' }, { status: 404 });
  return NextResponse.json(user);
}

export async function PATCH(request: NextRequest, { params }: { params: { id: string } }) {
  const body = await request.json();
  const user = await db.user.update({ where: { id: params.id }, data: body });
  return NextResponse.json(user);
}

export async function DELETE(request: NextRequest, { params }: { params: { id: string } }) {
  await db.user.delete({ where: { id: params.id } });
  return new NextResponse(null, { status: 204 });
}
```

---

## 8.6 Server Actions

Server Actions let you call server-side functions directly from client components — no API route needed.

```tsx
// app/actions.ts
'use server';  // all functions in this file run on server

import { revalidatePath } from 'next/cache';

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string;
  const email = formData.get('email') as string;

  // Validate
  const result = createUserSchema.safeParse({ name, email });
  if (!result.success) {
    return { error: result.error.flatten() };
  }

  // Create user
  await db.user.create({ data: result.data });

  // Revalidate the users page cache
  revalidatePath('/users');

  return { success: true };
}

export async function deleteUser(userId: string) {
  await db.user.delete({ where: { id: userId } });
  revalidatePath('/users');
}
```

```tsx
// components/UserForm.tsx (Client Component)
'use client';

import { createUser } from '@/app/actions';
import { useFormState } from 'react-dom';

export function UserForm() {
  const [state, formAction] = useFormState(createUser, null);

  return (
    <form action={formAction}>
      <input name="name" placeholder="Name" required />
      <input name="email" type="email" placeholder="Email" required />
      {state?.error && <p className="text-red-500">{state.error.formErrors[0]}</p>}
      <button type="submit">Create User</button>
    </form>
  );
}
```

---

## 8.7 Middleware

```typescript
// middleware.ts (at project root)
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;

  // Auth check for protected routes
  const token = request.cookies.get('token')?.value;

  if (pathname.startsWith('/dashboard') && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  // Add security headers
  const response = NextResponse.next();
  response.headers.set('X-Frame-Options', 'DENY');
  response.headers.set('X-Content-Type-Options', 'nosniff');

  // CORS for API routes
  if (pathname.startsWith('/api/')) {
    response.headers.set('Access-Control-Allow-Origin', 'https://myapp.com');
  }

  return response;
}

// Only run middleware on these paths
export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*', '/settings/:path*']
};
```

---

## 8.8 Next.js Image Optimization

```tsx
import Image from 'next/image';

// ✅ Always use Next.js Image — automatic WebP conversion, lazy loading, size optimization
<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority             // load eagerly (above fold)
  className="rounded-xl"
/>

// Remote images — must configure domain in next.config.js
<Image
  src="https://images.unsplash.com/..."
  alt="User avatar"
  width={64}
  height={64}
  className="rounded-full"
/>

// Fill parent container
<div className="relative w-full aspect-video">
  <Image
    src={product.image}
    alt={product.name}
    fill
    className="object-cover rounded-lg"
  />
</div>
```

```js
// next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'images.unsplash.com',
      },
      {
        protocol: 'https',
        hostname: '*.cloudinary.com',
      }
    ]
  }
};
```

---

# 9. Custom Hooks

## 9.1 What are Custom Hooks?

Custom hooks are functions that:
- Start with `use`
- Can call other hooks
- Extract and reuse stateful logic between components

```jsx
// Without custom hook — logic duplicated in every component
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);

  // This exact logic is copied in 10 other components... 😤
}

// With custom hook — DRY, reusable, testable
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);

    fetch(`/api/users/${userId}`)
      .then(r => {
        if (!r.ok) throw new Error(`HTTP ${r.status}`);
        return r.json();
      })
      .then(data => { if (!cancelled) setUser(data); })
      .catch(err => { if (!cancelled) setError(err.message); })
      .finally(() => { if (!cancelled) setLoading(false); });

    return () => { cancelled = true; };
  }, [userId]);

  return { user, loading, error };
}

// Use in any component — no duplication!
function UserProfile({ userId }) {
  const { user, loading, error } = useUser(userId);
  if (loading) return <Spinner />;
  if (error) return <Error message={error} />;
  return <UserCard user={user} />;
}
```

---

## 9.2 Essential Custom Hooks

### useFetch — Generic data fetching

```typescript
function useFetch<T>(url: string | null, options?: RequestInit) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!url) return;

    let cancelled = false;
    const controller = new AbortController();

    setLoading(true);
    setError(null);

    fetch(url, { ...options, signal: controller.signal })
      .then(async res => {
        if (!res.ok) {
          const err = await res.json().catch(() => ({}));
          throw new Error(err.message || `HTTP error ${res.status}`);
        }
        return res.json() as Promise<T>;
      })
      .then(data => { if (!cancelled) setData(data); })
      .catch(err => {
        if (!cancelled && err.name !== 'AbortError') {
          setError(err.message);
        }
      })
      .finally(() => { if (!cancelled) setLoading(false); });

    return () => {
      cancelled = true;
      controller.abort();
    };
  }, [url]);

  return { data, loading, error };
}

// Usage
function Products() {
  const { data: products, loading, error } = useFetch<Product[]>('/api/products');
  if (loading) return <Skeleton />;
  if (error) return <p>Error: {error}</p>;
  return <ProductGrid products={products!} />;
}
```

### useLocalStorage — Persistent state

```typescript
function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = useCallback((value: T | ((prev: T) => T)) => {
    try {
      const newValue = value instanceof Function ? value(storedValue) : value;
      setStoredValue(newValue);
      localStorage.setItem(key, JSON.stringify(newValue));
    } catch (err) {
      console.error(`useLocalStorage error for key "${key}":`, err);
    }
  }, [key, storedValue]);

  const removeValue = useCallback(() => {
    localStorage.removeItem(key);
    setStoredValue(initialValue);
  }, [key, initialValue]);

  return [storedValue, setValue, removeValue] as const;
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [language, setLanguage] = useLocalStorage('language', 'en');
  return <SettingsForm theme={theme} language={language} onThemeChange={setTheme} />;
}
```

### useDebounce — Delay updates

```typescript
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage — search input that doesn't fire API on every keystroke
function SearchBar() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 400); // wait 400ms after typing stops

  const { data: results } = useFetch(
    debouncedQuery ? `/api/search?q=${debouncedQuery}` : null
  );

  return (
    <>
      <input
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="Search..."
      />
      <SearchResults results={results} />
    </>
  );
}
```

### useIntersectionObserver — Infinite scroll, lazy loading

```typescript
function useIntersectionObserver(
  ref: React.RefObject<Element>,
  options: IntersectionObserverInit = {}
) {
  const [isIntersecting, setIsIntersecting] = useState(false);

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    const observer = new IntersectionObserver(
      ([entry]) => setIsIntersecting(entry.isIntersecting),
      { threshold: 0.1, ...options }
    );

    observer.observe(element);
    return () => observer.disconnect();
  }, [ref, options.threshold]);

  return isIntersecting;
}

// Usage — infinite scroll
function InfiniteProductList() {
  const [products, setProducts] = useState<Product[]>([]);
  const [page, setPage] = useState(1);
  const [hasMore, setHasMore] = useState(true);
  const loaderRef = useRef<HTMLDivElement>(null);
  const isVisible = useIntersectionObserver(loaderRef);

  useEffect(() => {
    if (isVisible && hasMore) {
      fetchProducts(page).then(newProducts => {
        setProducts(prev => [...prev, ...newProducts]);
        setPage(p => p + 1);
        if (newProducts.length < 10) setHasMore(false);
      });
    }
  }, [isVisible, hasMore]);

  return (
    <div>
      {products.map(p => <ProductCard key={p.id} product={p} />)}
      {hasMore && <div ref={loaderRef}><Spinner /></div>}
    </div>
  );
}
```

### useMediaQuery — Responsive behavior in JS

```typescript
function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(
    () => window.matchMedia(query).matches
  );

  useEffect(() => {
    const mql = window.matchMedia(query);
    const handler = (e: MediaQueryListEvent) => setMatches(e.matches);
    mql.addEventListener('change', handler);
    return () => mql.removeEventListener('change', handler);
  }, [query]);

  return matches;
}

// Predefined hooks
const useIsMobile = () => useMediaQuery('(max-width: 767px)');
const useIsTablet = () => useMediaQuery('(min-width: 768px) and (max-width: 1023px)');
const useIsDarkMode = () => useMediaQuery('(prefers-color-scheme: dark)');

// Usage
function Navbar() {
  const isMobile = useIsMobile();
  return isMobile ? <MobileNav /> : <DesktopNav />;
}
```

### useClickOutside — Close dropdowns, modals

```typescript
function useClickOutside(ref: React.RefObject<Element>, handler: () => void) {
  useEffect(() => {
    function handleClick(e: MouseEvent) {
      if (ref.current && !ref.current.contains(e.target as Node)) {
        handler();
      }
    }

    document.addEventListener('mousedown', handleClick);
    return () => document.removeEventListener('mousedown', handleClick);
  }, [ref, handler]);
}

// Usage
function Dropdown({ options }: { options: string[] }) {
  const [isOpen, setIsOpen] = useState(false);
  const dropdownRef = useRef<HTMLDivElement>(null);

  useClickOutside(dropdownRef, () => setIsOpen(false));

  return (
    <div ref={dropdownRef} className="relative">
      <button onClick={() => setIsOpen(o => !o)}>Options</button>
      {isOpen && (
        <ul className="absolute top-full mt-1 bg-white border rounded-lg shadow-lg">
          {options.map(opt => <li key={opt}>{opt}</li>)}
        </ul>
      )}
    </div>
  );
}
```

### useForm — Form state management

```typescript
function useForm<T extends Record<string, unknown>>(initialValues: T) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const handleChange = useCallback((field: keyof T, value: unknown) => {
    setValues(prev => ({ ...prev, [field]: value }));
    // Clear error when user starts typing
    if (errors[field]) {
      setErrors(prev => { const e = { ...prev }; delete e[field]; return e; });
    }
  }, [errors]);

  const handleBlur = useCallback((field: keyof T) => {
    setTouched(prev => ({ ...prev, [field]: true }));
  }, []);

  const reset = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
    setIsSubmitting(false);
  }, [initialValues]);

  const handleSubmit = useCallback((onSubmit: (values: T) => Promise<void>, validate?: (values: T) => Partial<Record<keyof T, string>>) => {
    return async (e: React.FormEvent) => {
      e.preventDefault();

      if (validate) {
        const validationErrors = validate(values);
        if (Object.keys(validationErrors).length > 0) {
          setErrors(validationErrors);
          // Mark all as touched to show errors
          const allTouched = Object.keys(values).reduce((acc, key) => ({ ...acc, [key]: true }), {});
          setTouched(allTouched as typeof touched);
          return;
        }
      }

      setIsSubmitting(true);
      try {
        await onSubmit(values);
      } finally {
        setIsSubmitting(false);
      }
    };
  }, [values]);

  return { values, errors, touched, isSubmitting, handleChange, handleBlur, handleSubmit, reset };
}

// Usage
function RegisterForm() {
  const form = useForm({ name: '', email: '', password: '' });

  const validate = (values: typeof form.values) => {
    const errors: Record<string, string> = {};
    if (!values.name) errors.name = 'Name is required';
    if (!values.email.includes('@')) errors.email = 'Valid email required';
    if (values.password.length < 8) errors.password = 'Minimum 8 characters';
    return errors;
  };

  const onSubmit = form.handleSubmit(async (values) => {
    await api.post('/auth/register', values);
    router.push('/dashboard');
  }, validate);

  return (
    <form onSubmit={onSubmit}>
      <input
        value={form.values.name}
        onChange={e => form.handleChange('name', e.target.value)}
        onBlur={() => form.handleBlur('name')}
      />
      {form.touched.name && form.errors.name && (
        <p className="text-red-500 text-sm">{form.errors.name}</p>
      )}
      <button type="submit" disabled={form.isSubmitting}>
        {form.isSubmitting ? 'Creating account...' : 'Register'}
      </button>
    </form>
  );
}
```

---

# 10. In-House Auth using NextAuth

## 10.1 What is NextAuth?

NextAuth (now **Auth.js**) handles authentication in Next.js apps. It supports:
- OAuth providers (Google, GitHub, Discord, etc.)
- Email/password (credentials)
- Magic links
- Session management (JWT or database)

## 10.2 Installation & Setup

```bash
npm install next-auth
```

```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth';
import { authOptions } from '@/lib/auth';

const handler = NextAuth(authOptions);
export { handler as GET, handler as POST };
```

```typescript
// lib/auth.ts — central auth configuration
import { NextAuthOptions } from 'next-auth';
import GoogleProvider from 'next-auth/providers/google';
import GithubProvider from 'next-auth/providers/github';
import CredentialsProvider from 'next-auth/providers/credentials';
import { PrismaAdapter } from '@auth/prisma-adapter';
import { prisma } from '@/lib/prisma';
import bcrypt from 'bcryptjs';

export const authOptions: NextAuthOptions = {
  // Store sessions in database
  adapter: PrismaAdapter(prisma),

  providers: [
    // Google OAuth
    GoogleProvider({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    }),

    // GitHub OAuth
    GithubProvider({
      clientId: process.env.GITHUB_ID!,
      clientSecret: process.env.GITHUB_SECRET!,
    }),

    // Email + Password
    CredentialsProvider({
      name: 'credentials',
      credentials: {
        email: { label: 'Email', type: 'email' },
        password: { label: 'Password', type: 'password' }
      },
      async authorize(credentials) {
        if (!credentials?.email || !credentials?.password) return null;

        const user = await prisma.user.findUnique({
          where: { email: credentials.email }
        });

        if (!user || !user.password) return null;

        const isValid = await bcrypt.compare(credentials.password, user.password);
        if (!isValid) return null;

        return {
          id: user.id,
          name: user.name,
          email: user.email,
          role: user.role,
          image: user.image
        };
      }
    })
  ],

  // Use JWT for session (faster, no DB lookup per request)
  session: {
    strategy: 'jwt',
    maxAge: 30 * 24 * 60 * 60, // 30 days
  },

  pages: {
    signIn: '/login',          // custom login page
    signOut: '/logout',
    error: '/auth/error',      // error page
    newUser: '/onboarding',    // new user redirect
  },

  callbacks: {
    // Add custom data to JWT
    async jwt({ token, user, account }) {
      if (user) {
        token.id = user.id;
        token.role = user.role;
      }
      return token;
    },

    // Add JWT data to session (accessible in components)
    async session({ session, token }) {
      session.user.id = token.id as string;
      session.user.role = token.role as string;
      return session;
    },

    // Control redirects after sign in
    async redirect({ url, baseUrl }) {
      if (url.startsWith('/')) return `${baseUrl}${url}`;
      if (new URL(url).origin === baseUrl) return url;
      return baseUrl;
    }
  },

  events: {
    async signIn({ user, isNewUser }) {
      if (isNewUser) {
        await sendWelcomeEmail(user.email!);
        await analytics.track('user_registered', { userId: user.id });
      }
    },
    async signOut({ token }) {
      await analytics.track('user_signed_out', { userId: token.id });
    }
  },

  // Enable debug in development
  debug: process.env.NODE_ENV === 'development',
};
```

## 10.3 Prisma Schema for NextAuth

```prisma
// prisma/schema.prisma — NextAuth required models
model Account {
  id                String  @id @default(cuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String? @db.Text
  access_token      String? @db.Text
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String? @db.Text
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
}

model Session {
  id           String   @id @default(cuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)
}

model User {
  id            String    @id @default(cuid())
  name          String?
  email         String?   @unique
  emailVerified DateTime?
  image         String?
  password      String?   // for credentials provider
  role          String    @default("user")
  createdAt     DateTime  @default(now())

  accounts Account[]
  sessions Session[]
}

model VerificationToken {
  identifier String
  token      String   @unique
  expires    DateTime

  @@unique([identifier, token])
}
```

## 10.4 Using Auth in Components

```typescript
// Type augmentation for custom session fields
// types/next-auth.d.ts
import 'next-auth';

declare module 'next-auth' {
  interface Session {
    user: {
      id: string;
      name: string;
      email: string;
      image?: string;
      role: string;
    }
  }
}
```

```tsx
// Server Component — get session on server
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';

export default async function DashboardPage() {
  const session = await getServerSession(authOptions);

  if (!session) {
    redirect('/login');
  }

  // session.user.id, session.user.role available here
  const userData = await db.user.findUnique({ where: { id: session.user.id } });

  return <Dashboard user={userData} />;
}
```

```tsx
// Client Component — get session on client
'use client';
import { useSession, signIn, signOut } from 'next-auth/react';

export function UserMenu() {
  const { data: session, status } = useSession();

  if (status === 'loading') return <Spinner />;
  if (!session) {
    return <button onClick={() => signIn()}>Sign In</button>;
  }

  return (
    <div className="flex items-center gap-3">
      <img src={session.user.image!} className="w-8 h-8 rounded-full" />
      <span>{session.user.name}</span>
      {session.user.role === 'admin' && <AdminBadge />}
      <button onClick={() => signOut({ callbackUrl: '/' })}>Sign Out</button>
    </div>
  );
}
```

## 10.5 Custom Login Page

```tsx
// app/login/page.tsx
'use client';
import { signIn } from 'next-auth/react';
import { useState } from 'react';
import { useRouter, useSearchParams } from 'next/navigation';

export default function LoginPage() {
  const router = useRouter();
  const searchParams = useSearchParams();
  const callbackUrl = searchParams.get('callbackUrl') || '/dashboard';
  const [error, setError] = useState('');
  const [isLoading, setIsLoading] = useState(false);

  async function handleCredentials(e: React.FormEvent<HTMLFormElement>) {
    e.preventDefault();
    setIsLoading(true);
    setError('');

    const formData = new FormData(e.currentTarget);

    const result = await signIn('credentials', {
      email: formData.get('email'),
      password: formData.get('password'),
      redirect: false, // handle redirect manually
    });

    if (result?.error) {
      setError('Invalid email or password');
      setIsLoading(false);
    } else {
      router.push(callbackUrl);
    }
  }

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="w-full max-w-md bg-white rounded-2xl shadow-sm border border-gray-100 p-8">
        <h1 className="text-2xl font-bold text-gray-900 mb-2">Welcome back</h1>
        <p className="text-gray-500 mb-8">Sign in to your account</p>

        {/* OAuth buttons */}
        <div className="space-y-3 mb-8">
          <button
            onClick={() => signIn('google', { callbackUrl })}
            className="w-full flex items-center justify-center gap-3 px-4 py-2.5 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
          >
            <GoogleIcon className="w-5 h-5" />
            Continue with Google
          </button>
          <button
            onClick={() => signIn('github', { callbackUrl })}
            className="w-full flex items-center justify-center gap-3 px-4 py-2.5 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
          >
            <GithubIcon className="w-5 h-5" />
            Continue with GitHub
          </button>
        </div>

        <div className="relative mb-6">
          <div className="absolute inset-0 flex items-center">
            <div className="w-full border-t border-gray-200" />
          </div>
          <div className="relative flex justify-center text-sm">
            <span className="bg-white px-4 text-gray-400">or</span>
          </div>
        </div>

        {/* Credentials form */}
        <form onSubmit={handleCredentials} className="space-y-4">
          {error && (
            <div className="bg-red-50 border border-red-200 text-red-700 text-sm px-4 py-3 rounded-lg">
              {error}
            </div>
          )}
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1.5">
              Email address
            </label>
            <input
              name="email"
              type="email"
              required
              autoComplete="email"
              className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-1.5">
              Password
            </label>
            <input
              name="password"
              type="password"
              required
              autoComplete="current-password"
              className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-indigo-500 focus:ring-2 focus:ring-indigo-100"
            />
          </div>
          <button
            type="submit"
            disabled={isLoading}
            className="w-full bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-400 text-white font-medium py-2.5 rounded-lg transition-colors"
          >
            {isLoading ? 'Signing in...' : 'Sign in'}
          </button>
        </form>

        <p className="text-center text-sm text-gray-500 mt-6">
          Don't have an account?{' '}
          <a href="/register" className="text-indigo-600 hover:underline font-medium">
            Sign up for free
          </a>
        </p>
      </div>
    </div>
  );
}
```

## 10.6 Protected Routes & Role-Based Access

```tsx
// Higher-order component for client-side route protection
'use client';

function withAuth(Component: React.ComponentType, requiredRole?: string) {
  return function AuthenticatedComponent(props: any) {
    const { data: session, status } = useSession();
    const router = useRouter();

    useEffect(() => {
      if (status === 'loading') return;
      if (!session) router.push('/login');
      if (requiredRole && session?.user.role !== requiredRole) {
        router.push('/unauthorized');
      }
    }, [session, status]);

    if (status === 'loading') return <PageLoader />;
    if (!session) return null;

    return <Component {...props} />;
  };
}

// Usage
const AdminDashboard = withAuth(AdminDashboardPage, 'admin');
const UserDashboard = withAuth(UserDashboardPage);

// Server-side (more secure — checks before rendering)
async function requireAuth(requiredRole?: string) {
  const session = await getServerSession(authOptions);
  if (!session) redirect('/login');
  if (requiredRole && session.user.role !== requiredRole) redirect('/unauthorized');
  return session;
}

// In a Server Component
export default async function AdminPage() {
  const session = await requireAuth('admin');
  return <AdminContent user={session.user} />;
}
```

---

# Quick Reference Cheat Sheets

## React Hooks at a Glance

| Hook | Purpose | Re-renders? |
|------|---------|------------|
| `useState` | Local component state | Yes |
| `useEffect` | Side effects (fetch, subscriptions) | No |
| `useRef` | DOM refs, mutable values | No |
| `useMemo` | Memoize expensive values | No |
| `useCallback` | Memoize functions | No |
| `useContext` | Consume context | Yes (when context changes) |
| `useReducer` | Complex state logic | Yes |
| `useTransition` | Non-urgent state updates | No |

## When to Use What

| Need | Solution |
|------|---------|
| Simple local state | `useState` |
| Complex state with actions | `useReducer` |
| Share state a few levels down | `useContext` |
| Global state, frequent updates | Recoil / Zustand |
| Server state (cache, sync) | React Query / SWR |
| Form state | React Hook Form / custom `useForm` |
| URL state | Search params (Next.js `useSearchParams`) |

## Next.js Rendering Strategies

| Strategy | When | Cache |
|---------|------|-------|
| Static (SSG) | Data doesn't change often | Build time |
| ISR | Data changes occasionally | Revalidate every N seconds |
| Dynamic (SSR) | Data changes on every request | Never |
| Client-side | Data specific to user session | Browser |

---
