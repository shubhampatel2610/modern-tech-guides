# 🔧 The Ultimate JavaScript Polyfills Guide (A to Z)

> **Author mindset:** Senior Developer preparing you for interviews
> **Goal:** Understand every polyfill deeply — what it does, why it exists, and how to write it from scratch.

---

## 📌 What is a Polyfill?

A **polyfill** is a piece of code (usually JavaScript) that provides **modern functionality** on **older browsers** that do not natively support it.

**Think of it like this:** Your old phone doesn't have a feature. A polyfill is like installing a custom app that gives you that exact feature.

```js
// Example: Array.includes() doesn't exist in IE11
// A polyfill would ADD this method so old browsers can use it too
```

---

## 📋 Table of Contents

| # | Polyfill | Interview Important? |
|---|---------|---------------------|
| 1 | [Array.map()](#1-arraymap) | ⭐⭐⭐ MUST KNOW |
| 2 | [Array.filter()](#2-arrayfilter) | ⭐⭐⭐ MUST KNOW |
| 3 | [Array.reduce()](#3-arrayreduce) | ⭐⭐⭐ MUST KNOW |
| 4 | [Array.forEach()](#4-arrayforeach) | ⭐⭐ IMPORTANT |
| 5 | [Array.find()](#5-arrayfind) | ⭐⭐ IMPORTANT |
| 6 | [Array.findIndex()](#6-arrayfindindex) | ⭐ GOOD TO KNOW |
| 7 | [Array.some()](#7-arraysome) | ⭐ GOOD TO KNOW |
| 8 | [Array.every()](#8-arrayevery) | ⭐ GOOD TO KNOW |
| 9 | [Array.includes()](#9-arrayincludes) | ⭐ GOOD TO KNOW |
| 10 | [Array.flat()](#10-arrayflat) | ⭐⭐ IMPORTANT |
| 11 | [Array.flatMap()](#11-arrayflatmap) | ⭐ GOOD TO KNOW |
| 12 | [Array.from()](#12-arrayfrom) | ⭐⭐ IMPORTANT |
| 13 | [Array.of()](#13-arrayof) | ⭐ GOOD TO KNOW |
| 14 | [Array.isArray()](#14-arrayisarray) | ⭐ GOOD TO KNOW |
| 15 | [Function.bind()](#15-functionbind) | ⭐⭐⭐ MUST KNOW |
| 16 | [Function.call()](#16-functioncall) | ⭐⭐⭐ MUST KNOW |
| 17 | [Function.apply()](#17-functionapply) | ⭐⭐⭐ MUST KNOW |
| 18 | [Object.create()](#18-objectcreate) | ⭐⭐ IMPORTANT |
| 19 | [Object.assign()](#19-objectassign) | ⭐⭐ IMPORTANT |
| 20 | [Object.keys()](#20-objectkeys) | ⭐⭐ IMPORTANT |
| 21 | [Object.values()](#21-objectvalues) | ⭐ GOOD TO KNOW |
| 22 | [Object.entries()](#22-objectentries) | ⭐ GOOD TO KNOW |
| 23 | [Object.freeze() (shallow)](#23-objectfreeze) | ⭐ GOOD TO KNOW |
| 24 | [Object.fromEntries()](#24-objectfromentries) | ⭐ GOOD TO KNOW |
| 25 | [Promise](#25-promise) | ⭐⭐⭐ MUST KNOW |
| 26 | [Promise.all()](#26-promiseall) | ⭐⭐⭐ MUST KNOW |
| 27 | [Promise.allSettled()](#27-promiseallsettled) | ⭐⭐ IMPORTANT |
| 28 | [Promise.race()](#28-promiserace) | ⭐⭐ IMPORTANT |
| 29 | [Promise.any()](#29-promiseany) | ⭐⭐ IMPORTANT |
| 30 | [Promise.resolve() & Promise.reject()](#30-promiseresolve--promisereject) | ⭐ GOOD TO KNOW |
| 31 | [String.includes()](#31-stringincludes) | ⭐ GOOD TO KNOW |
| 32 | [String.startsWith()](#32-stringstartswith) | ⭐ GOOD TO KNOW |
| 33 | [String.endsWith()](#33-stringendswith) | ⭐ GOOD TO KNOW |
| 34 | [String.repeat()](#34-stringrepeat) | ⭐ GOOD TO KNOW |
| 35 | [String.padStart() & String.padEnd()](#35-stringpadstart--stringpadend) | ⭐ GOOD TO KNOW |
| 36 | [String.trim(), trimStart(), trimEnd()](#36-stringtrim-trimstart-trimend) | ⭐ GOOD TO KNOW |
| 37 | [typeof & instanceof Polyfill Concept](#37-typeof--instanceof) | ⭐⭐ IMPORTANT |
| 38 | [new Keyword (Simulating)](#38-new-keyword-simulation) | ⭐⭐⭐ MUST KNOW |
| 39 | [Debounce](#39-debounce) | ⭐⭐⭐ MUST KNOW |
| 40 | [Throttle](#40-throttle) | ⭐⭐⭐ MUST KNOW |
| 41 | [Deep Clone / structuredClone](#41-deep-clone) | ⭐⭐⭐ MUST KNOW |
| 42 | [Shallow Clone](#42-shallow-clone) | ⭐⭐ IMPORTANT |
| 43 | [Currying](#43-currying) | ⭐⭐⭐ MUST KNOW |
| 44 | [Memoization](#44-memoization) | ⭐⭐⭐ MUST KNOW |
| 45 | [Flatten Array (Custom)](#45-flatten-array) | ⭐⭐⭐ MUST KNOW |
| 46 | [Pipe & Compose](#46-pipe--compose) | ⭐⭐ IMPORTANT |
| 47 | [Event Emitter](#47-event-emitter) | ⭐⭐ IMPORTANT |
| 48 | [setInterval using setTimeout](#48-setinterval-using-settimeout) | ⭐⭐ IMPORTANT |
| 49 | [clearAllTimers](#49-clearalltimers) | ⭐ GOOD TO KNOW |
| 50 | [once() Function](#50-once-function) | ⭐⭐ IMPORTANT |
| 51 | [Symbol.iterator / Custom Iterable](#51-custom-iterable) | ⭐⭐ IMPORTANT |
| 52 | [async/await to Promise Conversion](#52-asyncawait-to-promise) | ⭐⭐ IMPORTANT |
| 53 | [JSON.stringify() (simplified)](#53-jsonstringify) | ⭐ GOOD TO KNOW |
| 54 | [JSON.parse() (simplified)](#54-jsonparse) | ⭐ GOOD TO KNOW |
| 55 | [Array.reduceRight()](#55-arrayreduceright) | ⭐ GOOD TO KNOW |
| 56 | [groupBy()](#56-groupby) | ⭐⭐ IMPORTANT |
| 57 | [Promise.finally()](#57-promisefinally) | ⭐ GOOD TO KNOW |
| 58 | [Proxy / Reflect Basics](#58-proxy--reflect) | ⭐ GOOD TO KNOW |
| 59 | [String.replaceAll()](#59-stringreplaceall) | ⭐ GOOD TO KNOW |
| 60 | [at() method](#60-at-method) | ⭐ GOOD TO KNOW |

---

---

# SECTION 1: ARRAY POLYFILLS

---

## 1. Array.map()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
`map()` creates a **new array** by calling a function on **every element** of the original array. It does **NOT** change the original array.

### Simple analogy
Imagine you have a basket of apples. `map()` takes each apple, paints it red, and puts it in a **new basket**. The old basket stays the same.

### How it works internally:
1. It loops through each element of the array
2. Calls your callback function with `(currentElement, index, entireArray)`
3. Whatever your function **returns** gets pushed into a new array
4. Returns the new array at the end

### Native usage:
```js
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8]
```

### ✅ Polyfill:
```js
// We add our custom 'myMap' to Array's prototype
// so every array can use it like: [1,2,3].myMap(...)
Array.prototype.myMap = function (callback, thisArg) {
  // 'this' refers to the array on which myMap is called
  // Example: [1,2,3].myMap(...) → this = [1,2,3]

  // Safety check: callback must be a function
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  // Create a new empty array to store results
  const result = [];

  // Loop through each element of the array
  for (let i = 0; i < this.length; i++) {
    // Check if index actually exists (handles sparse arrays like [1, , 3])
    if (i in this) {
      // Call the callback with:
      //   - thisArg (optional context for 'this' inside callback)
      //   - this[i] = current element
      //   - i = current index
      //   - this = the entire array
      result.push(callback.call(thisArg, this[i], i, this));
    }
  }

  // Return the new array (original is unchanged)
  return result;
};

// --- Testing ---
const nums = [1, 2, 3, 4, 5];
const squared = nums.myMap((num) => num * num);
console.log(squared); // [1, 4, 9, 16, 25]
console.log(nums);    // [1, 2, 3, 4, 5] → original unchanged!
```

### 🔍 Interview follow-up questions:
- **Q:** Does `map()` mutate the original array? **A:** No, it returns a new array.
- **Q:** What happens with sparse arrays? **A:** `map()` skips holes but preserves them.
- **Q:** Difference between `map()` and `forEach()`? **A:** `map()` returns a new array, `forEach()` returns `undefined`.

---

## 2. Array.filter()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
`filter()` creates a **new array** with only those elements that **pass a test** (return `true` from the callback).

### Simple analogy
You have a bag of mixed fruits. `filter()` picks out only the mangoes and puts them in a new bag.

### How it works internally:
1. Loops through each element
2. Calls your callback for each element
3. If callback returns `true` (truthy) → element is added to result
4. If callback returns `false` (falsy) → element is skipped
5. Returns the new filtered array

### Native usage:
```js
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2, 4, 6]
```

### ✅ Polyfill:
```js
Array.prototype.myFilter = function (callback, thisArg) {
  // Safety check
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  const result = [];

  for (let i = 0; i < this.length; i++) {
    // Only process existing indices (skip holes in sparse arrays)
    if (i in this) {
      // Call the callback — if it returns truthy, include this element
      if (callback.call(thisArg, this[i], i, this)) {
        result.push(this[i]);
      }
    }
  }

  return result;
};

// --- Testing ---
const ages = [12, 18, 25, 8, 30, 15];
const adults = ages.myFilter((age) => age >= 18);
console.log(adults); // [18, 25, 30]
```

### Key Difference from map():
- `map()` transforms every element → always returns same-length array
- `filter()` selects elements → may return shorter array

---

## 3. Array.reduce()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS** — The most powerful array method!

### What does it do?
`reduce()` takes an array and **reduces** it down to a **single value** by running a function on each element, carrying forward an **accumulator**.

### Simple analogy
Imagine you're adding up a shopping bill. You start at ₹0 (initial value). For each item, you add its price to the running total. At the end, you have one final number.

### How it works internally:
1. Takes a callback and an optional `initialValue`
2. If `initialValue` is provided → accumulator starts with it
3. If NOT provided → accumulator starts with first element, loop starts from index 1
4. Each iteration: `accumulator = callback(accumulator, currentElement, index, array)`
5. Returns the final accumulator value

### Native usage:
```js
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 15
```

### ✅ Polyfill:
```js
Array.prototype.myReduce = function (callback, initialValue) {
  // Safety check
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  // Edge case: empty array with no initial value
  if (this.length === 0 && initialValue === undefined) {
    throw new TypeError("Reduce of empty array with no initial value");
  }

  // Determine the starting accumulator and starting index
  let accumulator;
  let startIndex;

  if (initialValue !== undefined) {
    // If initialValue is provided, start accumulator with it
    accumulator = initialValue;
    startIndex = 0; // Start from the first element
  } else {
    // If no initialValue, use first element as accumulator
    accumulator = this[0];
    startIndex = 1; // Start from the second element
  }

  // Loop through the array
  for (let i = startIndex; i < this.length; i++) {
    if (i in this) {
      // Update accumulator by calling callback
      // callback receives: (accumulator, currentValue, index, array)
      accumulator = callback(accumulator, this[i], i, this);
    }
  }

  // Return the final accumulated value
  return accumulator;
};

// --- Testing ---
// Sum
const total = [10, 20, 30].myReduce((acc, val) => acc + val, 0);
console.log(total); // 60

// Flatten array
const nested = [[1, 2], [3, 4], [5]];
const flat = nested.myReduce((acc, val) => acc.concat(val), []);
console.log(flat); // [1, 2, 3, 4, 5]

// Count occurrences
const fruits = ["apple", "banana", "apple", "orange", "banana", "apple"];
const count = fruits.myReduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});
console.log(count); // { apple: 3, banana: 2, orange: 1 }
```

### 🔍 Interview follow-up questions:
- **Q:** What happens if no initialValue and array is empty? **A:** TypeError is thrown.
- **Q:** Can reduce return an object/array? **A:** Yes! Reduce can return any type.
- **Q:** Implement `map` using `reduce`? **A:** Shown below:

```js
// map() using reduce() — common interview question!
function mapUsingReduce(arr, fn) {
  return arr.reduce((acc, curr, i, array) => {
    acc.push(fn(curr, i, array));
    return acc;
  }, []);
}
```

---

## 4. Array.forEach()
> ⭐⭐ **IMPORTANT**

### What does it do?
`forEach()` executes a function on each element but **returns nothing** (`undefined`). It's purely for **side effects** (like logging, updating external variables).

### ✅ Polyfill:
```js
Array.prototype.myForEach = function (callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      // Just call the function — we don't collect any return value
      callback.call(thisArg, this[i], i, this);
    }
  }

  // forEach returns nothing (undefined)
};

// --- Testing ---
const colors = ["red", "green", "blue"];
colors.myForEach((color, index) => {
  console.log(`${index}: ${color}`);
});
// Output:
// 0: red
// 1: green
// 2: blue
```

### Key point:
- You **cannot break out** of a `forEach`. Use a `for` loop or `for...of` if you need to `break`.

---

## 5. Array.find()
> ⭐⭐ **IMPORTANT**

### What does it do?
`find()` returns the **first element** that satisfies the test. If no element passes, it returns `undefined`.

### ✅ Polyfill:
```js
Array.prototype.myFind = function (callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      // If callback returns true for this element, return it immediately
      if (callback.call(thisArg, this[i], i, this)) {
        return this[i]; // Return the ELEMENT itself, not the index
      }
    }
  }

  // If nothing found, return undefined
  return undefined;
};

// --- Testing ---
const users = [
  { name: "Shubham", age: 25 },
  { name: "Ravi", age: 30 },
  { name: "Priya", age: 22 },
];
const found = users.myFind((user) => user.age > 24);
console.log(found); // { name: "Shubham", age: 25 }
```

---

## 6. Array.findIndex()
> ⭐ **GOOD TO KNOW**

### What does it do?
Like `find()`, but instead of returning the element, it returns the **index**. Returns `-1` if not found.

### ✅ Polyfill:
```js
Array.prototype.myFindIndex = function (callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      if (callback.call(thisArg, this[i], i, this)) {
        return i; // Return the INDEX, not the element
      }
    }
  }

  return -1; // Not found
};

// --- Testing ---
const numbers = [5, 12, 8, 130, 44];
const index = numbers.myFindIndex((num) => num > 10);
console.log(index); // 1 (because 12 is at index 1)
```

---

## 7. Array.some()
> ⭐ **GOOD TO KNOW**

### What does it do?
Returns `true` if **at least one** element passes the test. Short-circuits on first `true`.

### ✅ Polyfill:
```js
Array.prototype.mySome = function (callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      // As soon as one element passes, return true
      if (callback.call(thisArg, this[i], i, this)) {
        return true;
      }
    }
  }

  // No element passed the test
  return false;
};

// --- Testing ---
const nums = [1, 3, 5, 7, 8];
console.log(nums.mySome((n) => n % 2 === 0)); // true (8 is even)
console.log(nums.mySome((n) => n > 10));       // false
```

---

## 8. Array.every()
> ⭐ **GOOD TO KNOW**

### What does it do?
Returns `true` only if **ALL elements** pass the test. Short-circuits on first `false`.

### ✅ Polyfill:
```js
Array.prototype.myEvery = function (callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      // As soon as one element FAILS, return false
      if (!callback.call(thisArg, this[i], i, this)) {
        return false;
      }
    }
  }

  // All elements passed
  return true;
};

// --- Testing ---
const ages = [18, 21, 25, 30];
console.log(ages.myEvery((age) => age >= 18)); // true
console.log(ages.myEvery((age) => age >= 21)); // false (18 fails)
```

### Key difference: `some()` vs `every()`
| | `some()` | `every()` |
|---|---------|-----------|
| Returns `true` when | At least ONE passes | ALL pass |
| Returns `false` when | NONE pass | At least ONE fails |
| Short-circuits on | First `true` | First `false` |

---

## 9. Array.includes()
> ⭐ **GOOD TO KNOW**

### What does it do?
Checks if an array **contains** a specific value. Returns `true` or `false`. Uses **strict equality** (`===`) but also handles `NaN`.

### ✅ Polyfill:
```js
Array.prototype.myIncludes = function (searchElement, fromIndex) {
  // Handle negative fromIndex
  let start = fromIndex || 0;
  if (start < 0) {
    start = Math.max(this.length + start, 0);
  }

  for (let i = start; i < this.length; i++) {
    // Special handling for NaN (NaN !== NaN in JS, but includes should find NaN)
    if (this[i] === searchElement || (Number.isNaN(this[i]) && Number.isNaN(searchElement))) {
      return true;
    }
  }

  return false;
};

// --- Testing ---
console.log([1, 2, 3].myIncludes(2));     // true
console.log([1, 2, 3].myIncludes(4));     // false
console.log([1, NaN, 3].myIncludes(NaN)); // true (handles NaN!)
```

---

## 10. Array.flat()
> ⭐⭐ **IMPORTANT**

### What does it do?
Flattens nested arrays to a specified depth (default: 1). Creates a new array.

### ✅ Polyfill:
```js
Array.prototype.myFlat = function (depth = 1) {
  // Base case: if depth is 0, return a shallow copy
  if (depth === 0) return this.slice();

  const result = [];

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      if (Array.isArray(this[i]) && depth > 0) {
        // If current element is an array AND we still have depth left,
        // recursively flatten it with (depth - 1) and spread into result
        const flattened = this[i].myFlat(depth - 1);
        result.push(...flattened);
      } else {
        // Not an array or depth exhausted — just add the element
        result.push(this[i]);
      }
    }
  }

  return result;
};

// --- Testing ---
const nested = [1, [2, [3, [4, [5]]]]];
console.log(nested.myFlat());          // [1, 2, [3, [4, [5]]]]  — depth 1
console.log(nested.myFlat(2));         // [1, 2, 3, [4, [5]]]    — depth 2
console.log(nested.myFlat(Infinity));  // [1, 2, 3, 4, 5]        — fully flat
```

---

## 11. Array.flatMap()
> ⭐ **GOOD TO KNOW**

### What does it do?
First maps each element using a function, then flattens the result by **one level**. It's like doing `.map().flat(1)` but more efficient.

### ✅ Polyfill:
```js
Array.prototype.myFlatMap = function (callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  const result = [];

  for (let i = 0; i < this.length; i++) {
    if (i in this) {
      const mapped = callback.call(thisArg, this[i], i, this);
      // If the result is an array, spread it (flatten one level)
      if (Array.isArray(mapped)) {
        result.push(...mapped);
      } else {
        result.push(mapped);
      }
    }
  }

  return result;
};

// --- Testing ---
const sentences = ["Hello World", "Foo Bar"];
const words = sentences.myFlatMap((s) => s.split(" "));
console.log(words); // ["Hello", "World", "Foo", "Bar"]
```

---

## 12. Array.from()
> ⭐⭐ **IMPORTANT**

### What does it do?
Creates a new array from an **array-like** or **iterable** object (like `NodeList`, `arguments`, `Set`, `Map`, strings).

### ✅ Polyfill:
```js
Array.myFrom = function (arrayLike, mapFn, thisArg) {
  // Safety check
  if (arrayLike == null) {
    throw new TypeError("Array.from requires an array-like object");
  }

  // If mapFn is provided, it must be a function
  if (mapFn !== undefined && typeof mapFn !== "function") {
    throw new TypeError("mapFn must be a function");
  }

  const result = [];
  const len = arrayLike.length >>> 0; // Convert to unsigned integer

  for (let i = 0; i < len; i++) {
    const element = arrayLike[i];
    // Apply mapping function if provided
    result.push(mapFn ? mapFn.call(thisArg, element, i) : element);
  }

  return result;
};

// --- Testing ---
// From a string (string is iterable)
console.log(Array.myFrom("hello")); // ["h", "e", "l", "l", "o"]

// From array-like object
console.log(Array.myFrom({ 0: "a", 1: "b", 2: "c", length: 3 })); // ["a", "b", "c"]

// With mapping function
console.log(Array.myFrom([1, 2, 3], (x) => x * 2)); // [2, 4, 6]
```

---

## 13. Array.of()
> ⭐ **GOOD TO KNOW**

### What does it do?
Creates a new array from a variable number of arguments, regardless of number or type.

```js
// Problem with new Array():
new Array(3);    // Creates [empty × 3] — NOT [3]!
Array.of(3);     // Creates [3] — this is what we want
```

### ✅ Polyfill:
```js
Array.myOf = function (...args) {
  // Simply return a new array containing all arguments
  return args.slice();
};

// --- Testing ---
console.log(Array.myOf(1, 2, 3));   // [1, 2, 3]
console.log(Array.myOf(7));          // [7] — not an empty array of length 7
console.log(Array.myOf(undefined));  // [undefined]
```

---

## 14. Array.isArray()
> ⭐ **GOOD TO KNOW**

### What does it do?
Determines if a value is an Array. More reliable than `typeof` (which returns `"object"` for arrays).

### ✅ Polyfill:
```js
Array.myIsArray = function (value) {
  // The most reliable way to check if something is an array
  // Object.prototype.toString.call([]) returns "[object Array]"
  return Object.prototype.toString.call(value) === "[object Array]";
};

// --- Testing ---
console.log(Array.myIsArray([1, 2, 3]));     // true
console.log(Array.myIsArray("hello"));        // false
console.log(Array.myIsArray({ length: 3 }));  // false
console.log(Array.myIsArray(new Array()));    // true
```

---

---

# SECTION 2: FUNCTION POLYFILLS

---

## 15. Function.bind()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS** — Asked in almost every interview!

### What does it do?
`bind()` creates a **new function** that, when called, has its `this` set to a specific value. It can also **preset** some arguments (partial application).

### Simple analogy:
Think of `bind()` like giving someone a remote control that's permanently connected to a specific TV. No matter who presses the button, it always controls that one TV.

### How it works internally:
1. Takes a `thisArg` (what `this` should be) and optional preset arguments
2. Returns a NEW function (doesn't call the original)
3. When the new function is called, it calls the original with the bound `this` and combined arguments

### Native usage:
```js
const person = {
  name: "Shubham",
  greet: function (greeting) {
    return `${greeting}, I'm ${this.name}`;
  },
};

const greetShubham = person.greet.bind(person, "Hello");
console.log(greetShubham()); // "Hello, I'm Shubham"
```

### ✅ Polyfill:
```js
Function.prototype.myBind = function (thisArg, ...boundArgs) {
  // 'this' here is the function on which myBind is called
  // Example: greet.myBind(person) → this = greet
  const originalFunction = this;

  // Safety check: bind can only be called on functions
  if (typeof originalFunction !== "function") {
    throw new TypeError("Bind must be called on a function");
  }

  // Return a new function
  return function (...calledArgs) {
    // When this new function is called:
    // 1. Use the bound 'this' context (thisArg)
    // 2. Combine pre-set args (boundArgs) with new args (calledArgs)
    return originalFunction.apply(thisArg, [...boundArgs, ...calledArgs]);
  };
};

// --- Testing ---
const person = {
  name: "Shubham",
};

function introduce(greeting, punctuation) {
  return `${greeting}, I'm ${this.name}${punctuation}`;
}

// Bind with preset first argument
const introShubham = introduce.myBind(person, "Hey");
console.log(introShubham("!")); // "Hey, I'm Shubham!"
console.log(introShubham(".")); // "Hey, I'm Shubham."
```

### 🔍 Interview follow-up:
- **Q:** Does `bind()` call the function? **A:** No! It returns a new function.
- **Q:** Difference between `call`, `apply`, `bind`? **A:** `call` and `apply` invoke immediately. `bind` returns a new function.
- **Q:** Can you bind an already bound function? **A:** Yes, but the first bind's `this` wins.

---

## 16. Function.call()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
`call()` invokes a function **immediately** with a specified `this` value and arguments passed **one by one**.

### ✅ Polyfill:
```js
Function.prototype.myCall = function (thisArg, ...args) {
  // If thisArg is null/undefined, default to globalThis (window in browsers)
  thisArg = thisArg != null ? Object(thisArg) : globalThis;

  // Create a unique key to avoid overwriting existing properties
  const uniqueKey = Symbol("fn");

  // Attach the function to thisArg temporarily
  // This way, when we call thisArg[uniqueKey](), 'this' inside the function = thisArg
  thisArg[uniqueKey] = this;

  // Call the function with the provided arguments
  const result = thisArg[uniqueKey](...args);

  // Clean up — remove the temporary property
  delete thisArg[uniqueKey];

  // Return whatever the function returned
  return result;
};

// --- Testing ---
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

const person = { name: "Shubham" };
console.log(greet.myCall(person, "Hello", "!")); // "Hello, Shubham!"
```

### 🔍 How does this trick work?
When you do `obj.method()`, the `this` inside `method` points to `obj`. So by temporarily attaching our function to the target object and calling it, `this` naturally becomes `thisArg`!

---

## 17. Function.apply()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
Same as `call()`, but arguments are passed as an **array** instead of one by one.

### Memory trick: **A**pply = **A**rray, **C**all = **C**ommas

### ✅ Polyfill:
```js
Function.prototype.myApply = function (thisArg, argsArray) {
  // Default thisArg
  thisArg = thisArg != null ? Object(thisArg) : globalThis;

  // Default argsArray to empty array
  argsArray = argsArray || [];

  const uniqueKey = Symbol("fn");
  thisArg[uniqueKey] = this;

  // Spread the array as individual arguments
  const result = thisArg[uniqueKey](...argsArray);

  delete thisArg[uniqueKey];
  return result;
};

// --- Testing ---
function sum(a, b, c) {
  return `${this.prefix}: ${a + b + c}`;
}

const context = { prefix: "Total" };
console.log(sum.myApply(context, [10, 20, 30])); // "Total: 60"
```

### Quick comparison: call vs apply vs bind

| Method | Invokes immediately? | Arguments format | Returns |
|--------|---------------------|------------------|---------|
| `call()` | ✅ Yes | `fn.call(this, a, b, c)` | Function result |
| `apply()` | ✅ Yes | `fn.apply(this, [a, b, c])` | Function result |
| `bind()` | ❌ No | `fn.bind(this, a, b)` | New function |

---

---

# SECTION 3: OBJECT POLYFILLS

---

## 18. Object.create()
> ⭐⭐ **IMPORTANT**

### What does it do?
Creates a **new object** with a specified prototype. This is the foundation of **prototypal inheritance** in JavaScript.

### Simple analogy:
`Object.create(parent)` creates a child who **inherits** everything from the parent. The child can have its own properties too.

### ✅ Polyfill:
```js
Object.myCreate = function (proto, propertiesObject) {
  // proto must be an object or null
  if (typeof proto !== "object" && proto !== null) {
    throw new TypeError("Object prototype may only be an Object or null");
  }

  // Create a temporary constructor function
  function TempConstructor() {}

  // Set its prototype to the desired proto
  TempConstructor.prototype = proto;

  // Create a new instance — this instance's __proto__ will point to proto
  const newObj = new TempConstructor();

  // If propertiesObject is provided, define those properties
  if (propertiesObject !== undefined) {
    Object.defineProperties(newObj, propertiesObject);
  }

  // Handle null prototype (no prototype chain at all)
  if (proto === null) {
    newObj.__proto__ = null;
  }

  return newObj;
};

// --- Testing ---
const animal = {
  speak() {
    return `${this.name} makes a sound`;
  },
};

const dog = Object.myCreate(animal);
dog.name = "Buddy";
console.log(dog.speak()); // "Buddy makes a sound"

// dog doesn't have speak() directly, it inherits from animal
console.log(dog.hasOwnProperty("speak")); // false
console.log(dog.hasOwnProperty("name"));  // true
```

---

## 19. Object.assign()
> ⭐⭐ **IMPORTANT**

### What does it do?
Copies all **own enumerable** properties from one or more source objects to a target object. Returns the modified target. It does a **shallow copy**.

### ✅ Polyfill:
```js
Object.myAssign = function (target, ...sources) {
  // target cannot be null or undefined
  if (target == null) {
    throw new TypeError("Cannot convert undefined or null to object");
  }

  // Convert target to object (handles primitives like numbers)
  const result = Object(target);

  // Loop through each source object
  for (const source of sources) {
    // Skip null/undefined sources
    if (source != null) {
      // Copy all own enumerable properties
      for (const key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
          result[key] = source[key];
        }
      }

      // Also handle Symbol properties
      const symbols = Object.getOwnPropertySymbols(source);
      for (const sym of symbols) {
        if (Object.prototype.propertyIsEnumerable.call(source, sym)) {
          result[sym] = source[sym];
        }
      }
    }
  }

  return result;
};

// --- Testing ---
const target = { a: 1 };
const source1 = { b: 2, c: 3 };
const source2 = { c: 4, d: 5 }; // c will be overwritten

const result = Object.myAssign(target, source1, source2);
console.log(result); // { a: 1, b: 2, c: 4, d: 5 }
console.log(target); // { a: 1, b: 2, c: 4, d: 5 } — target is MUTATED!
```

### ⚠️ Important: Object.assign does SHALLOW copy!
```js
const original = { nested: { value: 42 } };
const copy = Object.assign({}, original);
copy.nested.value = 100;
console.log(original.nested.value); // 100 — original is also changed!
```

---

## 20. Object.keys()
> ⭐⭐ **IMPORTANT**

### What does it do?
Returns an array of an object's **own enumerable** property names (strings only).

### ✅ Polyfill:
```js
Object.myKeys = function (obj) {
  if (obj == null) {
    throw new TypeError("Cannot convert undefined or null to object");
  }

  const result = [];
  for (const key in obj) {
    // Only own properties, not inherited ones
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      result.push(key);
    }
  }
  return result;
};

// --- Testing ---
const person = { name: "Shubham", age: 25, city: "Mumbai" };
console.log(Object.myKeys(person)); // ["name", "age", "city"]
```

---

## 21. Object.values()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
Object.myValues = function (obj) {
  if (obj == null) {
    throw new TypeError("Cannot convert undefined or null to object");
  }

  const result = [];
  for (const key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      result.push(obj[key]); // Push the VALUE, not the key
    }
  }
  return result;
};

// --- Testing ---
const scores = { math: 90, science: 85, english: 88 };
console.log(Object.myValues(scores)); // [90, 85, 88]
```

---

## 22. Object.entries()
> ⭐ **GOOD TO KNOW**

### What does it do?
Returns an array of `[key, value]` pairs.

### ✅ Polyfill:
```js
Object.myEntries = function (obj) {
  if (obj == null) {
    throw new TypeError("Cannot convert undefined or null to object");
  }

  const result = [];
  for (const key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      result.push([key, obj[key]]); // Push [key, value] pair
    }
  }
  return result;
};

// --- Testing ---
const car = { brand: "Toyota", year: 2024 };
console.log(Object.myEntries(car));
// [["brand", "Toyota"], ["year", 2024]]
```

---

## 23. Object.freeze()
> ⭐ **GOOD TO KNOW**

### What does it do?
Freezes an object — you can't add, remove, or modify its properties. But it's **shallow** — nested objects are NOT frozen.

### ✅ Polyfill (Shallow):
```js
Object.myFreeze = function (obj) {
  if (typeof obj !== "object" || obj === null) {
    return obj; // Primitives are already immutable
  }

  // Get all own properties (including non-enumerable)
  const propNames = Object.getOwnPropertyNames(obj);

  for (const name of propNames) {
    // Make each property non-writable and non-configurable
    const desc = Object.getOwnPropertyDescriptor(obj, name);
    if (desc && ("value" in desc)) {
      Object.defineProperty(obj, name, {
        writable: false,
        configurable: false,
      });
    }
  }

  // Prevent adding/removing properties
  Object.preventExtensions(obj);

  return obj;
};

// --- Testing ---
const config = { apiUrl: "https://api.example.com", timeout: 5000 };
Object.myFreeze(config);
config.apiUrl = "hacked"; // Silently fails (or throws in strict mode)
config.newProp = "test";  // Silently fails
console.log(config.apiUrl); // "https://api.example.com" — unchanged!
```

### 🧊 Deep Freeze (Bonus):
```js
function deepFreeze(obj) {
  Object.freeze(obj);
  for (const key of Object.keys(obj)) {
    if (typeof obj[key] === "object" && obj[key] !== null && !Object.isFrozen(obj[key])) {
      deepFreeze(obj[key]); // Recursively freeze nested objects
    }
  }
  return obj;
}
```

---

## 24. Object.fromEntries()
> ⭐ **GOOD TO KNOW**

### What does it do?
The reverse of `Object.entries()` — takes an array of `[key, value]` pairs and creates an object.

### ✅ Polyfill:
```js
Object.myFromEntries = function (entries) {
  const result = {};
  for (const [key, value] of entries) {
    result[key] = value;
  }
  return result;
};

// --- Testing ---
const entries = [["name", "Shubham"], ["age", 25]];
console.log(Object.myFromEntries(entries)); // { name: "Shubham", age: 25 }

// Useful: Transform object values
const prices = { apple: 100, banana: 50, mango: 200 };
const discounted = Object.myFromEntries(
  Object.entries(prices).map(([fruit, price]) => [fruit, price * 0.9])
);
console.log(discounted); // { apple: 90, banana: 45, mango: 180 }
```

---

---

# SECTION 4: PROMISE POLYFILLS

---

## 25. Promise
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS** — The foundation of async JavaScript!

### What does it do?
A Promise represents a value that may be available **now**, or in the **future**, or **never**. It has three states:
- **pending** → initial state
- **fulfilled** → operation completed successfully
- **rejected** → operation failed

### How it works:
1. You create a Promise with an `executor` function that receives `resolve` and `reject`
2. When async work succeeds → call `resolve(value)`
3. When async work fails → call `reject(error)`
4. Use `.then()` to handle success, `.catch()` to handle errors

### ✅ Polyfill (Simplified but interview-worthy):
```js
class MyPromise {
  constructor(executor) {
    this.state = "pending";     // "pending" | "fulfilled" | "rejected"
    this.value = undefined;      // Resolved value or rejection reason
    this.callbacks = [];         // Queue of then/catch handlers

    // Resolve function — called when async work succeeds
    const resolve = (value) => {
      if (this.state !== "pending") return; // Can only transition once!
      this.state = "fulfilled";
      this.value = value;
      // Execute all queued success handlers
      this.callbacks.forEach((cb) => cb.onFulfilled(value));
    };

    // Reject function — called when async work fails
    const reject = (reason) => {
      if (this.state !== "pending") return;
      this.state = "rejected";
      this.value = reason;
      // Execute all queued error handlers
      this.callbacks.forEach((cb) => cb.onRejected(reason));
    };

    // Execute the executor function immediately
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error); // If executor throws, reject the promise
    }
  }

  then(onFulfilled, onRejected) {
    // then() returns a NEW promise (this enables chaining!)
    return new MyPromise((resolve, reject) => {
      const handleCallback = () => {
        try {
          if (this.state === "fulfilled") {
            // If onFulfilled is a function, call it with the value
            if (typeof onFulfilled === "function") {
              const result = onFulfilled(this.value);
              // If result is a promise, wait for it
              if (result instanceof MyPromise) {
                result.then(resolve, reject);
              } else {
                resolve(result);
              }
            } else {
              // If no onFulfilled handler, pass value through
              resolve(this.value);
            }
          } else if (this.state === "rejected") {
            if (typeof onRejected === "function") {
              const result = onRejected(this.value);
              if (result instanceof MyPromise) {
                result.then(resolve, reject);
              } else {
                resolve(result); // Note: resolve, not reject!
              }
            } else {
              reject(this.value);
            }
          }
        } catch (error) {
          reject(error);
        }
      };

      if (this.state === "pending") {
        // If promise is still pending, queue the handlers
        this.callbacks.push({
          onFulfilled: () => handleCallback(),
          onRejected: () => handleCallback(),
        });
      } else {
        // If already settled, execute asynchronously
        queueMicrotask(() => handleCallback());
      }
    });
  }

  catch(onRejected) {
    // catch is just a shorthand for then(null, onRejected)
    return this.then(null, onRejected);
  }

  finally(onFinally) {
    return this.then(
      (value) => {
        onFinally();
        return value; // Preserve the value
      },
      (reason) => {
        onFinally();
        throw reason; // Preserve the rejection
      }
    );
  }
}

// --- Testing ---
const p = new MyPromise((resolve, reject) => {
  setTimeout(() => resolve("Success!"), 1000);
});

p.then((value) => {
  console.log(value); // "Success!" (after 1 second)
  return "Next value";
})
  .then((value) => {
    console.log(value); // "Next value"
  })
  .catch((err) => {
    console.error(err);
  });
```

### 🔍 Key interview points:
- Promise states are **irreversible**: once fulfilled or rejected, it stays that way
- `.then()` always returns a **new** promise (enables chaining)
- Executor runs **synchronously**, but then/catch handlers run **asynchronously**

---

## 26. Promise.all()
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
Takes an array of promises and returns a single promise that:
- **Resolves** when ALL promises resolve (with array of results)
- **Rejects** as soon as ANY ONE promise rejects (fast-fail)

### Simple analogy:
Ordering 5 items online. `Promise.all()` says "I'll mark the order complete only when ALL 5 items are delivered. If even one fails, the whole order fails."

### ✅ Polyfill:
```js
Promise.myAll = function (promises) {
  return new Promise((resolve, reject) => {
    // Handle non-array or empty array
    const promisesArray = Array.from(promises);

    if (promisesArray.length === 0) {
      resolve([]);
      return;
    }

    const results = new Array(promisesArray.length);
    let completedCount = 0;

    promisesArray.forEach((promise, index) => {
      // Wrap in Promise.resolve() to handle non-promise values too
      Promise.resolve(promise)
        .then((value) => {
          results[index] = value; // Store result at correct index (ORDER matters!)
          completedCount++;

          // If ALL promises have resolved, resolve the main promise
          if (completedCount === promisesArray.length) {
            resolve(results);
          }
        })
        .catch((error) => {
          // If ANY promise rejects, immediately reject the whole thing
          reject(error);
        });
    });
  });
};

// --- Testing ---
const p1 = Promise.resolve(1);
const p2 = new Promise((res) => setTimeout(() => res(2), 100));
const p3 = Promise.resolve(3);

Promise.myAll([p1, p2, p3]).then((results) => {
  console.log(results); // [1, 2, 3] — in original order!
});

// With rejection
Promise.myAll([
  Promise.resolve("ok"),
  Promise.reject("failed!"),
  Promise.resolve("ok"),
]).catch((err) => {
  console.log(err); // "failed!" — rejects immediately
});
```

### 🔍 Common interview questions:
- **Q:** What's the order of results? **A:** Same as input order (NOT completion order).
- **Q:** What if one of the values is not a promise? **A:** It's auto-wrapped with `Promise.resolve()`.

---

## 27. Promise.allSettled()
> ⭐⭐ **IMPORTANT**

### What does it do?
Like `Promise.all()`, but it **never rejects**. It waits for ALL promises to settle (either fulfill or reject) and returns their results.

### When to use:
When you want results of ALL promises regardless of success/failure (e.g., making multiple API calls and showing partial results).

### ✅ Polyfill:
```js
Promise.myAllSettled = function (promises) {
  return new Promise((resolve) => {
    const promisesArray = Array.from(promises);

    if (promisesArray.length === 0) {
      resolve([]);
      return;
    }

    const results = new Array(promisesArray.length);
    let settledCount = 0;

    promisesArray.forEach((promise, index) => {
      Promise.resolve(promise)
        .then((value) => {
          // On success: store status and value
          results[index] = { status: "fulfilled", value };
        })
        .catch((reason) => {
          // On failure: store status and reason (NOT rejecting the outer promise!)
          results[index] = { status: "rejected", reason };
        })
        .finally(() => {
          settledCount++;
          if (settledCount === promisesArray.length) {
            resolve(results); // Always resolves!
          }
        });
    });
  });
};

// --- Testing ---
Promise.myAllSettled([
  Promise.resolve("Success"),
  Promise.reject("Error"),
  Promise.resolve(42),
]).then((results) => {
  console.log(results);
  // [
  //   { status: "fulfilled", value: "Success" },
  //   { status: "rejected", reason: "Error" },
  //   { status: "fulfilled", value: 42 }
  // ]
});
```

---

## 28. Promise.race()
> ⭐⭐ **IMPORTANT**

### What does it do?
Returns a promise that resolves/rejects as soon as the **first** promise settles (whichever finishes first wins).

### Simple analogy:
A race between runners. The first one to cross the finish line wins — doesn't matter if they won or tripped (resolved or rejected).

### ✅ Polyfill:
```js
Promise.myRace = function (promises) {
  return new Promise((resolve, reject) => {
    const promisesArray = Array.from(promises);

    promisesArray.forEach((promise) => {
      // The first promise to settle wins — it calls resolve or reject,
      // and since a promise can only settle once, all other results are ignored
      Promise.resolve(promise).then(resolve, reject);
    });
  });
};

// --- Testing ---
const slow = new Promise((res) => setTimeout(() => res("slow"), 2000));
const fast = new Promise((res) => setTimeout(() => res("fast"), 100));

Promise.myRace([slow, fast]).then((result) => {
  console.log(result); // "fast" — the first one wins!
});
```

### 💡 Common use case: Timeout pattern
```js
function fetchWithTimeout(url, timeout = 5000) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Timeout!")), timeout)
    ),
  ]);
}
```

---

## 29. Promise.any()
> ⭐⭐ **IMPORTANT**

### What does it do?
Returns the **first fulfilled** promise. Ignores rejections unless ALL promises reject (then throws `AggregateError`).

### Difference from `race()`:
- `race()` → first to **settle** (fulfill OR reject)
- `any()` → first to **fulfill** (ignores rejections)

### ✅ Polyfill:
```js
Promise.myAny = function (promises) {
  return new Promise((resolve, reject) => {
    const promisesArray = Array.from(promises);

    if (promisesArray.length === 0) {
      reject(new AggregateError([], "All promises were rejected"));
      return;
    }

    const errors = [];
    let rejectedCount = 0;

    promisesArray.forEach((promise, index) => {
      Promise.resolve(promise)
        .then((value) => {
          // First fulfilled promise wins!
          resolve(value);
        })
        .catch((error) => {
          errors[index] = error;
          rejectedCount++;

          // Only reject if ALL promises have rejected
          if (rejectedCount === promisesArray.length) {
            reject(new AggregateError(errors, "All promises were rejected"));
          }
        });
    });
  });
};

// --- Testing ---
Promise.myAny([
  Promise.reject("Error 1"),
  Promise.reject("Error 2"),
  Promise.resolve("Success!"),
]).then((value) => {
  console.log(value); // "Success!" — ignores the rejections
});

// When all reject:
Promise.myAny([
  Promise.reject("Error 1"),
  Promise.reject("Error 2"),
]).catch((err) => {
  console.log(err.errors); // ["Error 1", "Error 2"]
});
```

---

## 30. Promise.resolve() & Promise.reject()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
Promise.myResolve = function (value) {
  // If value is already a promise, return it as-is
  if (value instanceof Promise) {
    return value;
  }
  // Otherwise, wrap it in a resolved promise
  return new Promise((resolve) => resolve(value));
};

Promise.myReject = function (reason) {
  // Always return a new rejected promise (even if reason is a promise)
  return new Promise((_, reject) => reject(reason));
};

// --- Testing ---
Promise.myResolve(42).then((v) => console.log(v)); // 42
Promise.myReject("Oops").catch((e) => console.log(e)); // "Oops"
```

---

---

# SECTION 5: STRING POLYFILLS

---

## 31. String.includes()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
String.prototype.myIncludes = function (searchString, position) {
  // Use indexOf internally — if found, index >= 0
  return this.indexOf(searchString, position || 0) !== -1;
};

// --- Testing ---
console.log("Hello World".myIncludes("World")); // true
console.log("Hello World".myIncludes("world")); // false (case-sensitive!)
console.log("Hello World".myIncludes("Hello", 1)); // false (starts searching from index 1)
```

---

## 32. String.startsWith()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
String.prototype.myStartsWith = function (searchString, position) {
  // Start position (default: 0)
  const pos = position || 0;
  // Check if the substring at the start matches
  return this.substring(pos, pos + searchString.length) === searchString;
};

// --- Testing ---
console.log("JavaScript".myStartsWith("Java"));    // true
console.log("JavaScript".myStartsWith("Script"));  // false
console.log("JavaScript".myStartsWith("Script", 4)); // true (starts from index 4)
```

---

## 33. String.endsWith()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
String.prototype.myEndsWith = function (searchString, length) {
  // If length not provided, use full string length
  const len = length !== undefined ? length : this.length;
  // Check from the end
  const end = len;
  const start = end - searchString.length;
  return this.substring(start, end) === searchString;
};

// --- Testing ---
console.log("Hello World".myEndsWith("World")); // true
console.log("Hello World".myEndsWith("Hello")); // false
console.log("Hello World".myEndsWith("Hello", 5)); // true (considers only first 5 chars)
```

---

## 34. String.repeat()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
String.prototype.myRepeat = function (count) {
  // Validate count
  if (count < 0 || count === Infinity) {
    throw new RangeError("Invalid count value");
  }
  count = Math.floor(count); // Convert to integer

  let result = "";
  for (let i = 0; i < count; i++) {
    result += this;
  }
  return result;
};

// --- Testing ---
console.log("Ha".myRepeat(3));  // "HaHaHa"
console.log("abc".myRepeat(0)); // ""
```

---

## 35. String.padStart() & String.padEnd()
> ⭐ **GOOD TO KNOW**

### What do they do?
Pad a string to a target length with a specified character (or space by default).

### ✅ Polyfill:
```js
String.prototype.myPadStart = function (targetLength, padString) {
  // If string is already long enough, return it
  if (this.length >= targetLength) return String(this);

  padString = padString !== undefined ? String(padString) : " ";
  if (padString.length === 0) return String(this);

  // Calculate how many characters we need to add
  const padNeeded = targetLength - this.length;

  // Repeat padString enough times, then slice to exact length needed
  let padding = padString.repeat(Math.ceil(padNeeded / padString.length));
  padding = padding.slice(0, padNeeded);

  return padding + this; // Add padding at the START
};

String.prototype.myPadEnd = function (targetLength, padString) {
  if (this.length >= targetLength) return String(this);

  padString = padString !== undefined ? String(padString) : " ";
  if (padString.length === 0) return String(this);

  const padNeeded = targetLength - this.length;
  let padding = padString.repeat(Math.ceil(padNeeded / padString.length));
  padding = padding.slice(0, padNeeded);

  return this + padding; // Add padding at the END
};

// --- Testing ---
console.log("5".myPadStart(3, "0"));     // "005"
console.log("hi".myPadEnd(10, "."));      // "hi........"
console.log("42".myPadStart(5));          // "   42" (padded with spaces)
```

### 💡 Common use case:
```js
// Formatting time
const hours = "9".padStart(2, "0");   // "09"
const minutes = "5".padStart(2, "0"); // "05"
console.log(`${hours}:${minutes}`);   // "09:05"
```

---

## 36. String.trim(), trimStart(), trimEnd()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
// trim() — removes whitespace from BOTH ends
String.prototype.myTrim = function () {
  // Regex: ^\\s+ matches whitespace at start, \\s+$ matches at end
  return this.replace(/^\s+|\s+$/g, "");
};

// trimStart() — removes whitespace from the START only
String.prototype.myTrimStart = function () {
  return this.replace(/^\s+/, "");
};

// trimEnd() — removes whitespace from the END only
String.prototype.myTrimEnd = function () {
  return this.replace(/\s+$/, "");
};

// --- Testing ---
const str = "   Hello World   ";
console.log(str.myTrim());      // "Hello World"
console.log(str.myTrimStart()); // "Hello World   "
console.log(str.myTrimEnd());   // "   Hello World"
```

---

---

# SECTION 6: ADVANCED & UTILITY POLYFILLS

---

## 37. typeof & instanceof
> ⭐⭐ **IMPORTANT** — Understanding, not polyfill

### The problem with `typeof`:
```js
typeof [];           // "object" — not helpful!
typeof null;         // "object" — famous JS bug
typeof function(){}; // "function" — correct
typeof {};           // "object"
typeof 42;           // "number"
```

### Better type checking utility:
```js
function getType(value) {
  // This is the most reliable way to check types in JavaScript
  // Object.prototype.toString gives us "[object Type]"
  if (value === null) return "null";
  if (value === undefined) return "undefined";

  // Extract the type from "[object Type]"
  const type = Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
  return type;
}

// --- Testing ---
console.log(getType(42));            // "number"
console.log(getType("hello"));       // "string"
console.log(getType(true));          // "boolean"
console.log(getType(null));          // "null"
console.log(getType(undefined));     // "undefined"
console.log(getType([]));            // "array"
console.log(getType({}));            // "object"
console.log(getType(new Date()));    // "date"
console.log(getType(/regex/));       // "regexp"
console.log(getType(new Map()));     // "map"
console.log(getType(new Set()));     // "set"
```

### Custom `instanceof` polyfill:
```js
function myInstanceOf(obj, Constructor) {
  // Walk up the prototype chain
  let proto = Object.getPrototypeOf(obj);

  while (proto !== null) {
    if (proto === Constructor.prototype) {
      return true; // Found it in the chain!
    }
    proto = Object.getPrototypeOf(proto); // Move up
  }

  return false; // Reached the end, not found
}

// --- Testing ---
class Animal {}
class Dog extends Animal {}
const buddy = new Dog();

console.log(myInstanceOf(buddy, Dog));    // true
console.log(myInstanceOf(buddy, Animal)); // true (because Dog extends Animal)
console.log(myInstanceOf(buddy, Array));  // false
```

---

## 38. new Keyword Simulation
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does `new` do behind the scenes?
1. Creates a new empty object
2. Sets the prototype of the new object to the constructor's prototype
3. Calls the constructor with `this` = new object
4. Returns the object (unless constructor returns a different object)

### ✅ Polyfill:
```js
function myNew(Constructor, ...args) {
  // Step 1: Create a new empty object
  // Step 2: Link its prototype to Constructor.prototype
  const obj = Object.create(Constructor.prototype);

  // Step 3: Call the constructor with 'this' pointing to the new object
  const result = Constructor.apply(obj, args);

  // Step 4: If constructor returns an object, use that. Otherwise, use our created object
  // (This handles edge cases where constructor explicitly returns an object)
  return result instanceof Object ? result : obj;
}

// --- Testing ---
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function () {
  return `Hi, I'm ${this.name}, ${this.age} years old`;
};

// Using our custom 'new'
const person = myNew(Person, "Shubham", 25);
console.log(person.name);    // "Shubham"
console.log(person.age);     // 25
console.log(person.greet()); // "Hi, I'm Shubham, 25 years old"
console.log(person instanceof Person); // true!
```

---

## 39. Debounce
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS** — Asked in 90% of frontend interviews!

### What does it do?
**Debounce** delays the function execution until the user **stops** performing the action for a specified time. If the action happens again within the delay, the timer resets.

### Simple analogy:
Elevator doors! They wait for a few seconds after the last person enters before closing. If someone else walks in, the timer resets.

### When to use:
- **Search input** — wait until user stops typing, then search
- **Window resize** — wait until user stops resizing
- **Button clicks** — prevent double-submit

### ✅ Polyfill:
```js
function debounce(func, delay, options = {}) {
  let timeoutId = null;
  const { leading = false } = options;

  function debounced(...args) {
    // 'this' preservation — important for methods
    const context = this;

    // Should we call on the LEADING edge? (i.e., immediately on first call)
    const callNow = leading && timeoutId === null;

    // Clear any existing timer (this is the "reset" behavior)
    clearTimeout(timeoutId);

    // Set a new timer
    timeoutId = setTimeout(() => {
      timeoutId = null;
      // Call on the TRAILING edge (default behavior)
      if (!leading) {
        func.apply(context, args);
      }
    }, delay);

    // If leading mode, call immediately on first trigger
    if (callNow) {
      func.apply(context, args);
    }
  }

  // Allow manual cancellation
  debounced.cancel = function () {
    clearTimeout(timeoutId);
    timeoutId = null;
  };

  return debounced;
}

// --- Testing ---
// Scenario: Search as user types
const searchAPI = (query) => {
  console.log(`Searching for: ${query}`);
};

const debouncedSearch = debounce(searchAPI, 300);

// User types: "J", "Ja", "Jav", "Java"
debouncedSearch("J");    // Timer starts
debouncedSearch("Ja");   // Timer resets
debouncedSearch("Jav");  // Timer resets
debouncedSearch("Java"); // Timer resets → after 300ms → "Searching for: Java"
// Only ONE API call is made! 🎉
```

### 🔍 Interview tip:
Draw a timeline diagram to explain debounce:
```
User types:    J--Ja--Jav--Java---------->
Timer:         [start][reset][reset][reset]...[FIRE!]
API calls:                                    ✅ "Java"
```

---

## 40. Throttle
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
**Throttle** ensures a function is called at most **once** in a specified time period. Unlike debounce, it doesn't wait — it guarantees regular execution.

### Simple analogy:
A water tap with a flow controller. No matter how much you turn it, water comes out at a steady, controlled rate.

### Difference from debounce:
| | Debounce | Throttle |
|---|---------|----------|
| When it fires | After user STOPS for X ms | Every X ms during activity |
| Best for | Search input, form validation | Scroll events, resize, gaming |
| Guarantees | Last call happens | Regular intervals |

### ✅ Polyfill:
```js
function throttle(func, limit) {
  let lastCallTime = 0;      // Timestamp of last execution
  let timeoutId = null;       // For trailing edge call

  function throttled(...args) {
    const context = this;
    const now = Date.now();
    const timeSinceLastCall = now - lastCallTime;

    if (timeSinceLastCall >= limit) {
      // Enough time has passed — execute immediately
      lastCallTime = now;
      func.apply(context, args);
    } else {
      // Too soon — schedule for later (trailing call)
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        lastCallTime = Date.now();
        func.apply(context, args);
      }, limit - timeSinceLastCall);
    }
  }

  throttled.cancel = function () {
    clearTimeout(timeoutId);
    timeoutId = null;
  };

  return throttled;
}

// --- Testing ---
const handleScroll = () => {
  console.log("Scroll event handled at:", Date.now());
};

const throttledScroll = throttle(handleScroll, 1000);

// Even if scroll fires 100 times per second,
// handleScroll only executes once per second
// window.addEventListener("scroll", throttledScroll);
```

### 🔍 Interview visualization:
```
Events:    |--|--|--|--|--|--|--|--|--|--|--|--|-->  (every 100ms)
Throttle:  ✅--------✅--------✅--------✅-->     (every 1000ms)
```

---

## 41. Deep Clone
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
Creates a completely independent copy of an object. Changing the clone doesn't affect the original, even for nested objects.

### Why `JSON.parse(JSON.stringify())` is bad:
```js
// ❌ This approach fails for:
// - Functions (lost)
// - undefined (lost)
// - Date objects (become strings)
// - RegExp (become empty objects)
// - Circular references (throws error)
// - Map, Set, Symbol (lost)
```

### ✅ Polyfill (Handles edge cases):
```js
function deepClone(obj, visited = new WeakMap()) {
  // Handle primitives and null
  if (obj === null || typeof obj !== "object") {
    return obj;
  }

  // Handle circular references!
  // If we've already cloned this object, return the cached clone
  if (visited.has(obj)) {
    return visited.get(obj);
  }

  // Handle Date
  if (obj instanceof Date) {
    return new Date(obj.getTime());
  }

  // Handle RegExp
  if (obj instanceof RegExp) {
    return new RegExp(obj.source, obj.flags);
  }

  // Handle Map
  if (obj instanceof Map) {
    const mapClone = new Map();
    visited.set(obj, mapClone);
    obj.forEach((value, key) => {
      mapClone.set(deepClone(key, visited), deepClone(value, visited));
    });
    return mapClone;
  }

  // Handle Set
  if (obj instanceof Set) {
    const setClone = new Set();
    visited.set(obj, setClone);
    obj.forEach((value) => {
      setClone.add(deepClone(value, visited));
    });
    return setClone;
  }

  // Handle Arrays
  if (Array.isArray(obj)) {
    const arrClone = [];
    visited.set(obj, arrClone);
    for (let i = 0; i < obj.length; i++) {
      arrClone[i] = deepClone(obj[i], visited);
    }
    return arrClone;
  }

  // Handle plain Objects
  const objClone = Object.create(Object.getPrototypeOf(obj));
  visited.set(obj, objClone);

  for (const key of Object.keys(obj)) {
    objClone[key] = deepClone(obj[key], visited);
  }

  return objClone;
}

// --- Testing ---
const original = {
  name: "Shubham",
  scores: [90, 85, 92],
  address: {
    city: "Mumbai",
    coords: { lat: 19.07, lng: 72.87 },
  },
  birthdate: new Date("2000-01-15"),
  pattern: /hello/gi,
};

const clone = deepClone(original);

// Modify clone — original should NOT be affected
clone.address.city = "Delhi";
clone.scores.push(100);

console.log(original.address.city); // "Mumbai" — unchanged! ✅
console.log(original.scores);       // [90, 85, 92] — unchanged! ✅

// Circular reference test
const circular = { name: "A" };
circular.self = circular; // Points to itself!
const circularClone = deepClone(circular);
console.log(circularClone.self === circularClone); // true (correctly cloned!)
console.log(circularClone.self === circular);       // false (different objects!)
```

### 💡 Modern alternative: `structuredClone()`
```js
// Available in modern browsers and Node.js 17+
const clone = structuredClone(original);
// Handles Date, Map, Set, circular refs — but NOT functions or DOM nodes
```

---

## 42. Shallow Clone
> ⭐⭐ **IMPORTANT**

### What does it do?
Copies only the **first level** of properties. Nested objects are still **shared references**.

### ✅ Methods:
```js
// Method 1: Spread operator (most common)
const clone1 = { ...original };

// Method 2: Object.assign
const clone2 = Object.assign({}, original);

// Method 3: Custom polyfill
function shallowClone(obj) {
  if (obj === null || typeof obj !== "object") return obj;

  // Handle arrays
  if (Array.isArray(obj)) return [...obj];

  // Handle objects
  const clone = {};
  for (const key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      clone[key] = obj[key]; // Only copies reference for nested objects
    }
  }
  return clone;
}

// --- Testing ---
const original = {
  name: "Shubham",
  address: { city: "Mumbai" }, // Nested object
};

const clone = shallowClone(original);
clone.name = "Ravi";            // ✅ Original NOT affected
clone.address.city = "Delhi";   // ⚠️ Original IS affected! (shared reference)

console.log(original.name);         // "Shubham" — safe
console.log(original.address.city); // "Delhi" — changed! This is the limitation
```

---

## 43. Currying
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
Transforms a function with multiple arguments into a sequence of functions, each taking **one argument**.

`f(a, b, c)` → `f(a)(b)(c)`

### Simple analogy:
Instead of ordering a full meal at once, you choose the starter first, then main course, then dessert — one at a time.

### ✅ Polyfill:
```js
// Basic curry — transforms f(a, b, c) into f(a)(b)(c)
function curry(func) {
  // curried is a helper that collects arguments
  return function curried(...args) {
    // If we have enough arguments, call the original function
    if (args.length >= func.length) {
      return func.apply(this, args);
    }

    // Otherwise, return a new function that collects more arguments
    return function (...nextArgs) {
      return curried.apply(this, [...args, ...nextArgs]);
    };
  };
}

// --- Testing ---
function add(a, b, c) {
  return a + b + c;
}

const curriedAdd = curry(add);

// All of these work:
console.log(curriedAdd(1)(2)(3));     // 6  — one at a time
console.log(curriedAdd(1, 2)(3));     // 6  — two, then one
console.log(curriedAdd(1)(2, 3));     // 6  — one, then two
console.log(curriedAdd(1, 2, 3));     // 6  — all at once

// Practical example: reusable loggers
const log = curry((level, module, message) => {
  console.log(`[${level}] [${module}] ${message}`);
});

const errorLog = log("ERROR");           // preset level
const dbError = errorLog("DATABASE");     // preset module
dbError("Connection failed");             // "Error" + "DATABASE" + message
// Output: [ERROR] [DATABASE] Connection failed
```

### 🔍 Infinite currying (bonus interview question):
```js
// sum(1)(2)(3)...(n)() → returns total when called with no args
function sum(a) {
  return function (b) {
    if (b !== undefined) {
      return sum(a + b); // Keep collecting
    }
    return a; // No more arguments, return total
  };
}

console.log(sum(1)(2)(3)(4)()); // 10
```

---

## 44. Memoization
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
Caches the result of expensive function calls. If the same input is given again, returns the cached result instead of recomputing.

### Simple analogy:
A student solves a math problem. Instead of solving it every time, they write the answer in a notebook. Next time they see the same problem — just look at the notebook!

### ✅ Polyfill:
```js
function memoize(func) {
  // Cache to store results: key = arguments, value = result
  const cache = new Map();

  return function (...args) {
    // Create a unique key from the arguments
    const key = JSON.stringify(args);

    // If we've seen these args before, return cached result
    if (cache.has(key)) {
      console.log("📦 From cache");
      return cache.get(key);
    }

    // Otherwise, compute the result
    console.log("🔄 Computing...");
    const result = func.apply(this, args);

    // Store in cache for future calls
    cache.set(key, result);

    return result;
  };
}

// --- Testing ---
// Expensive function: factorial
function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

const memoizedFactorial = memoize(factorial);

console.log(memoizedFactorial(5)); // 🔄 Computing... → 120
console.log(memoizedFactorial(5)); // 📦 From cache → 120 (instant!)
console.log(memoizedFactorial(10)); // 🔄 Computing... → 3628800
```

### Advanced memoize with LRU cache (bonus):
```js
function memoizeWithLimit(func, maxSize = 100) {
  const cache = new Map();

  return function (...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      // Move to end (most recently used)
      const value = cache.get(key);
      cache.delete(key);
      cache.set(key, value);
      return value;
    }

    const result = func.apply(this, args);
    cache.set(key, result);

    // If cache exceeds limit, remove the OLDEST entry (LRU)
    if (cache.size > maxSize) {
      const oldestKey = cache.keys().next().value;
      cache.delete(oldestKey);
    }

    return result;
  };
}
```

---

## 45. Flatten Array
> ⭐⭐⭐ **MUST KNOW FOR INTERVIEWS**

### What does it do?
Converts a nested array into a single-level array. (This is a custom version — different from `Array.flat()`).

### ✅ Multiple approaches (interviewers love options!):

```js
// ============================================
// Approach 1: Recursive (most intuitive)
// ============================================
function flattenRecursive(arr) {
  const result = [];

  for (const item of arr) {
    if (Array.isArray(item)) {
      // If item is an array, recursively flatten and spread
      result.push(...flattenRecursive(item));
    } else {
      result.push(item);
    }
  }

  return result;
}

// ============================================
// Approach 2: Using reduce (concise)
// ============================================
function flattenReduce(arr) {
  return arr.reduce((acc, curr) => {
    return acc.concat(Array.isArray(curr) ? flattenReduce(curr) : curr);
  }, []);
}

// ============================================
// Approach 3: Iterative with stack (no recursion)
// ============================================
function flattenIterative(arr) {
  const stack = [...arr]; // Copy array to stack
  const result = [];

  while (stack.length > 0) {
    const item = stack.pop(); // Take from end

    if (Array.isArray(item)) {
      stack.push(...item); // If array, put its elements back on stack
    } else {
      result.unshift(item); // If not array, add to result (unshift to maintain order)
    }
  }

  return result;
}

// ============================================
// Approach 4: Using toString (hacky, only for numbers)
// ============================================
function flattenToString(arr) {
  return arr
    .toString()       // "1,2,3,4,5"
    .split(",")       // ["1", "2", "3", "4", "5"]
    .map(Number);     // [1, 2, 3, 4, 5]
}

// --- Testing ---
const nested = [1, [2, [3, [4, [5]]]], 6, [7, 8]];

console.log(flattenRecursive(nested));  // [1, 2, 3, 4, 5, 6, 7, 8]
console.log(flattenReduce(nested));     // [1, 2, 3, 4, 5, 6, 7, 8]
console.log(flattenIterative(nested));  // [1, 2, 3, 4, 5, 6, 7, 8]
```

---

## 46. Pipe & Compose
> ⭐⭐ **IMPORTANT**

### What do they do?
Both combine multiple functions into one. The difference is the **order of execution**:
- **Pipe**: Left to right → `pipe(f, g, h)(x)` = `h(g(f(x)))`
- **Compose**: Right to left → `compose(f, g, h)(x)` = `f(g(h(x)))`

### ✅ Polyfill:
```js
// Pipe: f1 → f2 → f3 (left to right)
function pipe(...fns) {
  return function (input) {
    return fns.reduce((acc, fn) => fn(acc), input);
  };
}

// Compose: f3 → f2 → f1 (right to left, like math)
function compose(...fns) {
  return function (input) {
    return fns.reduceRight((acc, fn) => fn(acc), input);
  };
}

// --- Testing ---
const add10 = (x) => x + 10;
const multiply2 = (x) => x * 2;
const subtract5 = (x) => x - 5;

// Pipe: 3 → add10 → 13 → multiply2 → 26 → subtract5 → 21
const piped = pipe(add10, multiply2, subtract5);
console.log(piped(3)); // 21

// Compose: 3 → subtract5 → -2 → multiply2 → -4 → add10 → 6
const composed = compose(add10, multiply2, subtract5);
console.log(composed(3)); // 6
```

---

## 47. Event Emitter
> ⭐⭐ **IMPORTANT** — Common in Node.js and React interview questions

### What does it do?
An Event Emitter follows the **pub-sub pattern** — components can subscribe to events and get notified when those events occur.

### ✅ Polyfill:
```js
class EventEmitter {
  constructor() {
    // Store events and their listeners
    // { 'click': [fn1, fn2], 'submit': [fn3] }
    this.events = {};
  }

  // Subscribe to an event
  on(eventName, listener) {
    if (!this.events[eventName]) {
      this.events[eventName] = [];
    }
    this.events[eventName].push(listener);
    return this; // Allow chaining
  }

  // Subscribe but only fire ONCE
  once(eventName, listener) {
    const wrapper = (...args) => {
      listener.apply(this, args);
      this.off(eventName, wrapper); // Remove after first call
    };
    this.on(eventName, wrapper);
    return this;
  }

  // Emit an event (notify all listeners)
  emit(eventName, ...args) {
    const listeners = this.events[eventName];
    if (!listeners || listeners.length === 0) return false;

    // Call each listener with the provided arguments
    // Use slice to avoid issues if a listener removes itself
    listeners.slice().forEach((listener) => {
      listener.apply(this, args);
    });

    return true;
  }

  // Unsubscribe from an event
  off(eventName, listener) {
    const listeners = this.events[eventName];
    if (!listeners) return this;

    this.events[eventName] = listeners.filter((l) => l !== listener);
    return this;
  }

  // Remove ALL listeners for an event (or all events)
  removeAllListeners(eventName) {
    if (eventName) {
      delete this.events[eventName];
    } else {
      this.events = {};
    }
    return this;
  }
}

// --- Testing ---
const emitter = new EventEmitter();

// Subscribe
const greetHandler = (name) => console.log(`Hello, ${name}!`);
emitter.on("greet", greetHandler);
emitter.on("greet", (name) => console.log(`Welcome, ${name}!`));

// Emit
emitter.emit("greet", "Shubham");
// Output:
// "Hello, Shubham!"
// "Welcome, Shubham!"

// Once — fires only once
emitter.once("login", (user) => console.log(`${user} logged in`));
emitter.emit("login", "Shubham"); // "Shubham logged in"
emitter.emit("login", "Shubham"); // Nothing! (already unsubscribed)

// Unsubscribe
emitter.off("greet", greetHandler);
emitter.emit("greet", "Ravi");
// Output: Only "Welcome, Ravi!" (greetHandler was removed)
```

---

## 48. setInterval using setTimeout
> ⭐⭐ **IMPORTANT**

### Why would you do this?
- `setInterval` can cause issues if the callback takes longer than the interval
- `setTimeout` approach ensures the next call only starts AFTER the previous one finishes

### ✅ Polyfill:
```js
function mySetInterval(callback, delay) {
  let isCancelled = false;

  function execute() {
    if (isCancelled) return;

    callback(); // Run the callback

    // Schedule the next execution
    setTimeout(execute, delay);
  }

  // Start the first execution
  setTimeout(execute, delay);

  // Return a cancel function
  return {
    clear() {
      isCancelled = true;
    },
  };
}

// --- Testing ---
let count = 0;
const interval = mySetInterval(() => {
  count++;
  console.log(`Tick ${count}`);

  if (count === 3) {
    interval.clear(); // Stop after 3 ticks
    console.log("Stopped!");
  }
}, 1000);
// Output: Tick 1 → Tick 2 → Tick 3 → Stopped!
```

---

## 49. clearAllTimers
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
// Utility to clear ALL active timeouts and intervals
function clearAllTimers() {
  // Get the highest timer ID
  const highestId = setTimeout(() => {}, 0);

  // Clear all IDs from 0 to highestId
  for (let i = 0; i <= highestId; i++) {
    clearTimeout(i);
    clearInterval(i);
  }
}

// Useful in tests or cleanup scenarios
```

---

## 50. once() Function
> ⭐⭐ **IMPORTANT**

### What does it do?
Creates a function that can only be called **once**. Subsequent calls return the result of the first call.

### ✅ Polyfill:
```js
function once(func) {
  let called = false;
  let result;

  return function (...args) {
    if (!called) {
      called = true;
      result = func.apply(this, args);
    }
    // Always return the first call's result
    return result;
  };
}

// --- Testing ---
const initialize = once(() => {
  console.log("Initializing...");
  return { status: "ready" };
});

console.log(initialize()); // "Initializing..." → { status: "ready" }
console.log(initialize()); // (nothing logged) → { status: "ready" }
console.log(initialize()); // (nothing logged) → { status: "ready" }
// The function body only ran ONCE!
```

---

## 51. Custom Iterable
> ⭐⭐ **IMPORTANT**

### What is it?
JavaScript's `for...of` loop works with **iterables**. An iterable is any object that implements `Symbol.iterator`. 

### ✅ Polyfill:
```js
// Make a range object iterable
function range(start, end) {
  return {
    // This is what makes it iterable — the for...of protocol
    [Symbol.iterator]() {
      let current = start;

      return {
        // next() is called on each iteration
        next() {
          if (current <= end) {
            return { value: current++, done: false };
          }
          return { value: undefined, done: true };
        },
      };
    },
  };
}

// --- Testing ---
for (const num of range(1, 5)) {
  console.log(num); // 1, 2, 3, 4, 5
}

// Works with spread too!
console.log([...range(1, 5)]); // [1, 2, 3, 4, 5]

// Works with destructuring
const [a, b, c] = range(10, 15);
console.log(a, b, c); // 10, 11, 12
```

### Making any object iterable:
```js
const team = {
  members: ["Shubham", "Ravi", "Priya"],
  [Symbol.iterator]() {
    let index = 0;
    const members = this.members;

    return {
      next() {
        if (index < members.length) {
          return { value: members[index++], done: false };
        }
        return { done: true };
      },
    };
  },
};

for (const member of team) {
  console.log(member); // "Shubham", "Ravi", "Priya"
}
```

---

## 52. async/await to Promise
> ⭐⭐ **IMPORTANT** — Understanding how async/await works under the hood

### What to know:
`async/await` is just syntactic sugar over Promises. Here's how they relate:

```js
// This async function:
async function fetchUser() {
  const response = await fetch("/api/user");
  const data = await response.json();
  return data;
}

// Is equivalent to this Promise chain:
function fetchUser() {
  return fetch("/api/user")
    .then((response) => response.json())
    .then((data) => data);
}
```

### ✅ Convert Promise to "async-like" with generator (how Babel does it):
```js
// This is how async/await was polyfilled before native support!
function asyncToGenerator(generatorFn) {
  return function (...args) {
    const generator = generatorFn.apply(this, args);

    return new Promise((resolve, reject) => {
      function step(key, value) {
        try {
          // Call generator.next(value) or generator.throw(value)
          const result = generator[key](value);

          if (result.done) {
            // Generator finished — resolve with final value
            resolve(result.value);
          } else {
            // Generator yielded — wait for the promise, then continue
            Promise.resolve(result.value).then(
              (val) => step("next", val),
              (err) => step("throw", err)
            );
          }
        } catch (error) {
          reject(error);
        }
      }

      step("next", undefined); // Start the generator
    });
  };
}

// --- Testing ---
// Instead of: async function getData() { const data = await fetch(...) }
// You write:
const getData = asyncToGenerator(function* () {
  const response = yield fetch("https://jsonplaceholder.typicode.com/todos/1");
  const data = yield response.json();
  return data;
});

// getData().then(console.log);
```

---

## 53. JSON.stringify() (Simplified)
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
function myStringify(value) {
  // Handle null
  if (value === null) return "null";

  // Handle undefined and functions (they get omitted in objects, become null in arrays)
  if (value === undefined || typeof value === "function") return undefined;

  // Handle booleans
  if (typeof value === "boolean") return value.toString();

  // Handle numbers
  if (typeof value === "number") {
    if (Number.isFinite(value)) return String(value);
    return "null"; // NaN, Infinity → null
  }

  // Handle strings (must be double-quoted)
  if (typeof value === "string") return `"${value}"`;

  // Handle arrays
  if (Array.isArray(value)) {
    const items = value.map((item) => {
      const stringified = myStringify(item);
      return stringified === undefined ? "null" : stringified;
    });
    return `[${items.join(",")}]`;
  }

  // Handle objects
  if (typeof value === "object") {
    const pairs = [];
    for (const key of Object.keys(value)) {
      const stringifiedValue = myStringify(value[key]);
      // Skip undefined and function values
      if (stringifiedValue !== undefined) {
        pairs.push(`"${key}":${stringifiedValue}`);
      }
    }
    return `{${pairs.join(",")}}`;
  }
}

// --- Testing ---
console.log(myStringify({ name: "Shubham", age: 25 }));
// '{"name":"Shubham","age":25}'

console.log(myStringify([1, "hello", null, true]));
// '[1,"hello",null,true]'

console.log(myStringify({ a: undefined, b: function () {}, c: 42 }));
// '{"c":42}' — undefined and functions are skipped
```

---

## 54. JSON.parse() (Simplified)
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill (using eval — for learning purposes):
```js
// ⚠️ WARNING: Using eval is dangerous in production!
// This is for understanding how parsing works conceptually
function myParse(jsonString) {
  // Use Function constructor instead of eval (slightly safer)
  // The string is wrapped in parentheses to handle edge cases
  return new Function("return " + jsonString)();
}

// --- Testing ---
const obj = myParse('{"name":"Shubham","age":25}');
console.log(obj.name); // "Shubham"
console.log(obj.age);  // 25

// NOTE: In real implementations, JSON.parse uses a proper parser
// (lexer + token analysis) and does NOT use eval/Function
```

### 🔍 Interview: Why is `eval()` dangerous?
Because it executes arbitrary code. If the JSON string contains malicious JavaScript, `eval` would run it. Real `JSON.parse` validates the string strictly.

---

## 55. Array.reduceRight()
> ⭐ **GOOD TO KNOW**

### What does it do?
Same as `reduce()`, but processes elements from **right to left**.

### ✅ Polyfill:
```js
Array.prototype.myReduceRight = function (callback, initialValue) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  let accumulator;
  let startIndex;

  if (initialValue !== undefined) {
    accumulator = initialValue;
    startIndex = this.length - 1;
  } else {
    if (this.length === 0) {
      throw new TypeError("Reduce of empty array with no initial value");
    }
    accumulator = this[this.length - 1];
    startIndex = this.length - 2;
  }

  // Iterate from right to left
  for (let i = startIndex; i >= 0; i--) {
    if (i in this) {
      accumulator = callback(accumulator, this[i], i, this);
    }
  }

  return accumulator;
};

// --- Testing ---
const nested = [[1, 2], [3, 4], [5, 6]];
const flatReversed = nested.myReduceRight((acc, curr) => acc.concat(curr), []);
console.log(flatReversed); // [5, 6, 3, 4, 1, 2]
```

---

## 56. groupBy()
> ⭐⭐ **IMPORTANT** — Very practical and increasingly asked!

### What does it do?
Groups array elements by a key returned from a callback function.

### ✅ Polyfill:
```js
function groupBy(array, keyFn) {
  return array.reduce((groups, item) => {
    // Get the group key for this item
    const key = typeof keyFn === "function" ? keyFn(item) : item[keyFn];

    // If this group doesn't exist yet, create it
    if (!groups[key]) {
      groups[key] = [];
    }

    // Add item to its group
    groups[key].push(item);

    return groups;
  }, {});
}

// Also as a prototype method (proposed for JS):
// Array.prototype.myGroupBy = function(keyFn) { ... }

// --- Testing ---
const people = [
  { name: "Shubham", department: "Engineering" },
  { name: "Priya", department: "Design" },
  { name: "Ravi", department: "Engineering" },
  { name: "Neha", department: "Design" },
  { name: "Amit", department: "Marketing" },
];

const grouped = groupBy(people, (person) => person.department);
console.log(grouped);
// {
//   Engineering: [{ name: "Shubham", ... }, { name: "Ravi", ... }],
//   Design: [{ name: "Priya", ... }, { name: "Neha", ... }],
//   Marketing: [{ name: "Amit", ... }]
// }

// Group numbers by even/odd
const numbers = [1, 2, 3, 4, 5, 6];
const evenOdd = groupBy(numbers, (n) => (n % 2 === 0 ? "even" : "odd"));
console.log(evenOdd);
// { odd: [1, 3, 5], even: [2, 4, 6] }
```

---

## 57. Promise.finally()
> ⭐ **GOOD TO KNOW**

### What does it do?
Runs a callback when the promise settles (whether fulfilled or rejected). Useful for cleanup (like hiding a loader).

### ✅ Polyfill:
```js
Promise.prototype.myFinally = function (onFinally) {
  return this.then(
    // On fulfillment: run onFinally, then pass through the value
    (value) => Promise.resolve(onFinally()).then(() => value),
    // On rejection: run onFinally, then re-throw the error
    (reason) =>
      Promise.resolve(onFinally()).then(() => {
        throw reason;
      })
  );
};

// --- Testing ---
function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => resolve("Data loaded"), 1000);
  });
}

fetchData()
  .then((data) => console.log(data))
  .catch((err) => console.error(err))
  .myFinally(() => {
    console.log("Loader hidden"); // Always runs!
  });
```

---

## 58. Proxy & Reflect
> ⭐ **GOOD TO KNOW** — Cannot be truly polyfilled, but important to understand

### What is Proxy?
A Proxy wraps an object and intercepts operations on it (get, set, delete, etc.). It's like a security guard for your object.

```js
// ===================================
// Reactive object (like Vue.js does!)
// ===================================
function reactive(obj, onChange) {
  return new Proxy(obj, {
    // Intercept property reads
    get(target, prop, receiver) {
      console.log(`📖 Reading "${prop}"`);
      const value = Reflect.get(target, prop, receiver);

      // If the value is an object, make it reactive too (deep reactivity)
      if (typeof value === "object" && value !== null) {
        return reactive(value, onChange);
      }
      return value;
    },

    // Intercept property writes
    set(target, prop, value, receiver) {
      const oldValue = target[prop];
      const result = Reflect.set(target, prop, value, receiver);

      if (oldValue !== value) {
        console.log(`✏️ Changed "${prop}": ${oldValue} → ${value}`);
        if (onChange) onChange(prop, value, oldValue);
      }

      return result;
    },

    // Intercept property deletions
    deleteProperty(target, prop) {
      console.log(`🗑️ Deleting "${prop}"`);
      return Reflect.deleteProperty(target, prop);
    },
  });
}

// --- Testing ---
const state = reactive({ count: 0, name: "Shubham" }, (prop, newVal) => {
  console.log(`State updated: ${prop} = ${newVal}`);
});

state.count = 1;
// ✏️ Changed "count": 0 → 1
// State updated: count = 1

console.log(state.name);
// 📖 Reading "name"
// "Shubham"
```

### Validation with Proxy:
```js
const validator = {
  set(target, prop, value) {
    if (prop === "age" && typeof value !== "number") {
      throw new TypeError("Age must be a number");
    }
    if (prop === "age" && (value < 0 || value > 150)) {
      throw new RangeError("Age must be between 0 and 150");
    }
    target[prop] = value;
    return true;
  },
};

const person = new Proxy({}, validator);
person.age = 25;   // ✅ Works
// person.age = -5;   // ❌ RangeError: Age must be between 0 and 150
// person.age = "old"; // ❌ TypeError: Age must be a number
```

---

## 59. String.replaceAll()
> ⭐ **GOOD TO KNOW**

### ✅ Polyfill:
```js
String.prototype.myReplaceAll = function (search, replacement) {
  // If search is a string, escape special regex characters and use global flag
  if (typeof search === "string") {
    // Escape special regex characters in the search string
    const escaped = search.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
    return this.replace(new RegExp(escaped, "g"), replacement);
  }

  // If search is a regex, ensure it has the global flag
  if (search instanceof RegExp) {
    if (!search.global) {
      throw new TypeError("replaceAll must be called with a global RegExp");
    }
    return this.replace(search, replacement);
  }
};

// --- Testing ---
console.log("hello world hello".myReplaceAll("hello", "hi"));
// "hi world hi"

console.log("a.b.c.d".myReplaceAll(".", "-"));
// "a-b-c-d"
```

---

## 60. at() Method
> ⭐ **GOOD TO KNOW**

### What does it do?
Access elements by index, supporting **negative indices** (from the end).

### ✅ Polyfill:
```js
// For Arrays
Array.prototype.myAt = function (index) {
  // Convert to integer
  index = Math.trunc(index) || 0;

  // Handle negative index
  if (index < 0) {
    index = this.length + index;
  }

  // Return undefined if out of bounds
  if (index < 0 || index >= this.length) {
    return undefined;
  }

  return this[index];
};

// For Strings
String.prototype.myAt = function (index) {
  index = Math.trunc(index) || 0;
  if (index < 0) index = this.length + index;
  if (index < 0 || index >= this.length) return undefined;
  return this[index];
};

// --- Testing ---
const arr = [10, 20, 30, 40, 50];

console.log(arr.myAt(0));   // 10 — first element
console.log(arr.myAt(-1));  // 50 — last element!
console.log(arr.myAt(-2));  // 40 — second from last

console.log("Hello".myAt(-1)); // "o" — last character
```

---

---

# BONUS: QUICK REFERENCE CHEAT SHEET

## Most Asked Polyfills in Interviews (Top 15)

| Rank | Polyfill | Why it's asked |
|------|---------|---------------|
| 1 | `Array.map()` | Tests prototype, callback, `this` understanding |
| 2 | `Array.filter()` | Tests conditional logic |
| 3 | `Array.reduce()` | Tests accumulator pattern + versatility |
| 4 | `Function.bind()` | Tests `this`, closures, partial application |
| 5 | `Function.call()` | Tests `this` binding mechanism |
| 6 | `Function.apply()` | Tests call vs apply difference |
| 7 | `Promise` (basic) | Tests async understanding |
| 8 | `Promise.all()` | Tests parallel async handling |
| 9 | `Debounce` | Tests closures, timers, practical usage |
| 10 | `Throttle` | Tests closures, timers, debounce vs throttle |
| 11 | `Deep Clone` | Tests recursion, edge cases, data structures |
| 12 | `Currying` | Tests closures, function composition |
| 13 | `Flatten Array` | Tests recursion, multiple approaches |
| 14 | `new` keyword | Tests OOP, prototypes, constructor behavior |
| 15 | `Memoization` | Tests caching, closures, optimization |

---

## Key Concepts Needed for Polyfills

### 1. `this` keyword
```js
// 'this' in a method → the object before the dot
// 'this' in a standalone function → window (or undefined in strict mode)
// 'this' in an arrow function → inherited from parent scope
```

### 2. Prototype Chain
```js
// Every object has a __proto__ that links to its constructor's .prototype
// Array methods live on Array.prototype
// That's why [].map() works → [] → Array.prototype.map
```

### 3. Closures
```js
// A closure is when a function "remembers" variables from its parent scope
// even after the parent function has finished executing
function outer() {
  let count = 0; // This variable is "closed over"
  return function inner() {
    count++;
    return count;
  };
}
const counter = outer();
counter(); // 1
counter(); // 2 — 'count' is remembered!
```

### 4. Higher-Order Functions
```js
// Functions that take functions as arguments OR return functions
// map, filter, reduce, bind → all are higher-order functions
```

---

## Interview Tips

1. **Always start by explaining** what the method does before writing code
2. **Handle edge cases**: null/undefined inputs, empty arrays, wrong types
3. **Use `Object.prototype.hasOwnProperty.call()`** instead of `obj.hasOwnProperty()` (safer)
4. **Check `i in this`** for sparse array handling in array polyfills
5. **Mention the `thisArg` parameter** — many candidates forget it exists
6. **Practice writing from scratch** — don't memorize, understand the logic

---

> **Last updated:** 2025 | **Level:** Beginner to Advanced
>
> 💡 **Pro tip:** Practice writing these polyfills without looking at the answers. Start with map, filter, reduce, and bind — they cover 80% of the concepts you need.
