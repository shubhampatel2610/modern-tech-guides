# 🚀 Redux Toolkit Counter App - Complete Guide

This guide explains how to implement a counter app using Redux Toolkit, step by step.

---

## 📋 Table of Contents

1. [What is Redux Toolkit?](#what-is-redux-toolkit)
2. [Traditional Redux vs Redux Toolkit](#traditional-redux-vs-redux-toolkit)
3. [Installation](#installation)
4. [Project Structure](#project-structure)
5. [Step-by-Step Implementation](#step-by-step-implementation)
6. [How It Works](#how-it-works)
7. [Using in Components](#using-in-components)
8. [Async Operations](#async-operations)
9. [Complete Code Example](#complete-code-example)
10. [Common Patterns](#common-patterns)

---

## 🤔 What is Redux Toolkit?

**Redux Toolkit** is the official, recommended way to write Redux logic. It simplifies Redux development by:

- ✅ Reducing boilerplate code
- ✅ Following best practices automatically
- ✅ Including useful utilities
- ✅ Better TypeScript support
- ✅ Built-in DevTools support

---

## 📊 Traditional Redux vs Redux Toolkit

### Traditional Redux (Your Current Project)

**3 Separate Files:**

**1. `counter-action-types.tsx`** - Action constants
```typescript
export const COUNTER = 'COUNTER';
export const INCREASE_COUNT = 'INCREASE_COUNT';
export const DECREASE_COUNT = 'DECREASE_COUNT';
```

**2. `counter-action-creator.tsx`** - Action creators
```typescript
export const increaseCount = (response: any) => ({
    type: INCREASE_COUNT,
    payload: response
});

export const decreaseCount = (response: any) => ({
    type: DECREASE_COUNT,
    payload: response
});
```

**3. `counter-reducer.tsx`** - Reducer
```typescript
export const CounterReducer = (state = initialState, action: any) => {
    if (action.type === ActionTypes.INCREASE_COUNT) {
        return { ...state, counterValue: action.payload };
    } else if (action.type === ActionTypes.DECREASE_COUNT) {
        return { ...state, counterValue: action.payload };
    }
    // ... more conditions
    return state;
}
```

**4. `configure-store.tsx`** - Store setup
```typescript
import {applyMiddleware, combineReducers, createStore} from 'redux';

export const rootReducers = combineReducers({
    counterStore: CounterReducer,
    // ... other reducers
});

export default function configureStore() {
    const store = createStore(rootReducers, composeWithDevTools(middlewareEnhancer));
    return store;
}
```

---

### Redux Toolkit (Modern Approach)

**1 File for Everything:** `counterSlice.ts`

```typescript
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
  },
});

export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

**Store setup** - Much simpler!
```typescript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

---

## 📦 Installation

```bash
npm install @reduxjs/toolkit react-redux
```

**What gets installed:**
- `@reduxjs/toolkit` - Core Redux Toolkit library
- `react-redux` - React bindings for Redux (hooks like useSelector, useDispatch)

---

## 📁 Project Structure

```
src/app/src/component/
├── state-management/
│   └── counter/
│       ├── counterSlice.ts                          # Slice (actions + reducer)
│       ├── configure-store-counter-example.tsx      # Store configuration
│       └── hooks.ts                                 # Typed hooks
└── Counter/
    ├── Counter.tsx                                  # Counter component
    └── CounterApp.tsx                               # App with Provider
```

---

## 🛠️ Step-by-Step Implementation

### **Step 1: Create the Counter Slice**

**File:** `src/app/src/component/state-management/counter/counterSlice.ts`

```typescript
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit';

// Define the state shape
interface CounterState {
  value: number;
  isLoading: boolean;
}

// Initial state
const initialState: CounterState = {
  value: 0,
  isLoading: false,
};

// Create the slice
const counterSlice = createSlice({
  name: 'counter', // Prefix for action types
  initialState,
  reducers: {
    // Simple increment action
    increment: (state) => {
      state.value += 1;
    },
    
    // Simple decrement action
    decrement: (state) => {
      state.value -= 1;
    },
    
    // Increment by specific amount
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
    
    // Reset counter
    reset: (state) => {
      state.value = 0;
    },
  },
});

// Export actions
export const { increment, decrement, incrementByAmount, reset } = counterSlice.actions;

// Export reducer
export default counterSlice.reducer;
```

**What happens here:**
- `createSlice` automatically generates:
  - Action types: `'counter/increment'`, `'counter/decrement'`
  - Action creators: `increment()`, `decrement()`
  - Reducer function that handles these actions

---

### **Step 2: Configure the Store**

**File:** `src/app/src/component/state-management/counter/configure-store-counter-example.tsx`

```typescript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

export default function configureCounterStore() {
  const store = configureStore({
    reducer: {
      counter: counterReducer, // Key 'counter' = access via state.counter
    },
  });
  
  return store;
}

// Export types for TypeScript
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

export const store = configureCounterStore();
```

**What `configureStore` does automatically:**
- ✅ Combines reducers (no need for `combineReducers`)
- ✅ Adds redux-thunk middleware
- ✅ Adds Redux DevTools Extension
- ✅ Adds development checks (serializable state, etc.)

---

### **Step 3: Create Typed Hooks** (Optional but Recommended)

**File:** `src/app/src/component/state-management/counter/hooks.ts`

```typescript
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import type { RootState, AppDispatch } from './configure-store-counter-example';

// Typed dispatch hook
export const useAppDispatch = () => useDispatch<AppDispatch>();

// Typed selector hook
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

**Benefits:**
- Full TypeScript autocomplete
- Type safety for state and actions
- No need to type every useSelector call

---

### **Step 4: Create Counter Component**

**File:** `src/app/src/component/Counter/Counter.tsx`

```typescript
import React from 'react';
import { useAppDispatch, useAppSelector } from '../state-management/counter/hooks';
import { increment, decrement, incrementByAmount, reset } from '../state-management/counter/counterSlice';

const Counter: React.FC = () => {
  // Get state from Redux
  const count = useAppSelector((state) => state.counter.value);
  const isLoading = useAppSelector((state) => state.counter.isLoading);
  
  // Get dispatch function
  const dispatch = useAppDispatch();

  return (
    <div style={{ padding: '40px', textAlign: 'center' }}>
      <h1>Redux Toolkit Counter</h1>
      
      <div style={{ fontSize: '72px', margin: '30px' }}>
        {count}
      </div>
      
      <div style={{ display: 'flex', gap: '10px', justifyContent: 'center' }}>
        <button onClick={() => dispatch(decrement())}>
          - Decrement
        </button>
        
        <button onClick={() => dispatch(increment())}>
          + Increment
        </button>
        
        <button onClick={() => dispatch(incrementByAmount(5))}>
          Add 5
        </button>
        
        <button onClick={() => dispatch(reset())}>
          Reset
        </button>
      </div>
    </div>
  );
};

export default Counter;
```

---

### **Step 5: Wrap App with Provider**

**File:** `src/app/src/component/Counter/CounterApp.tsx`

```typescript
import React from 'react';
import { Provider } from 'react-redux';
import { store } from '../state-management/counter/configure-store-counter-example';
import Counter from './Counter';

const CounterApp: React.FC = () => {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
};

export default CounterApp;
```

**Or in your main app file:**

```typescript
// In Main.tsx or App.tsx
import { Provider } from 'react-redux';
import { store } from './component/state-management/counter/configure-store-counter-example';

function App() {
  return (
    <Provider store={store}>
      {/* Your app components */}
    </Provider>
  );
}
```

---

## ⚙️ How It Works

### 1. **User clicks button**
```typescript
<button onClick={() => dispatch(increment())}>Increment</button>
```

### 2. **Action is dispatched to Redux**
```typescript
dispatch(increment())
// Creates action: { type: 'counter/increment' }
```

### 3. **Reducer handles the action**
```typescript
increment: (state) => {
  state.value += 1; // Updates state
}
```

### 4. **Component re-renders with new state**
```typescript
const count = useAppSelector((state) => state.counter.value);
// Gets updated value from store
```

---

## 🎯 Using in Components

### **Reading State (useAppSelector)**

```typescript
// Get single value
const count = useAppSelector((state) => state.counter.value);

// Get multiple values
const count = useAppSelector((state) => state.counter.value);
const isLoading = useAppSelector((state) => state.counter.isLoading);

// Get entire counter state
const counterState = useAppSelector((state) => state.counter);
```

### **Dispatching Actions (useAppDispatch)**

```typescript
const dispatch = useAppDispatch();

// Simple actions (no payload)
dispatch(increment());
dispatch(decrement());
dispatch(reset());

// Actions with payload
dispatch(incrementByAmount(5));
dispatch(incrementByAmount(10));
```

---

## 🔄 Async Operations

For API calls or async operations, use `createAsyncThunk`:

### **Create Async Thunk**

```typescript
// In counterSlice.ts
import { createAsyncThunk } from '@reduxjs/toolkit';

// Async action - similar to your getScheduleList
export const incrementAsync = createAsyncThunk(
  'counter/incrementAsync',
  async (amount: number) => {
    // API call or async operation
    const response = await fetch('/api/increment', {
      method: 'POST',
      body: JSON.stringify({ amount }),
    });
    const data = await response.json();
    return data.newValue;
  }
);
```

### **Handle Async States in Reducer**

```typescript
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0, isLoading: false, error: null },
  reducers: {
    // ... sync reducers
  },
  extraReducers: (builder) => {
    builder
      // When request starts
      .addCase(incrementAsync.pending, (state) => {
        state.isLoading = true;
        state.error = null;
      })
      // When request succeeds
      .addCase(incrementAsync.fulfilled, (state, action) => {
        state.isLoading = false;
        state.value = action.payload;
      })
      // When request fails
      .addCase(incrementAsync.rejected, (state, action) => {
        state.isLoading = false;
        state.error = action.error.message;
      });
  },
});
```

### **Use in Component**

```typescript
import { incrementAsync } from '../state-management/counter/counterSlice';

const Counter = () => {
  const dispatch = useAppDispatch();
  const isLoading = useAppSelector((state) => state.counter.isLoading);
  
  return (
    <button 
      onClick={() => dispatch(incrementAsync(10))}
      disabled={isLoading}
    >
      {isLoading ? 'Loading...' : 'Async Add 10'}
    </button>
  );
};
```

---

## 💡 Complete Code Example

### **Full Slice with Sync + Async Actions**

```typescript
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit';
import axios from 'axios';

// State interface
interface CounterState {
  value: number;
  isLoading: boolean;
  error: string | null;
}

// Initial state
const initialState: CounterState = {
  value: 0,
  isLoading: false,
  error: null,
};

// Async thunk for API call
export const fetchCounterValue = createAsyncThunk(
  'counter/fetchValue',
  async () => {
    const response = await axios.get('/api/counter');
    return response.data.value;
  }
);

// Slice
const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    // Sync actions
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
    reset: (state) => {
      state.value = 0;
      state.error = null;
    },
  },
  extraReducers: (builder) => {
    // Async actions
    builder
      .addCase(fetchCounterValue.pending, (state) => {
        state.isLoading = true;
        state.error = null;
      })
      .addCase(fetchCounterValue.fulfilled, (state, action) => {
        state.isLoading = false;
        state.value = action.payload;
      })
      .addCase(fetchCounterValue.rejected, (state, action) => {
        state.isLoading = false;
        state.error = action.error.message || 'Failed to fetch';
      });
  },
});

export const { increment, decrement, incrementByAmount, reset } = counterSlice.actions;
export default counterSlice.reducer;
```

---

## 🎨 Common Patterns

### **Pattern 1: Multiple State Values**

```typescript
const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    count: 0,
    step: 1,
    history: [] as number[],
  },
  reducers: {
    increment: (state) => {
      state.count += state.step;
      state.history.push(state.count);
    },
    setStep: (state, action: PayloadAction<number>) => {
      state.step = action.payload;
    },
    clearHistory: (state) => {
      state.history = [];
    },
  },
});
```

### **Pattern 2: Computed Values (Selectors)**

```typescript
// In counterSlice.ts
export const selectCount = (state: RootState) => state.counter.value;
export const selectIsEven = (state: RootState) => state.counter.value % 2 === 0;

// In component
import { selectCount, selectIsEven } from '../state-management/counter/counterSlice';

const count = useAppSelector(selectCount);
const isEven = useAppSelector(selectIsEven);
```

### **Pattern 3: Conditional Updates**

```typescript
reducers: {
  incrementIfOdd: (state) => {
    if (state.value % 2 !== 0) {
      state.value += 1;
    }
  },
  incrementIfLessThan: (state, action: PayloadAction<number>) => {
    if (state.value < action.payload) {
      state.value += 1;
    }
  },
}
```

---

## 🆚 Key Differences Summary

| Feature | Traditional Redux | Redux Toolkit |
|---------|------------------|---------------|
| **Action Types** | Manual string constants | Auto-generated |
| **Action Creators** | Manual functions | Auto-generated |
| **Reducer** | Switch/if-else statements | Simple object methods |
| **Immutability** | Manual spread operators | "Mutable" updates (Immer) |
| **Store Setup** | `createStore` + manual middleware | `configureStore` (automatic) |
| **DevTools** | Manual setup | Built-in |
| **Async Actions** | Manual thunks | `createAsyncThunk` |
| **Boilerplate** | High | Low |
| **Files Needed** | 3+ files | 1 file (slice) |

---

## ✅ Best Practices

1. **Use TypeScript** - Get full type safety
2. **Create typed hooks** - Use `useAppDispatch` and `useAppSelector`
3. **One slice per feature** - Keep related state together
4. **Use `createAsyncThunk`** - For all async operations
5. **Normalize state shape** - For complex data (use `createEntityAdapter`)
6. **Use selectors** - Extract data access logic
7. **Keep slices focused** - Don't make them too large

---

## 🎓 Learning Path

1. ✅ **Start here** - Create simple counter app
2. ✅ **Add async actions** - Fetch data from API
3. ✅ **Multiple slices** - Create todo list + counter
4. ✅ **Entity adapter** - Handle normalized data
5. ✅ **RTK Query** - Advanced data fetching

---

## 📚 Resources

- **Official Docs:** https://redux-toolkit.js.org/
- **Tutorial:** https://redux-toolkit.js.org/tutorials/quick-start
- **TypeScript Guide:** https://redux-toolkit.js.org/usage/usage-with-typescript
- **Best Practices:** https://redux.js.org/style-guide/style-guide

---

## 🚦 Quick Reference

### **Installation**
```bash
npm install @reduxjs/toolkit react-redux
```

### **Create Slice**
```typescript
const slice = createSlice({
  name: 'feature',
  initialState,
  reducers: { /* actions */ }
});
```

### **Configure Store**
```typescript
const store = configureStore({
  reducer: { feature: featureReducer }
});
```

### **Use in Component**
```typescript
const data = useAppSelector(state => state.feature.data);
const dispatch = useAppDispatch();
dispatch(actionName(payload));
```

---

## 🎯 Your Next Steps

1. Run `npm install @reduxjs/toolkit react-redux`
2. Create `counterSlice.ts` file
3. Create store configuration
4. Create Counter component
5. Test it in your app!

Good luck! 🚀
