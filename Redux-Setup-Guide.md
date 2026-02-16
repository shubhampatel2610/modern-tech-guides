# 🔧 Traditional Redux - Complete Guide

This guide explains how to implement state management using Traditional Redux, step by step.

---

## 📋 Table of Contents

1. [What is Redux?](#what-is-redux)
2. [Core Concepts](#core-concepts)
3. [Installation](#installation)
4. [Project Structure](#project-structure)
5. [Step-by-Step Implementation](#step-by-step-implementation)
6. [How It Works](#how-it-works)
7. [Using in Components](#using-in-components)
8. [Async Operations](#async-operations)
9. [Complete Code Example](#complete-code-example)
10. [Common Patterns](#common-patterns)

---

## 🤔 What is Redux?

**Redux** is a predictable state container for JavaScript apps. It helps you manage global application state in a centralized store.

### Key Principles:

1. **Single Source of Truth** - All state lives in one store
2. **State is Read-Only** - Only way to change state is to dispatch actions
3. **Changes are Made with Pure Functions** - Reducers specify how state changes

### When to Use Redux:

- ✅ Large applications with complex state
- ✅ State shared across many components
- ✅ State that needs to be accessed from anywhere
- ✅ Need for predictable state changes
- ✅ Time-travel debugging capabilities

---

## 🧩 Core Concepts

### 1. **Store**
The single object that holds all application state.

```typescript
const store = createStore(rootReducer);
```

### 2. **Actions**
Plain JavaScript objects that describe what happened.

```typescript
{
  type: 'INCREMENT_COUNTER',
  payload: 5
}
```

### 3. **Action Creators**
Functions that create and return action objects.

```typescript
const incrementCounter = (amount) => ({
  type: 'INCREMENT_COUNTER',
  payload: amount
});
```

### 4. **Reducers**
Pure functions that take the current state and an action, and return the new state.

```typescript
const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREMENT_COUNTER':
      return { ...state, count: state.count + action.payload };
    default:
      return state;
  }
};
```

### 5. **Dispatch**
Method to send actions to the store.

```typescript
store.dispatch(incrementCounter(5));
```

---

## 📦 Installation

```bash
npm install redux react-redux redux-thunk
```

**For TypeScript projects:**
```bash
npm install redux react-redux redux-thunk
npm install --save-dev @types/react-redux
```

**What gets installed:**
- `redux` - Core Redux library
- `react-redux` - React bindings (Provider, connect, useSelector, useDispatch)
- `redux-thunk` - Middleware for async actions

---

## 📁 Project Structure

```
src/app/src/component/
├── state-management/
│   └── counter/
│       ├── counter-action-types.ts          # Action type constants
│       ├── counter-action-creators.ts       # Action creator functions
│       ├── counter-reducer.ts               # Reducer function
│       ├── counter-selectors.ts             # Selector functions (optional)
│       └── configure-store.ts               # Store configuration
└── Counter/
    ├── Counter.tsx                          # Counter component
    └── CounterApp.tsx                       # App with Provider
```

**Compared to your current project:**
```
src/app/src/component/state-management/fsautocomm/
├── fsautocomm-action-types.tsx       # Action constants
├── fsautocomm-action-creator.tsx     # Action creators
└── fsautocomm-reducer.tsx            # Reducer logic
```

---

## 🛠️ Step-by-Step Implementation

### **Step 1: Define Action Types**

**File:** `counter-action-types.ts`

```typescript
// Action type constants
// Using constants prevents typos and makes refactoring easier

export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
export const INCREMENT_BY_AMOUNT = 'INCREMENT_BY_AMOUNT';
export const RESET = 'RESET';
export const SET_LOADING = 'SET_LOADING';
export const SET_ERROR = 'SET_ERROR';

// Can also use object for namespacing
export const CounterActionTypes = {
  INCREMENT: 'counter/INCREMENT',
  DECREMENT: 'counter/DECREMENT',
  INCREMENT_BY_AMOUNT: 'counter/INCREMENT_BY_AMOUNT',
  RESET: 'counter/RESET',
  SET_LOADING: 'counter/SET_LOADING',
  SET_ERROR: 'counter/SET_ERROR',
} as const;
```

**Why use constants?**
- ✅ Prevents typos (TypeScript catches errors)
- ✅ Easy to find all action types
- ✅ Autocomplete in IDE
- ✅ Single source of truth

---

### **Step 2: Create Action Creators**

**File:** `counter-action-creators.ts`

```typescript
import * as ActionTypes from './counter-action-types';

// Simple action creators (no payload)
export const increment = () => ({
  type: ActionTypes.INCREMENT
});

export const decrement = () => ({
  type: ActionTypes.DECREMENT
});

export const reset = () => ({
  type: ActionTypes.RESET
});

// Action creators with payload
export const incrementByAmount = (amount: number) => ({
  type: ActionTypes.INCREMENT_BY_AMOUNT,
  payload: amount
});

export const setLoading = (isLoading: boolean) => ({
  type: ActionTypes.SET_LOADING,
  payload: isLoading
});

export const setError = (error: string | null) => ({
  type: ActionTypes.SET_ERROR,
  payload: error
});

// TypeScript: Define action types
export type CounterAction =
  | ReturnType<typeof increment>
  | ReturnType<typeof decrement>
  | ReturnType<typeof reset>
  | ReturnType<typeof incrementByAmount>
  | ReturnType<typeof setLoading>
  | ReturnType<typeof setError>;
```

---

### **Step 3: Create Reducer**

**File:** `counter-reducer.ts`

```typescript
import * as ActionTypes from './counter-action-types';
import { CounterAction } from './counter-action-creators';

// Define state interface
export interface CounterState {
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

// Reducer function
export const counterReducer = (
  state: CounterState = initialState,
  action: CounterAction
): CounterState => {
  switch (action.type) {
    case ActionTypes.INCREMENT:
      return {
        ...state,
        value: state.value + 1,
      };

    case ActionTypes.DECREMENT:
      return {
        ...state,
        value: state.value - 1,
      };

    case ActionTypes.INCREMENT_BY_AMOUNT:
      return {
        ...state,
        value: state.value + action.payload,
      };

    case ActionTypes.RESET:
      return {
        ...state,
        value: 0,
        error: null,
      };

    case ActionTypes.SET_LOADING:
      return {
        ...state,
        isLoading: action.payload,
      };

    case ActionTypes.SET_ERROR:
      return {
        ...state,
        error: action.payload,
      };

    default:
      return state;
  }
};
```

**Alternative: Using if-else (from your project)**
```typescript
export const FSAutoComReducer = (state = initialState, action: any) => {
    if (action.type === ActionTypes.GET_SCHEDULE_LIST) {
        return { ...state, scheduleList: action.payload };
    } else if (action.type === ActionTypes.GET_SCHEDULE_TOTAL_COUNT) {
        return { ...state, scheduleTotalCount: action.payload };
    } else if (action.type === ActionTypes.IS_SCHEDULE_DELETE_LOADING) {
        return { ...state, isDeleteLoading: action.payload };
    }
    return state;
}
```

**⚠️ Immutability Rules:**
```typescript
// ❌ WRONG - Mutates state directly
case INCREMENT:
  state.value++;
  return state;

// ✅ CORRECT - Creates new object
case INCREMENT:
  return { ...state, value: state.value + 1 };

// ❌ WRONG - Mutates nested object
case UPDATE_USER:
  state.user.name = action.payload;
  return state;

// ✅ CORRECT - Creates new nested object
case UPDATE_USER:
  return {
    ...state,
    user: { ...state.user, name: action.payload }
  };
```

---

### **Step 4: Configure Store**

**File:** `configure-store.ts`

```typescript
import { createStore, combineReducers, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
import { counterReducer } from './counter-reducer';

// Combine all reducers
const rootReducer = combineReducers({
  counter: counterReducer,
  // Add more reducers here
  // users: usersReducer,
  // posts: postsReducer,
});

// Redux DevTools Extension
const composeEnhancers =
  (window as any).__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

// Create store with middleware
export default function configureStore() {
  const store = createStore(
    rootReducer,
    composeEnhancers(applyMiddleware(thunk))
  );
  
  return store;
}

// Export types for TypeScript
export type RootState = ReturnType<typeof rootReducer>;
export type AppDispatch = ReturnType<typeof configureStore>['dispatch'];

// Create store instance
export const store = configureStore();
```

**What's happening:**
1. **`combineReducers`** - Combines multiple reducers into one
2. **`thunk`** - Middleware for async actions
3. **`composeEnhancers`** - Adds Redux DevTools support
4. **`applyMiddleware`** - Adds middleware to store

---

### **Step 5: Create Typed Hooks** (Optional but Recommended)

**File:** `hooks.ts`

```typescript
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import type { RootState, AppDispatch } from './configure-store';

// Typed dispatch hook
export const useAppDispatch = () => useDispatch<AppDispatch>();

// Typed selector hook
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

**Benefits:**
- ✅ Full TypeScript autocomplete
- ✅ Type safety for state access
- ✅ No need to type every `useSelector` call

---

### **Step 6: Create Selectors** (Optional but Recommended)

**File:** `counter-selectors.ts`

```typescript
import { RootState } from './configure-store';

// Basic selectors
export const selectCounterValue = (state: RootState) => state.counter.value;
export const selectCounterLoading = (state: RootState) => state.counter.isLoading;
export const selectCounterError = (state: RootState) => state.counter.error;

// Derived selectors (computed values)
export const selectIsEven = (state: RootState) => state.counter.value % 2 === 0;
export const selectIsPositive = (state: RootState) => state.counter.value > 0;
export const selectDoubleValue = (state: RootState) => state.counter.value * 2;
```

**Why use selectors?**
- ✅ Encapsulates state shape
- ✅ Reusable across components
- ✅ Easy to add computed values
- ✅ Can use memoization libraries (reselect)

---

### **Step 7: Create Counter Component**

**File:** `Counter.tsx`

```typescript
import React from 'react';
import { useAppDispatch, useAppSelector } from '../state-management/counter/hooks';
import {
  increment,
  decrement,
  incrementByAmount,
  reset
} from '../state-management/counter/counter-action-creators';
import {
  selectCounterValue,
  selectCounterLoading,
  selectIsEven
} from '../state-management/counter/counter-selectors';

const Counter: React.FC = () => {
  // Get state using selectors
  const count = useAppSelector(selectCounterValue);
  const isLoading = useAppSelector(selectCounterLoading);
  const isEven = useAppSelector(selectIsEven);
  
  // Or get state directly
  // const count = useAppSelector((state) => state.counter.value);
  
  // Get dispatch function
  const dispatch = useAppDispatch();

  return (
    <div style={{ padding: '40px', textAlign: 'center' }}>
      <h1>Traditional Redux Counter</h1>
      
      <div style={{ fontSize: '72px', margin: '30px' }}>
        {count}
      </div>
      
      <div style={{ marginBottom: '20px' }}>
        <span>The number is {isEven ? 'even' : 'odd'}</span>
      </div>
      
      <div style={{ display: 'flex', gap: '10px', justifyContent: 'center' }}>
        <button 
          onClick={() => dispatch(decrement())}
          disabled={isLoading}
        >
          - Decrement
        </button>
        
        <button 
          onClick={() => dispatch(increment())}
          disabled={isLoading}
        >
          + Increment
        </button>
        
        <button 
          onClick={() => dispatch(incrementByAmount(5))}
          disabled={isLoading}
        >
          Add 5
        </button>
        
        <button 
          onClick={() => dispatch(reset())}
          disabled={isLoading}
        >
          Reset
        </button>
      </div>
      
      {isLoading && <div>Loading...</div>}
    </div>
  );
};

export default Counter;
```

---

### **Step 8: Wrap App with Provider**

**File:** `CounterApp.tsx`

```typescript
import React from 'react';
import { Provider } from 'react-redux';
import { store } from '../state-management/counter/configure-store';
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

**Or in your main app:**
```typescript
// In index.tsx or App.tsx
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './component/state-management/counter/configure-store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

---

## ⚙️ How It Works

### **The Redux Flow:**

```
┌─────────────┐
│  Component  │
└──────┬──────┘
       │
       │ 1. dispatch(action)
       ▼
┌─────────────┐
│   Action    │ { type: 'INCREMENT', payload: 5 }
└──────┬──────┘
       │
       │ 2. Action sent to store
       ▼
┌─────────────┐
│   Reducer   │ (state, action) => newState
└──────┬──────┘
       │
       │ 3. New state calculated
       ▼
┌─────────────┐
│    Store    │ { counter: { value: 5 } }
└──────┬──────┘
       │
       │ 4. Component re-renders
       ▼
┌─────────────┐
│  Component  │ Updated with new state
└─────────────┘
```

### **Step-by-Step Example:**

**1. User clicks button:**
```typescript
<button onClick={() => dispatch(increment())}>Increment</button>
```

**2. Action creator creates action:**
```typescript
increment() // Returns: { type: 'INCREMENT' }
```

**3. Action dispatched to store:**
```typescript
dispatch({ type: 'INCREMENT' })
```

**4. Reducer processes action:**
```typescript
case 'INCREMENT':
  return { ...state, value: state.value + 1 };
```

**5. Store updates state:**
```typescript
// Old state: { counter: { value: 5 } }
// New state: { counter: { value: 6 } }
```

**6. Component re-renders with new value:**
```typescript
const count = useAppSelector((state) => state.counter.value); // 6
```

---

## 🎯 Using in Components

### **Method 1: Hooks (Recommended)**

```typescript
import { useSelector, useDispatch } from 'react-redux';
import { increment } from './counter-action-creators';
import { RootState } from './configure-store';

const Counter = () => {
  // Get state
  const count = useSelector((state: RootState) => state.counter.value);
  
  // Get dispatch
  const dispatch = useDispatch();
  
  return (
    <div>
      <div>{count}</div>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
};
```

### **Method 2: connect() HOC (Legacy)**

```typescript
import { connect } from 'react-redux';
import { increment, decrement } from './counter-action-creators';
import { RootState } from './configure-store';

interface StateProps {
  count: number;
}

interface DispatchProps {
  increment: () => void;
  decrement: () => void;
}

type Props = StateProps & DispatchProps;

const Counter: React.FC<Props> = ({ count, increment, decrement }) => {
  return (
    <div>
      <div>{count}</div>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
};

// Map state to props
const mapStateToProps = (state: RootState): StateProps => ({
  count: state.counter.value,
});

// Map dispatch to props
const mapDispatchToProps: DispatchProps = {
  increment,
  decrement,
};

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```

---

## 🔄 Async Operations with Redux Thunk

### **What is Redux Thunk?**

Redux Thunk is middleware that allows you to write action creators that return a **function** instead of an action object. This function can perform async operations.

### **Basic Thunk Pattern**

```typescript
// Thunk action creator
export const fetchCounterValue = () => {
  return async (dispatch, getState) => {
    // Dispatch loading action
    dispatch(setLoading(true));
    
    try {
      // API call
      const response = await fetch('/api/counter');
      const data = await response.json();
      
      // Dispatch success action
      dispatch(incrementByAmount(data.value));
      dispatch(setLoading(false));
    } catch (error) {
      // Dispatch error action
      dispatch(setError(error.message));
      dispatch(setLoading(false));
    }
  };
};
```

### **Complete Async Example**

**File:** `counter-action-creators.ts`

```typescript
import * as ActionTypes from './counter-action-types';
import { Dispatch } from 'redux';
import axios from 'axios';

// Sync action creators
export const setLoading = (isLoading: boolean) => ({
  type: ActionTypes.SET_LOADING,
  payload: isLoading,
});

export const setError = (error: string | null) => ({
  type: ActionTypes.SET_ERROR,
  payload: error,
});

export const setCounterValue = (value: number) => ({
  type: ActionTypes.SET_VALUE,
  payload: value,
});

// Async thunk action creator
export const fetchCounterFromAPI = () => {
  return async (dispatch: Dispatch) => {
    dispatch(setLoading(true));
    dispatch(setError(null));
    
    try {
      const response = await axios.get('/api/counter');
      dispatch(setCounterValue(response.data.value));
    } catch (error: any) {
      dispatch(setError(error.message || 'Failed to fetch counter'));
    } finally {
      dispatch(setLoading(false));
    }
  };
};

// Thunk with parameters
export const incrementAsync = (amount: number, delay: number = 1000) => {
  return async (dispatch: Dispatch) => {
    dispatch(setLoading(true));
    
    // Simulate API delay
    await new Promise(resolve => setTimeout(resolve, delay));
    
    dispatch(incrementByAmount(amount));
    dispatch(setLoading(false));
  };
};

// Thunk that uses getState
export const incrementIfOdd = () => {
  return (dispatch: Dispatch, getState: any) => {
    const state = getState();
    const currentValue = state.counter.value;
    
    if (currentValue % 2 !== 0) {
      dispatch(increment());
    }
  };
};
```

### **Use in Component**

```typescript
import { fetchCounterFromAPI, incrementAsync } from './counter-action-creators';

const Counter = () => {
  const dispatch = useAppDispatch();
  const count = useAppSelector((state) => state.counter.value);
  const isLoading = useAppSelector((state) => state.counter.isLoading);

  // Fetch on mount
  useEffect(() => {
    dispatch(fetchCounterFromAPI());
  }, [dispatch]);

  return (
    <div>
      <div>{isLoading ? 'Loading...' : count}</div>
      <button onClick={() => dispatch(incrementAsync(10))}>
        Async +10
      </button>
    </div>
  );
};
```

---

## 💡 Complete Code Example

### **Full Implementation with Async**

**counter-action-types.ts:**
```typescript
export const INCREMENT = 'counter/INCREMENT';
export const DECREMENT = 'counter/DECREMENT';
export const INCREMENT_BY_AMOUNT = 'counter/INCREMENT_BY_AMOUNT';
export const RESET = 'counter/RESET';
export const SET_VALUE = 'counter/SET_VALUE';
export const SET_LOADING = 'counter/SET_LOADING';
export const SET_ERROR = 'counter/SET_ERROR';
```

**counter-action-creators.ts:**
```typescript
import * as ActionTypes from './counter-action-types';
import { Dispatch } from 'redux';
import axios from 'axios';

// Sync actions
export const increment = () => ({ type: ActionTypes.INCREMENT });
export const decrement = () => ({ type: ActionTypes.DECREMENT });
export const reset = () => ({ type: ActionTypes.RESET });

export const incrementByAmount = (amount: number) => ({
  type: ActionTypes.INCREMENT_BY_AMOUNT,
  payload: amount,
});

export const setValue = (value: number) => ({
  type: ActionTypes.SET_VALUE,
  payload: value,
});

export const setLoading = (isLoading: boolean) => ({
  type: ActionTypes.SET_LOADING,
  payload: isLoading,
});

export const setError = (error: string | null) => ({
  type: ActionTypes.SET_ERROR,
  payload: error,
});

// Async thunk
export const fetchCounter = () => {
  return async (dispatch: Dispatch) => {
    dispatch(setLoading(true));
    try {
      const response = await axios.get('/api/counter');
      dispatch(setValue(response.data.value));
    } catch (error: any) {
      dispatch(setError(error.message));
    } finally {
      dispatch(setLoading(false));
    }
  };
};
```

**counter-reducer.ts:**
```typescript
import * as ActionTypes from './counter-action-types';

export interface CounterState {
  value: number;
  isLoading: boolean;
  error: string | null;
}

const initialState: CounterState = {
  value: 0,
  isLoading: false,
  error: null,
};

export const counterReducer = (
  state = initialState,
  action: any
): CounterState => {
  switch (action.type) {
    case ActionTypes.INCREMENT:
      return { ...state, value: state.value + 1 };
      
    case ActionTypes.DECREMENT:
      return { ...state, value: state.value - 1 };
      
    case ActionTypes.INCREMENT_BY_AMOUNT:
      return { ...state, value: state.value + action.payload };
      
    case ActionTypes.SET_VALUE:
      return { ...state, value: action.payload };
      
    case ActionTypes.RESET:
      return { ...state, value: 0, error: null };
      
    case ActionTypes.SET_LOADING:
      return { ...state, isLoading: action.payload };
      
    case ActionTypes.SET_ERROR:
      return { ...state, error: action.payload };
      
    default:
      return state;
  }
};
```

---

## 🎨 Common Patterns

### **Pattern 1: Loading States**

```typescript
// Action creators
export const fetchDataStart = () => ({ type: 'FETCH_START' });
export const fetchDataSuccess = (data) => ({ type: 'FETCH_SUCCESS', payload: data });
export const fetchDataFailure = (error) => ({ type: 'FETCH_FAILURE', payload: error });

// Reducer
const initialState = {
  data: null,
  loading: false,
  error: null,
};

switch (action.type) {
  case 'FETCH_START':
    return { ...state, loading: true, error: null };
  case 'FETCH_SUCCESS':
    return { ...state, loading: false, data: action.payload };
  case 'FETCH_FAILURE':
    return { ...state, loading: false, error: action.payload };
}
```

### **Pattern 2: Array Operations**

```typescript
// Add item to array
case 'ADD_TODO':
  return {
    ...state,
    todos: [...state.todos, action.payload]
  };

// Remove item from array
case 'REMOVE_TODO':
  return {
    ...state,
    todos: state.todos.filter(todo => todo.id !== action.payload)
  };

// Update item in array
case 'UPDATE_TODO':
  return {
    ...state,
    todos: state.todos.map(todo =>
      todo.id === action.payload.id
        ? { ...todo, ...action.payload.updates }
        : todo
    )
  };
```

### **Pattern 3: Nested State Updates**

```typescript
// Update nested object
case 'UPDATE_USER_NAME':
  return {
    ...state,
    user: {
      ...state.user,
      name: action.payload
    }
  };

// Update deeply nested object
case 'UPDATE_ADDRESS':
  return {
    ...state,
    user: {
      ...state.user,
      profile: {
        ...state.user.profile,
        address: {
          ...state.user.profile.address,
          city: action.payload
        }
      }
    }
  };
```

### **Pattern 4: Multiple Reducers**

```typescript
// configure-store.ts
import { combineReducers } from 'redux';
import { counterReducer } from './counter-reducer';
import { todosReducer } from './todos-reducer';
import { userReducer } from './user-reducer';

const rootReducer = combineReducers({
  counter: counterReducer,
  todos: todosReducer,
  user: userReducer,
});

export type RootState = ReturnType<typeof rootReducer>;
```

### **Pattern 5: Action Creators with Multiple Dispatches**

```typescript
export const saveAndNotify = (data: any) => {
  return async (dispatch: Dispatch) => {
    // Save data
    dispatch(setLoading(true));
    await saveData(data);
    dispatch(setData(data));
    dispatch(setLoading(false));
    
    // Show notification
    dispatch(showNotification('Data saved successfully!'));
    
    // Clear notification after delay
    setTimeout(() => {
      dispatch(clearNotification());
    }, 3000);
  };
};
```

---

## 🔍 Debugging Redux

### **1. Redux DevTools**

```typescript
// In configure-store.ts
const composeEnhancers =
  (window as any).__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const store = createStore(
  rootReducer,
  composeEnhancers(applyMiddleware(thunk))
);
```

**Features:**
- 📊 View all dispatched actions
- 🔄 Time-travel debugging
- 📝 Inspect state changes
- ⏮️ Replay actions

### **2. Logging Middleware**

```typescript
const logger = (store) => (next) => (action) => {
  console.group(action.type);
  console.info('dispatching', action);
  console.log('prev state', store.getState());
  let result = next(action);
  console.log('next state', store.getState());
  console.groupEnd();
  return result;
};

const store = createStore(
  rootReducer,
  applyMiddleware(logger, thunk)
);
```

### **3. Action Logging**

```typescript
// Log actions in action creators
export const increment = () => {
  console.log('INCREMENT action dispatched');
  return { type: ActionTypes.INCREMENT };
};
```

---

## ⚡ Performance Optimization

### **1. Shallow Equality in useSelector**

```typescript
// ❌ Creates new object every time - causes re-renders
const data = useSelector(state => ({
  count: state.counter.value,
  loading: state.counter.loading
}));

// ✅ Use multiple selectors
const count = useSelector(state => state.counter.value);
const loading = useSelector(state => state.counter.loading);

// ✅ Or use shallowEqual
import { shallowEqual } from 'react-redux';
const data = useSelector(state => ({
  count: state.counter.value,
  loading: state.counter.loading
}), shallowEqual);
```

### **2. Memoized Selectors with Reselect**

```bash
npm install reselect
```

```typescript
import { createSelector } from 'reselect';

const selectTodos = (state: RootState) => state.todos;
const selectFilter = (state: RootState) => state.filter;

// Memoized selector - only recomputes when inputs change
export const selectFilteredTodos = createSelector(
  [selectTodos, selectFilter],
  (todos, filter) => {
    return todos.filter(todo => {
      // Expensive filtering logic
      return todo.status === filter;
    });
  }
);
```

### **3. Batch Updates**

```typescript
import { batch } from 'react-redux';

// Batch multiple dispatches to trigger only one re-render
batch(() => {
  dispatch(action1());
  dispatch(action2());
  dispatch(action3());
});
```

---

## 🆚 Common Mistakes & Solutions

### **Mistake 1: Mutating State**

```typescript
// ❌ WRONG
case 'ADD_TODO':
  state.todos.push(action.payload);
  return state;

// ✅ CORRECT
case 'ADD_TODO':
  return {
    ...state,
    todos: [...state.todos, action.payload]
  };
```

### **Mistake 2: Not Returning State**

```typescript
// ❌ WRONG - No default case
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
  }
  // Missing return statement!
};

// ✅ CORRECT
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    default:
      return state; // Always return state
  }
};
```

### **Mistake 3: Async Logic in Reducer**

```typescript
// ❌ WRONG - Reducer must be pure
case 'FETCH_DATA':
  fetch('/api/data').then(res => {
    state.data = res.data; // Don't do async in reducer!
  });
  return state;

// ✅ CORRECT - Use thunk
export const fetchData = () => {
  return async (dispatch) => {
    const res = await fetch('/api/data');
    dispatch({ type: 'SET_DATA', payload: res.data });
  };
};
```

### **Mistake 4: Dispatching in Render**

```typescript
// ❌ WRONG - Causes infinite loop
const Counter = () => {
  const dispatch = useDispatch();
  dispatch(increment()); // Don't dispatch in render!
  
  return <div>Counter</div>;
};

// ✅ CORRECT - Use useEffect or event handlers
const Counter = () => {
  const dispatch = useDispatch();
  
  useEffect(() => {
    dispatch(fetchData());
  }, [dispatch]);
  
  return <div>Counter</div>;
};
```

---

## 📚 Best Practices

1. **✅ Use TypeScript** - Type safety prevents many errors
2. **✅ Keep reducers pure** - No side effects, no mutations
3. **✅ Use action constants** - Prevents typos
4. **✅ Create selectors** - Encapsulate state access
5. **✅ Split reducers** - One reducer per feature
6. **✅ Use Redux Thunk** - For async operations
7. **✅ Enable DevTools** - Essential for debugging
8. **✅ Normalize state shape** - Flat structure, avoid nesting
9. **✅ Keep state minimal** - Derive data in selectors
10. **✅ Use hooks** - `useSelector` and `useDispatch` over `connect`

---

## 🎓 When to Use What

### **Use Traditional Redux When:**
- ✅ Working on existing Redux codebase
- ✅ Team is familiar with Redux
- ✅ Need fine-grained control
- ✅ Understanding Redux internals is important

### **Consider Redux Toolkit When:**
- ✅ Starting new project
- ✅ Want less boilerplate
- ✅ Modern best practices
- ✅ Better TypeScript support

---

## 🔗 Resources

- **Official Docs:** https://redux.js.org/
- **React-Redux:** https://react-redux.js.org/
- **Redux Thunk:** https://github.com/reduxjs/redux-thunk
- **Reselect:** https://github.com/reduxjs/reselect
- **DevTools:** https://github.com/reduxjs/redux-devtools

---

## 📝 Quick Reference

### **Installation**
```bash
npm install redux react-redux redux-thunk
```

### **Basic Structure**
```typescript
// 1. Action Types
export const INCREMENT = 'INCREMENT';

// 2. Action Creators
export const increment = () => ({ type: INCREMENT });

// 3. Reducer
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case INCREMENT:
      return { ...state, count: state.count + 1 };
    default:
      return state;
  }
};

// 4. Store
const store = createStore(reducer, applyMiddleware(thunk));

// 5. Component
const count = useSelector(state => state.count);
const dispatch = useDispatch();
dispatch(increment());
```

### **Async Pattern**
```typescript
export const fetchData = () => {
  return async (dispatch) => {
    dispatch({ type: 'LOADING' });
    try {
      const data = await api.fetch();
      dispatch({ type: 'SUCCESS', payload: data });
    } catch (error) {
      dispatch({ type: 'ERROR', payload: error });
    }
  };
};
```

---

## 🚀 Your Next Steps

1. **Understand your current implementation** - Review existing reducers
2. **Install dependencies** - `npm install redux react-redux redux-thunk`
3. **Create a simple counter** - Practice the basics
4. **Add async operations** - Learn thunks
5. **Consider migrating to Redux Toolkit** - For new features

Good luck! 🎉
