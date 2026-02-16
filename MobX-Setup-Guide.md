# MobX Counter Setup Guide - Complete Reference

This guide explains how to set up a simple counter app using MobX in your project, following the same pattern as your SmartFormStore.

---

## Table of Contents
1. [What is MobX?](#what-is-mobx)
2. [Installation](#installation)
3. [Core Concepts](#core-concepts)
4. [Step-by-Step Implementation](#step-by-step-implementation)
5. [Complete Code Examples](#complete-code-examples)
6. [How Data Flows](#how-data-flows)
7. [Common Patterns](#common-patterns)

---

## What is MobX?

MobX is a state management library that makes your app's state **reactive**. When you change a value, all components using that value automatically update.

**Think of it like Excel spreadsheets:**
- Cell A1 has value: `5`
- Cell B1 has formula: `=A1 * 2`
- When you change A1 to `10`, B1 automatically becomes `20`

MobX works the same way with your app's data!

---

## Installation

### Required Packages

```bash
npm install mobx mobx-react
```

**What each package does:**
- `mobx` - The core state management library
- `mobx-react` - Connects MobX to React components

---

## Core Concepts

### 1. **@observable** - State That Can Be Watched

```typescript
import { observable } from "mobx";

class CounterStore {
  // This value can be watched for changes
  @observable counter: number = 0;
  
  // Any component using this value will re-render when it changes
}
```

**Think of it as:** A variable that announces "Hey, I changed!" whenever you update it.

---

### 2. **@action** - Methods That Change State

```typescript
import { action, observable } from "mobx";

class CounterStore {
  @observable counter: number = 0;
  
  // Actions are the ONLY way you should change observables
  @action
  increment = () => {
    this.counter = this.counter + 1;  // Modify state here
  };
  
  @action
  setCounter = (value: number) => {
    this.counter = value;  // Setter action
  };
}
```

**Think of it as:** Official channels to update your data. Like using a bank teller instead of directly accessing the vault.

**Why use actions?**
- Makes your code predictable
- Easy to debug (you know exactly where changes happen)
- Follows best practices

---

### 3. **@computed** - Derived Values

```typescript
import { action, observable, computed } from "mobx";

class CounterStore {
  @observable counter: number = 5;
  
  // This value is calculated from counter
  // It automatically updates when counter changes
  @computed
  get doubleValue(): number {
    return this.counter * 2;  // If counter = 5, this returns 10
  }
  
  @computed
  get isEven(): boolean {
    return this.counter % 2 === 0;  // Returns true if counter is even
  }
}
```

**Think of it as:** Excel formulas that auto-recalculate when source cells change.

**Benefits:**
- No need to manually update them
- Only recalculates when dependencies change (efficient!)
- Always in sync with source data

---

### 4. **observer** - Make Components Reactive

```typescript
import { observer } from 'mobx-react';

// Without observer - component won't update when store changes ❌
const Counter = ({ counterStore }) => {
  return <div>{counterStore.counter}</div>;
};

// With observer - component updates automatically ✅
const Counter = observer(({ counterStore }) => {
  return <div>{counterStore.counter}</div>;
});
```

**Think of it as:** Subscribing to notifications. The component gets alerted whenever observable values it uses change.

---

### 5. **inject** - Connect Store to Component

```typescript
import { inject, observer } from 'mobx-react';

// Inject makes the store available as a prop
const Counter = observer(({ counterStore }) => {
  // counterStore is provided by inject
  return <div>{counterStore.counter}</div>;
});

// 'counterStore' must match the key in your stores.ts file
export default inject('counterStore')(Counter);
```

**Think of it as:** Plugging in a USB cable. It connects your component to the global store.

---

### 6. **Provider** - Make Stores Available Globally

```typescript
import { Provider } from 'mobx-react';
import store from './stores';

// Wrap your entire app once
<Provider {...store}>
  <App />
</Provider>

// Now all components can inject any store
```

**Think of it as:** A power outlet that makes electricity (stores) available everywhere in your house (app).

---

## Step-by-Step Implementation

### Step 1: Create the Counter Store

**File:** `src/app/src/stores/CounterStore/CounterStore.ts`

```typescript
import { action, observable, computed } from "mobx";

/**
 * CounterStore - Manages counter state and operations
 * 
 * This is a "slice" of your app's state, similar to how
 * SmartFormStore manages form-related state.
 */
class CounterStore {
  
  // ============================================
  // STATE (Observable Properties)
  // ============================================
  
  /**
   * @observable makes this value "reactive"
   * Any component using this will re-render when it changes
   */
  @observable counter: number = 0;
  
  /**
   * You can have multiple observable properties
   */
  @observable counterName: string = "Main Counter";
  
  @observable isActive: boolean = true;

  
  // ============================================
  // ACTIONS (Methods That Modify State)
  // ============================================
  
  /**
   * Increment counter by 1
   * @action tells MobX this method modifies state
   */
  @action
  increment = () => {
    this.counter = this.counter + 1;
    console.log('Counter incremented to:', this.counter);
  };

  /**
   * Decrement counter by 1
   */
  @action
  decrement = () => {
    this.counter = this.counter - 1;
    console.log('Counter decremented to:', this.counter);
  };

  /**
   * Reset counter to 0
   */
  @action
  reset = () => {
    this.counter = 0;
    console.log('Counter reset to 0');
  };

  /**
   * Set counter to a specific value (Setter action)
   * @param value - The new counter value
   */
  @action
  setCounter = (value: number) => {
    this.counter = value;
    console.log('Counter set to:', this.counter);
  };

  /**
   * Increment by a custom amount
   * @param amount - How much to add
   */
  @action
  incrementBy = (amount: number) => {
    this.counter = this.counter + amount;
    console.log(`Counter incremented by ${amount}, new value:`, this.counter);
  };

  /**
   * Update counter name
   * @param name - New name for the counter
   */
  @action
  setCounterName = (name: string) => {
    this.counterName = name;
  };

  /**
   * Toggle active state
   */
  @action
  toggleActive = () => {
    this.isActive = !this.isActive;
  };

  
  // ============================================
  // COMPUTED VALUES (Derived State)
  // ============================================
  
  /**
   * Returns true if counter is positive
   * This automatically updates when counter changes
   */
  @computed
  get isPositive(): boolean {
    return this.counter > 0;
  }

  /**
   * Returns true if counter is negative
   */
  @computed
  get isNegative(): boolean {
    return this.counter < 0;
  }

  /**
   * Returns true if counter is zero
   */
  @computed
  get isZero(): boolean {
    return this.counter === 0;
  }

  /**
   * Returns double the counter value
   * Only recalculates when counter changes (efficient!)
   */
  @computed
  get doubleValue(): number {
    console.log('Calculating double value...');
    return this.counter * 2;
  }

  /**
   * Returns counter squared
   */
  @computed
  get squaredValue(): number {
    return this.counter * this.counter;
  }

  /**
   * Returns true if counter is even
   */
  @computed
  get isEven(): boolean {
    return this.counter % 2 === 0;
  }

  /**
   * Returns a status message based on counter value
   */
  @computed
  get statusMessage(): string {
    if (this.counter > 10) return 'High value!';
    if (this.counter < -10) return 'Very negative!';
    if (this.counter === 0) return 'At zero';
    return 'Normal range';
  }

  /**
   * Example: Computed value using multiple observables
   */
  @computed
  get displayText(): string {
    return `${this.counterName}: ${this.counter}`;
  }
}

export default CounterStore;
```

---

### Step 2: Register Store in stores.ts

**File:** `src/app/src/stores.ts`

```typescript
import CounterStore from './stores/CounterStore/CounterStore';

/*
 * This is where all stores are created and made available to the app.
 * 
 * IMPORTANT CONCEPTS:
 * 
 * 1. Single Instance: Each store is created ONCE here
 *    - new CounterStore() creates ONE instance
 *    - All components share this same instance
 *    - This ensures consistent state across your app
 * 
 * 2. Keys Matter: The key names here ('counterStore') are used
 *    when injecting stores into components
 *    - inject('counterStore') must match the key here
 * 
 * 3. Dependencies: If one store needs another, pass it as a parameter
 *    - Example: new StoreB(storeA) if StoreB needs StoreA
 */

// Create the counter store instance
const counterStoreInstance = new CounterStore();

// Export the store object
// This object is spread into Provider in index.tsx
const store = {
    counterStore: counterStoreInstance,  // Key name used in inject()
    
    // If you had multiple stores:
    // userStore: new UserStore(),
    // productStore: new ProductStore(),
    // cartStore: new CartStore(productStore), // Pass dependency
};

export default store;
```

---

### Step 3: Provider Setup (Already Done in Your Project)

**File:** `src/app/src/index.tsx`

This is already set up in your project, but here's how it works:

```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'mobx-react';  // MobX Provider
import store from './stores';           // Import all stores
import App from './App';

/*
 * Provider makes all stores available to your entire app
 * 
 * How it works:
 * 1. {...store} spreads all store properties
 * 2. Makes them available for injection in any component
 * 3. Only needs to be done ONCE at the root level
 */

ReactDOM.render(
    <Provider {...store}>
        <App />
    </Provider>,
    document.getElementById('root')
);
```

**What's happening:**
- `{...store}` spreads to: `counterStore={counterStoreInstance}`
- Now any component can do: `inject('counterStore')`

---

### Step 4: Create Counter Component

**File:** `src/app/src/components/Counter/Counter.tsx`

```typescript
import React from 'react';
import { inject, observer } from 'mobx-react';
import CounterStore from '../../stores/CounterStore/CounterStore';

/**
 * Props Interface
 * counterStore is optional (?) because inject() provides it
 */
interface CounterProps {
  counterStore?: CounterStore;  // Optional because inject adds it
}

/**
 * Counter Component
 * 
 * KEY CONCEPTS:
 * 
 * 1. observer() - Makes component reactive
 *    - Component re-renders when any observable it uses changes
 *    - Wraps the component function
 * 
 * 2. counterStore comes from inject()
 *    - Provided as a prop automatically
 *    - Use ! (non-null assertion) or optional chaining
 */
const Counter = observer(({ counterStore }: CounterProps) => {
  
  // ============================================
  // ACCESSING STATE (Reading Values)
  // ============================================
  
  /**
   * Get current counter value
   * This value is ALWAYS fresh - MobX ensures it
   */
  const currentValue = counterStore!.counter;
  
  /**
   * Access other observable values
   */
  const counterName = counterStore!.counterName;
  const isActive = counterStore!.isActive;
  
  /**
   * Access computed values (they auto-update!)
   */
  const doubled = counterStore!.doubleValue;
  const squared = counterStore!.squaredValue;
  const isEven = counterStore!.isEven;
  const status = counterStore!.statusMessage;

  
  // ============================================
  // CALLING ACTIONS (Updating State)
  // ============================================
  
  /**
   * Handler: Increment counter
   * Calls the action method from the store
   */
  const handleIncrement = () => {
    counterStore!.increment();
    // Counter value updates automatically
    // Component re-renders automatically
  };

  /**
   * Handler: Decrement counter
   */
  const handleDecrement = () => {
    counterStore!.decrement();
  };

  /**
   * Handler: Reset to zero
   */
  const handleReset = () => {
    counterStore!.reset();
  };

  /**
   * Handler: Set to a specific value
   * This demonstrates using a setter action
   */
  const handleSetToTen = () => {
    counterStore!.setCounter(10);
  };

  /**
   * Handler: Increment by custom amount
   */
  const handleIncrementBy5 = () => {
    counterStore!.incrementBy(5);
  };

  /**
   * Handler: Update counter name from input
   */
  const handleNameChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    counterStore!.setCounterName(event.target.value);
  };

  /**
   * Handler: Toggle active state
   */
  const handleToggleActive = () => {
    counterStore!.toggleActive();
  };

  
  // ============================================
  // RENDER UI
  // ============================================
  
  return (
    <div style={{ padding: '2rem', fontFamily: 'Arial' }}>
      <h2>Counter App with MobX</h2>
      
      {/* Display Counter Name */}
      <div style={{ marginBottom: '1rem' }}>
        <input 
          type="text" 
          value={counterName}
          onChange={handleNameChange}
          placeholder="Counter name"
          style={{ padding: '0.5rem', fontSize: '1rem' }}
        />
      </div>

      {/* Main Counter Display */}
      <div style={{ 
        background: '#f0f0f0', 
        padding: '2rem', 
        borderRadius: '10px',
        marginBottom: '1rem',
        opacity: isActive ? 1 : 0.5
      }}>
        <h1 style={{ fontSize: '4rem', margin: 0, color: '#007bff' }}>
          {currentValue}
        </h1>
        <p style={{ fontSize: '1.2rem', color: '#666' }}>
          {counterName}
        </p>
      </div>

      {/* Computed Values Display */}
      <div style={{ marginBottom: '1rem', padding: '1rem', background: '#fff3cd' }}>
        <h3>Computed Values (Auto-Update):</h3>
        <p>Double: {doubled}</p>
        <p>Squared: {squared}</p>
        <p>Is Even: {isEven ? 'Yes' : 'No'}</p>
        <p>Status: {status}</p>
        <p>Positive: {counterStore!.isPositive ? 'Yes' : 'No'}</p>
        <p>Negative: {counterStore!.isNegative ? 'Yes' : 'No'}</p>
      </div>

      {/* Action Buttons */}
      <div style={{ display: 'flex', gap: '0.5rem', flexWrap: 'wrap' }}>
        <button onClick={handleIncrement} style={buttonStyle}>
          Increment (+1)
        </button>
        
        <button onClick={handleDecrement} style={buttonStyle}>
          Decrement (-1)
        </button>
        
        <button onClick={handleIncrementBy5} style={buttonStyle}>
          Add 5
        </button>
        
        <button onClick={handleReset} style={buttonStyle}>
          Reset
        </button>
        
        <button onClick={handleSetToTen} style={buttonStyle}>
          Set to 10
        </button>
        
        <button onClick={handleToggleActive} style={buttonStyle}>
          {isActive ? 'Deactivate' : 'Activate'}
        </button>
      </div>

      {/* Conditional Rendering Based on State */}
      {currentValue > 20 && (
        <div style={{ marginTop: '1rem', padding: '1rem', background: '#f8d7da', color: '#721c24' }}>
          ⚠️ Counter is very high!
        </div>
      )}
    </div>
  );
});

// Simple button style (inline for this example)
const buttonStyle: React.CSSProperties = {
  padding: '0.75rem 1.5rem',
  fontSize: '1rem',
  border: 'none',
  borderRadius: '5px',
  background: '#007bff',
  color: 'white',
  cursor: 'pointer',
};

/**
 * CRITICAL: inject and export
 * 
 * inject('counterStore') does 3 things:
 * 1. Looks for 'counterStore' in Provider
 * 2. Passes it as a prop to this component
 * 3. Returns a new component with the store injected
 * 
 * The string 'counterStore' MUST match the key in stores.ts
 */
export default inject('counterStore')(Counter);
```

---

### Step 5: Use Counter in Your App

**File:** `src/app/src/App.tsx` (or any other component)

```typescript
import React from 'react';
import Counter from './components/Counter/Counter';

function App() {
  return (
    <div>
      <h1>My App</h1>
      {/* Just use the component - store is already injected! */}
      <Counter />
    </div>
  );
}

export default App;
```

---

## How Data Flows

### The Complete Flow:

```
1. USER CLICKS BUTTON
   ↓
2. HANDLER FUNCTION RUNS
   handleIncrement()
   ↓
3. ACTION IS CALLED
   counterStore!.increment()
   ↓
4. OBSERVABLE IS UPDATED
   this.counter = this.counter + 1
   ↓
5. MOBX NOTIFIES OBSERVERS
   "Hey, counter changed!"
   ↓
6. COMPUTED VALUES RECALCULATE
   doubleValue, isEven, etc.
   ↓
7. COMPONENT RE-RENDERS
   observer() triggers re-render
   ↓
8. UI UPDATES
   New value appears on screen
```

### Example Timeline:

```typescript
// Initial state
counter = 0
doubleValue = 0 (computed)
Component shows: "0"

// User clicks "Increment" button
↓
handleIncrement() runs
↓
counterStore!.increment() called
↓
this.counter = 0 + 1 (now counter = 1)
↓
MobX: "counter changed from 0 to 1"
↓
doubleValue recalculates: 1 * 2 = 2
↓
Component re-renders
↓
UI updates to show: "1"
```

---

## Common Patterns

### Pattern 1: Using Multiple Stores

```typescript
// In stores.ts
const store = {
  counterStore: new CounterStore(),
  userStore: new UserStore(),
  todoStore: new TodoStore(),
};

// In component - inject multiple stores
interface MyComponentProps {
  counterStore?: CounterStore;
  userStore?: UserStore;
}

const MyComponent = observer(({ counterStore, userStore }: MyComponentProps) => {
  return (
    <div>
      <p>Counter: {counterStore!.counter}</p>
      <p>User: {userStore!.username}</p>
    </div>
  );
});

export default inject('counterStore', 'userStore')(MyComponent);
```

### Pattern 2: Async Actions (API Calls)

```typescript
class CounterStore {
  @observable counter: number = 0;
  @observable isLoading: boolean = false;
  @observable error: string | null = null;

  /**
   * Async action - fetches counter value from server
   */
  @action
  fetchCounterFromServer = async () => {
    this.isLoading = true;
    this.error = null;
    
    try {
      const response = await fetch('/api/counter');
      const data = await response.json();
      
      // Use runInAction for async updates
      runInAction(() => {
        this.counter = data.value;
        this.isLoading = false;
      });
    } catch (err) {
      runInAction(() => {
        this.error = 'Failed to fetch counter';
        this.isLoading = false;
      });
    }
  };
}
```

### Pattern 3: Store Dependencies

```typescript
// If CartStore needs ProductStore
class CartStore {
  @observable items: CartItem[] = [];
  
  // Inject dependency via constructor
  constructor(private productStore: ProductStore) {
    // Can now use this.productStore inside CartStore
  }
  
  @action
  addItem = (productId: string) => {
    const product = this.productStore.getProductById(productId);
    this.items.push({ product, quantity: 1 });
  };
}

// In stores.ts
const productStore = new ProductStore();
const cartStore = new CartStore(productStore); // Pass dependency

const store = {
  productStore,
  cartStore,
};
```

### Pattern 4: Conditional Rendering with Computed

```typescript
class UserStore {
  @observable isLoggedIn: boolean = false;
  @observable userRole: string = '';
  
  @computed
  get canEditContent(): boolean {
    return this.isLoggedIn && (this.userRole === 'admin' || this.userRole === 'editor');
  }
  
  @computed
  get displayName(): string {
    return this.isLoggedIn ? this.username : 'Guest';
  }
}

// In component
const MyComponent = observer(({ userStore }) => {
  return (
    <div>
      <h1>Welcome, {userStore!.displayName}</h1>
      
      {userStore!.canEditContent && (
        <button>Edit Content</button>
      )}
    </div>
  );
});
```

### Pattern 5: Forms with MobX

```typescript
class FormStore {
  @observable formData = {
    name: '',
    email: '',
    age: 0,
  };
  
  @observable errors: Record<string, string> = {};
  
  @action
  updateField = (field: string, value: any) => {
    this.formData[field] = value;
    this.validateField(field);
  };
  
  @action
  validateField = (field: string) => {
    if (field === 'email' && !this.formData.email.includes('@')) {
      this.errors.email = 'Invalid email';
    } else {
      delete this.errors[field];
    }
  };
  
  @computed
  get isFormValid(): boolean {
    return Object.keys(this.errors).length === 0 && 
           this.formData.name !== '' &&
           this.formData.email !== '';
  };
}

// In component
const MyForm = observer(({ formStore }) => {
  const handleChange = (e) => {
    formStore!.updateField(e.target.name, e.target.value);
  };
  
  return (
    <form>
      <input 
        name="name"
        value={formStore!.formData.name}
        onChange={handleChange}
      />
      
      <input 
        name="email"
        value={formStore!.formData.email}
        onChange={handleChange}
      />
      {formStore!.errors.email && <span>{formStore!.errors.email}</span>}
      
      <button disabled={!formStore!.isFormValid}>Submit</button>
    </form>
  );
});
```

---

## Key Takeaways

### ✅ DO:
- Always use `@action` to modify observables
- Wrap components with `observer()` to make them reactive
- Use `@computed` for derived values
- Keep stores focused (one store per domain)
- Use meaningful names for actions

### ❌ DON'T:
- Modify observables directly without `@action` (bad practice)
- Forget to wrap components with `observer()`
- Create store instances in components (create once in stores.ts)
- Access stores without injecting them
- Put UI logic in stores (keep stores for data/business logic only)

---

## Debugging Tips

### 1. Check if component is observing
```typescript
// If component doesn't update, make sure it's wrapped:
export default inject('counterStore')(observer(Counter));
// NOT just: export default inject('counterStore')(Counter);
```

### 2. Verify store is injected correctly
```typescript
// In component, log to check:
console.log('CounterStore:', counterStore);
// Should show the store instance, not undefined
```

### 3. Check action is being called
```typescript
@action
increment = () => {
  console.log('Increment called, current value:', this.counter);
  this.counter = this.counter + 1;
  console.log('New value:', this.counter);
};
```

### 4. Use MobX DevTools
```bash
npm install mobx-react-devtools --save-dev
```

```typescript
// In development mode only
import DevTools from 'mobx-react-devtools';

function App() {
  return (
    <>
      <Counter />
      {process.env.NODE_ENV === 'development' && <DevTools />}
    </>
  );
}
```

---

## Comparison with Redux Toolkit

| Feature | MobX | Redux Toolkit |
|---------|------|---------------|
| **Boilerplate** | Less code | More code |
| **Learning Curve** | Easier | Steeper |
| **Reactivity** | Automatic | Manual selectors |
| **Actions** | Class methods | Action creators |
| **State Updates** | Direct assignment | Reducers |
| **Computed Values** | @computed | Reselect |

---

## Summary

1. **Install**: `npm install mobx mobx-react`

2. **Create Store**: Use `@observable`, `@action`, `@computed`

3. **Register Store**: Add to `stores.ts`

4. **Provide Store**: Already done in `index.tsx` with `<Provider>`

5. **Use in Component**: 
   - `inject('counterStore')` to get the store
   - `observer()` to make component reactive

6. **Access State**: `counterStore!.counter`

7. **Update State**: Call actions like `counterStore!.increment()`

That's it! You now have a fully functional MobX setup following your project's pattern. 🎉

---

## Next Steps

- Try creating a `TodoStore` with add/remove/toggle actions
- Experiment with computed values for filtering todos
- Create a `UserStore` to manage authentication state
- Build a `ThemeStore` to handle dark/light mode

Happy coding! 🚀
