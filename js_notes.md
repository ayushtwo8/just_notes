# 📘 Complete JavaScript Notes — Beginner to Advanced
### Every Concept Explained Simply with Real Examples

---

# TABLE OF CONTENTS

1. [What is JavaScript?](#1-what-is-javascript)
2. [Variables — var, let, const](#2-variables--var-let-const)
3. [Data Types](#3-data-types)
4. [Operators](#4-operators)
5. [Strings in Detail](#5-strings-in-detail)
6. [Arrays in Detail](#6-arrays-in-detail)
7. [Objects in Detail](#7-objects-in-detail)
8. [Conditional Statements](#8-conditional-statements)
9. [Loops](#9-loops)
10. [Functions](#10-functions)
11. [Scope & Hoisting](#11-scope--hoisting)
12. [Closures](#12-closures)
13. [The `this` Keyword](#13-the-this-keyword)
14. [Prototypes & Inheritance](#14-prototypes--inheritance)
15. [Classes](#15-classes)
16. [Error Handling](#16-error-handling)
17. [Destructuring](#17-destructuring)
18. [Spread & Rest](#18-spread--rest)
19. [ES6+ Modern Features](#19-es6-modern-features)
20. [How JavaScript Runs — The Engine](#20-how-javascript-runs--the-engine)
21. [The Event Loop](#21-the-event-loop)
22. [Callbacks](#22-callbacks)
23. [Promises](#23-promises)
24. [Async / Await](#24-async--await)
25. [DOM Manipulation](#25-dom-manipulation)
26. [Events](#26-events)
27. [Fetch API & HTTP Requests](#27-fetch-api--http-requests)
28. [LocalStorage & SessionStorage](#28-localstorage--sessionstorage)
29. [Modules (import / export)](#29-modules-import--export)
30. [Important Array Methods](#30-important-array-methods)
31. [Important Object Methods](#31-important-object-methods)
32. [Common Patterns & Best Practices](#32-common-patterns--best-practices)

---

# 1. What is JavaScript?

## 1.1 A Simple Explanation

JavaScript is a **programming language** that makes web pages interactive. Think of a web page like a house:

- **HTML** = the walls, floors, and roof (structure)
- **CSS** = the paint, furniture, and decoration (style)
- **JavaScript** = the electricity, plumbing, and smart devices (behavior)

Without JavaScript, web pages would be static documents — like reading a newspaper. With JavaScript, you get:
- Buttons that actually do things when clicked
- Forms that validate your input before submitting
- Content that loads without refreshing the page
- Animations and interactive effects
- Full applications like Gmail, YouTube, Google Maps

## 1.2 Where Does JavaScript Run?

```
Browser (Frontend):
  Chrome, Firefox, Safari, Edge all have a JS engine built in
  Chrome → V8 engine
  Firefox → SpiderMonkey
  Safari → JavaScriptCore

Server (Backend):
  Node.js = V8 engine + extra APIs (file system, network, etc.)
  You can run JS on servers to build APIs, process data, etc.

So JavaScript truly runs EVERYWHERE:
  Web browsers → Frontend apps
  Node.js servers → Backend APIs
  React Native → Mobile apps (iOS & Android)
  Electron → Desktop apps (VS Code is built with it!)
```

## 1.3 Your First JavaScript

```html
<!DOCTYPE html>
<html>
<head>
  <title>My First JS</title>
</head>
<body>

  <h1>Hello World Page</h1>
  <button id="myBtn">Click me!</button>
  <p id="result"></p>

  <script>
    // This is JavaScript code
    // Everything inside <script> tags is JS

    // Show a message in the browser console
    console.log("JavaScript is running!");

    // Make the button do something
    document.getElementById("myBtn").addEventListener("click", function() {
      document.getElementById("result").textContent = "You clicked the button! 🎉";
    });
  </script>

</body>
</html>
```

## 1.4 The Browser Console

The console is your best friend for learning JavaScript. Open it with `F12` → Console tab.

```js
console.log("Hello");           // Print a value
console.log(1 + 2);             // Print result: 3
console.error("Something broke!"); // Red error message
console.warn("Be careful!");    // Yellow warning
console.table([1, 2, 3]);       // Display array as table
console.clear();                // Clear the console
```

---

# 2. Variables — var, let, const

## 2.1 What is a Variable?

A variable is a **named container** for storing a value. Think of it like a labeled box.

```
  ┌─────────┐
  │  Alice  │   ← value stored inside
  └─────────┘
      name       ← label on the box (variable name)
```

## 2.2 Declaring Variables

JavaScript has three ways to declare variables:

```js
// var — old way (avoid in modern JS)
var age = 25;

// let — for values that will change
let score = 0;
score = 10;  // ✅ allowed to change

// const — for values that will NOT change
const PI = 3.14159;
// PI = 3;  // ❌ Error! Can't reassign const
```

## 2.3 let vs const — When to Use Which?

```js
// Use const by DEFAULT
const userName = "Alice";
const maxScore = 100;
const API_URL = "https://api.example.com";

// Switch to let only when you NEED to reassign
let currentScore = 0;
let isLoggedIn = false;
let message = "";

function playGame() {
  currentScore = currentScore + 10; // needs let
  if (currentScore >= maxScore) {
    isLoggedIn = true;              // needs let
    message = "You won!";           // needs let
  }
}
```

## 2.4 Variable Naming Rules

```js
// ✅ Valid names
let firstName = "Alice";
let age2 = 25;
let _privateVar = "hidden";
let $price = 99.99;
let camelCaseIsStandard = true;

// ❌ Invalid names
// let 2age = 25;        // can't START with number
// let first-name = "";  // no hyphens
// let let = "value";    // can't use reserved keywords

// Naming conventions
const MY_CONSTANT = 42;        // SCREAMING_SNAKE_CASE for constants
let myVariable = "hello";      // camelCase for variables
function doSomething() {}      // camelCase for functions
class MyClass {}               // PascalCase for classes
```

## 2.5 var — Why Avoid It

```js
// var is function-scoped, not block-scoped
if (true) {
  var x = 10;  // var leaks outside the block!
  let y = 20;  // let stays inside the block
}
console.log(x); // 10 — x leaked out!
console.log(y); // ❌ ReferenceError — y is not defined here

// var is hoisted AND initialized to undefined
console.log(name); // undefined (not an error with var!)
var name = "Alice";

// This causes confusing bugs — another reason to avoid var
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Prints: 3, 3, 3 (not 0, 1, 2 — var shares the same i!)

for (let j = 0; j < 3; j++) {
  setTimeout(() => console.log(j), 100);
}
// Prints: 0, 1, 2 ✅ (let creates new j for each iteration)
```

---

# 3. Data Types

## 3.1 Primitive Types (7 types)

```js
// 1. Number — integers and decimals
let age = 25;
let price = 19.99;
let negative = -10;
let bigNum = 1_000_000;  // underscores for readability (ES2021)
let infinity = Infinity;
let notANumber = NaN;    // result of invalid math operations

// 2. String — text
let name = "Alice";
let city = 'Mumbai';
let message = `Hello, ${name}!`;  // template literal (backticks)

// 3. Boolean — true or false only
let isLoggedIn = true;
let hasPermission = false;

// 4. undefined — variable declared but not assigned
let x;
console.log(x);  // undefined

// 5. null — intentional absence of value
let user = null;  // user doesn't exist yet

// 6. BigInt — numbers larger than Number can hold
let hugeNumber = 9007199254740991n;  // add 'n' at the end
let billion = 1_000_000_000n;

// 7. Symbol — unique identifier (advanced, rarely used by beginners)
const id1 = Symbol("id");
const id2 = Symbol("id");
console.log(id1 === id2);  // false — every Symbol is unique
```

## 3.2 Reference Types (Object types)

```js
// Object — key-value pairs
const person = {
  name: "Alice",
  age: 25,
  isStudent: true
};

// Array — ordered list
const fruits = ["apple", "banana", "mango"];

// Function — reusable block of code
function greet(name) {
  return `Hello, ${name}!`;
}

// Date
const today = new Date();

// Regular Expression
const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
```

## 3.3 typeof — Check a Type

```js
typeof 42;           // "number"
typeof "hello";      // "string"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof null;         // "object" ← famous JavaScript bug, null is NOT an object
typeof {};           // "object"
typeof [];           // "object" ← arrays are objects!
typeof function(){}; // "function"
typeof Symbol();     // "symbol"
typeof 42n;          // "bigint"

// How to properly check for null
let value = null;
value === null;  // true ✅

// How to check for array
Array.isArray([1, 2, 3]);  // true ✅
```

## 3.4 Type Conversion

```js
// String to Number
Number("42");       // 42
Number("3.14");     // 3.14
Number("hello");    // NaN (not a valid number)
Number("");         // 0
Number(true);       // 1
Number(false);      // 0
Number(null);       // 0
Number(undefined);  // NaN
parseInt("42px");   // 42 — stops at non-numeric character
parseFloat("3.14m"); // 3.14

// Number to String
String(42);      // "42"
(42).toString(); // "42"
(255).toString(16); // "ff" — convert to hexadecimal

// To Boolean
Boolean(0);         // false
Boolean("");        // false
Boolean(null);      // false
Boolean(undefined); // false
Boolean(NaN);       // false
// EVERYTHING ELSE is true:
Boolean(1);         // true
Boolean("hello");   // true
Boolean([]);        // true (empty array is truthy!)
Boolean({});        // true (empty object is truthy!)

// Implicit type coercion (JavaScript does this automatically — can be confusing!)
"5" + 3;   // "53" — number becomes string (+ concatenates)
"5" - 3;   // 2   — string becomes number (- only works on numbers)
"5" * 2;   // 10
"5" == 5;  // true — loose equality converts types
"5" === 5; // false — strict equality does NOT convert types ← Always use ===
```

---

# 4. Operators

## 4.1 Arithmetic Operators

```js
let a = 10;
let b = 3;

a + b;   // 13 — addition
a - b;   // 7  — subtraction
a * b;   // 30 — multiplication
a / b;   // 3.3333... — division
a % b;   // 1  — modulo (remainder after division)
a ** b;  // 1000 — exponentiation (10 to the power of 3)

// Increment & Decrement
let count = 5;
count++;   // count becomes 6 (post-increment — use THEN increase)
++count;   // count becomes 7 (pre-increment — increase THEN use)
count--;   // count becomes 6
--count;   // count becomes 5

// Real-world example — modulo for even/odd check
function isEven(num) {
  return num % 2 === 0;
}
isEven(4);  // true
isEven(7);  // false

// Real-world — modulo for cycling through items
const colors = ["red", "green", "blue"];
let current = 0;
function nextColor() {
  current = (current + 1) % colors.length;
  return colors[current];
}
// 0 → 1 → 2 → 0 → 1 → 2 (cycles back to 0)
```

## 4.2 Comparison Operators

```js
5 == "5";   // true  — loose equality (converts types — AVOID)
5 === "5";  // false — strict equality (no type conversion — USE THIS)
5 === 5;    // true

5 != "5";   // false — loose inequality
5 !== "5";  // true  — strict inequality (USE THIS)

10 > 5;     // true
10 < 5;     // false
10 >= 10;   // true (greater than OR equal)
10 <= 9;    // false

// String comparison — alphabetical order
"apple" < "banana";   // true
"Z" < "a";            // true (uppercase letters come before lowercase in ASCII)

// Comparing different types (always use ===)
null == undefined;   // true (loose)
null === undefined;  // false (strict)
NaN === NaN;         // false (NaN is never equal to itself!)
Number.isNaN(NaN);   // true ← correct way to check for NaN
```

## 4.3 Logical Operators

```js
// && — AND (both must be true)
true && true;    // true
true && false;   // false
false && true;   // false

// || — OR (at least one must be true)
true || false;   // true
false || false;  // false

// ! — NOT (flips true/false)
!true;   // false
!false;  // true
!!0;     // false (double NOT — converts to boolean)
!!"hello"; // true

// Short-circuit evaluation — very important!

// && returns first falsy value, or last value if all truthy
false && console.log("won't run");  // false — stops at false
0 && "hello";    // 0 (first falsy)
"hi" && "there"; // "there" (both truthy, returns last)
null && doSomething(); // null — doSomething() never called!

// || returns first truthy value, or last value if all falsy
"" || "default";      // "default" — used for fallback values!
null || "fallback";   // "fallback"
0 || 42;              // 42
"hello" || "world";   // "hello" (first truthy)

// Real-world use:
const username = userInput || "Guest"; // if userInput is empty/""/null, use "Guest"
user && user.name;                     // only access .name if user exists

// ?? — Nullish Coalescing (only falls back for null/undefined, not 0 or "")
0 || "default";    // "default" (0 is falsy)
0 ?? "default";    // 0 ← ?? treats 0 as valid!

null ?? "default";      // "default"
undefined ?? "default"; // "default"
"" ?? "default";        // "" ← ?? treats empty string as valid!
```

## 4.4 Assignment Operators

```js
let x = 10;

x += 5;   // x = x + 5  → 15
x -= 3;   // x = x - 3  → 12
x *= 2;   // x = x * 2  → 24
x /= 4;   // x = x / 4  → 6
x **= 2;  // x = x ** 2 → 36
x %= 5;   // x = x % 5  → 1

// Logical assignment (ES2021)
let a = null;
a ??= "default";   // a = "default" (assigned because a was null)

let b = 0;
b ||= 42;   // b = 42 (assigned because 0 is falsy)

let c = 5;
c &&= 10;   // c = 10 (assigned because 5 is truthy)
```

## 4.5 Ternary Operator

A shorthand for if-else in a single line.

```js
// Syntax: condition ? valueIfTrue : valueIfFalse
let age = 20;
let status = age >= 18 ? "adult" : "minor";
console.log(status); // "adult"

// Real-world examples
const greeting = isLoggedIn ? "Welcome back!" : "Please log in";
const buttonText = isLoading ? "Loading..." : "Submit";
const cssClass = isActive ? "btn-active" : "btn-inactive";

// Can be nested (but keep it readable — use if-else for complex logic)
let score = 75;
let grade = score >= 90 ? "A"
          : score >= 80 ? "B"
          : score >= 70 ? "C"
          : score >= 60 ? "D"
          : "F";
// grade = "C"
```

---

# 5. Strings in Detail

## 5.1 Creating Strings

```js
// Three ways to create strings
let single = 'Hello, World!';
let double = "Hello, World!";
let template = `Hello, World!`;  // template literals — most powerful

// Escaping special characters
let quote = "She said \"hello\"";   // escape double quotes
let apostrophe = 'It\'s a bird';    // escape single quote
let backslash = "C:\\Users\\Alice"; // escape backslash
let newline = "Line 1\nLine 2";     // \n = new line
let tab = "Name:\tAlice";           // \t = tab
```

## 5.2 Template Literals — The Modern Way

```js
const name = "Alice";
const age = 25;
const city = "Mumbai";

// Old way — concatenation (messy)
const msg1 = "Hello, my name is " + name + ". I am " + age + " years old.";

// Modern way — template literals (clean)
const msg2 = `Hello, my name is ${name}. I am ${age} years old.`;

// Expressions inside ${}
const a = 5, b = 3;
console.log(`${a} + ${b} = ${a + b}`);       // "5 + 3 = 8"
console.log(`${a > b ? "a" : "b"} is bigger`); // "a is bigger"

// Multi-line strings
const html = `
  <div class="card">
    <h2>${name}</h2>
    <p>Age: ${age}</p>
    <p>City: ${city}</p>
  </div>
`;

// Function calls inside ${}
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}
console.log(`Hello, ${capitalize("alice")}!`); // "Hello, Alice!"
```

## 5.3 String Properties & Methods

```js
const str = "Hello, World!";

// Length
str.length;  // 13

// Access characters (0-indexed)
str[0];           // "H"
str[7];           // "W"
str.charAt(0);    // "H" (older method, same result)
str[str.length - 1]; // "!" (last character)

// Case
"hello".toUpperCase();  // "HELLO"
"HELLO".toLowerCase();  // "hello"

// Search / Find
str.indexOf("World");      // 7  (position where it starts, -1 if not found)
str.indexOf("xyz");        // -1 (not found)
str.includes("World");     // true  ← cleaner than indexOf
str.includes("xyz");       // false
str.startsWith("Hello");   // true
str.endsWith("!");         // true
str.search(/world/i);      // 7 (regex search, case-insensitive)

// Extraction
str.slice(0, 5);     // "Hello" (from index 0 to 5, not including 5)
str.slice(7);        // "World!" (from index 7 to end)
str.slice(-6);       // "orld!" (negative = from end)
str.substring(7, 12); // "World" (similar to slice, no negative indexes)

// Splitting
"a,b,c".split(",");          // ["a", "b", "c"]
"hello".split("");            // ["h", "e", "l", "l", "o"]
"one two three".split(" ");  // ["one", "two", "three"]
"hello world".split(" ", 1); // ["hello"] (limit results)

// Replacing
"hello world".replace("world", "JS");       // "hello JS" (only first match)
"aabbcc".replace(/a/g, "x");               // "xxbbcc" (global flag = all matches)
"hello world".replaceAll("l", "r");        // "herro worrd"

// Trimming whitespace
"  hello  ".trim();        // "hello"
"  hello  ".trimStart();   // "hello  "
"  hello  ".trimEnd();     // "  hello"

// Padding
"5".padStart(3, "0");    // "005" (useful for IDs, time formatting)
"hi".padEnd(5, ".");     // "hi..."

// Repeating
"ha".repeat(3);  // "hahaha"

// String to Array and back
Array.from("hello");       // ["h", "e", "l", "l", "o"]
["h", "e", "y"].join("");  // "hey"
["a", "b", "c"].join("-"); // "a-b-c"

// Check if string is empty
"".length === 0;           // true
"   ".trim().length === 0; // true (whitespace-only)
```

## 5.4 Real-World String Examples

```js
// Format a phone number
function formatPhone(digits) {
  // "9876543210" → "+91 98765 43210"
  return `+91 ${digits.slice(0, 5)} ${digits.slice(5)}`;
}

// Truncate long text
function truncate(text, maxLength) {
  if (text.length <= maxLength) return text;
  return text.slice(0, maxLength - 3) + "...";
}
truncate("This is a very long sentence", 15); // "This is a ver..."

// Slugify for URLs
function slugify(title) {
  return title
    .toLowerCase()
    .trim()
    .replace(/[^a-z0-9\s-]/g, "")  // remove special chars
    .replace(/\s+/g, "-");          // spaces to hyphens
}
slugify("Hello World! My Post");  // "hello-world-my-post"

// Capitalize first letter of each word
function titleCase(str) {
  return str.split(" ")
    .map(word => word.charAt(0).toUpperCase() + word.slice(1).toLowerCase())
    .join(" ");
}
titleCase("the quick brown fox"); // "The Quick Brown Fox"

// Count occurrences of a character
function countChar(str, char) {
  return str.split(char).length - 1;
}
countChar("banana", "a"); // 3

// Check if palindrome
function isPalindrome(str) {
  const clean = str.toLowerCase().replace(/[^a-z0-9]/g, "");
  return clean === clean.split("").reverse().join("");
}
isPalindrome("racecar"); // true
isPalindrome("A man a plan a canal Panama"); // true
```

---

# 6. Arrays in Detail

## 6.1 What is an Array?

An array is an **ordered list** of values. Think of it like a numbered shelf.

```js
// Creating arrays
const empty = [];
const numbers = [1, 2, 3, 4, 5];
const fruits = ["apple", "banana", "mango"];
const mixed = [1, "hello", true, null, { name: "Alice" }]; // can mix types

// Access by index (starts at 0)
fruits[0];  // "apple"
fruits[1];  // "banana"
fruits[2];  // "mango"
fruits[fruits.length - 1]; // "mango" (last element)

// Modify
fruits[1] = "orange";  // ["apple", "orange", "mango"]

// Length
fruits.length;  // 3
```

## 6.2 Adding & Removing Elements

```js
const arr = [1, 2, 3];

// Add to END
arr.push(4);       // [1, 2, 3, 4] — returns new length
arr.push(5, 6);    // [1, 2, 3, 4, 5, 6]

// Remove from END
arr.pop();         // removes 6, returns 6 — arr = [1, 2, 3, 4, 5]

// Add to BEGINNING
arr.unshift(0);    // [0, 1, 2, 3, 4, 5] — returns new length

// Remove from BEGINNING
arr.shift();       // removes 0, returns 0 — arr = [1, 2, 3, 4, 5]

// Add/Remove at any position — splice(startIndex, deleteCount, ...itemsToAdd)
const colors = ["red", "green", "blue", "yellow"];
colors.splice(1, 0, "purple");  // insert "purple" at index 1, delete 0
// ["red", "purple", "green", "blue", "yellow"]

colors.splice(2, 1);  // delete 1 element at index 2
// ["red", "purple", "blue", "yellow"]

colors.splice(1, 2, "black", "white");  // replace 2 elements at index 1
// ["red", "black", "white", "yellow"]

// Remove element by value
const index = colors.indexOf("black");
if (index !== -1) colors.splice(index, 1);
```

## 6.3 Searching in Arrays

```js
const nums = [10, 20, 30, 40, 50];
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" }
];

// indexOf — finds primitive values
nums.indexOf(30);     // 2
nums.indexOf(99);     // -1 (not found)
nums.lastIndexOf(30); // 2 (search from end)

// includes — check if value exists
nums.includes(30);  // true
nums.includes(99);  // false

// find — find first object matching a condition
const bob = users.find(user => user.name === "Bob");
// { id: 2, name: "Bob" }

// findIndex — find index of first match
const bobIndex = users.findIndex(user => user.name === "Bob"); // 1

// some — does ANY element match?
nums.some(n => n > 40);  // true (50 > 40)
nums.some(n => n > 100); // false

// every — do ALL elements match?
nums.every(n => n > 0);   // true (all positive)
nums.every(n => n > 20);  // false (10 is not > 20)
```

## 6.4 Transforming Arrays (map, filter, reduce)

These three methods are the most important array methods you'll ever use.

```js
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// MAP — transform each element, returns new array of SAME length
const doubled = numbers.map(n => n * 2);
// [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

const products = [
  { name: "Phone", price: 30000 },
  { name: "Laptop", price: 80000 }
];
const names = products.map(p => p.name);    // ["Phone", "Laptop"]
const prices = products.map(p => p.price);  // [30000, 80000]

// FILTER — keep elements that pass a test, returns new SHORTER array
const evens = numbers.filter(n => n % 2 === 0);
// [2, 4, 6, 8, 10]

const expensive = products.filter(p => p.price > 50000);
// [{ name: "Laptop", price: 80000 }]

// REDUCE — boil array down to a single value
// Syntax: array.reduce((accumulator, currentValue) => newAccumulator, initialValue)
const sum = numbers.reduce((total, n) => total + n, 0);
// 0 + 1 = 1 → 1 + 2 = 3 → 3 + 3 = 6 → ... → 55

const totalPrice = products.reduce((total, p) => total + p.price, 0);
// 110000

// Group by category using reduce
const items = [
  { name: "Apple", category: "fruit" },
  { name: "Carrot", category: "vegetable" },
  { name: "Banana", category: "fruit" },
  { name: "Broccoli", category: "vegetable" }
];

const grouped = items.reduce((acc, item) => {
  const key = item.category;
  if (!acc[key]) acc[key] = [];
  acc[key].push(item.name);
  return acc;
}, {});
// { fruit: ["Apple", "Banana"], vegetable: ["Carrot", "Broccoli"] }

// CHAINING — combine multiple methods
const result = numbers
  .filter(n => n % 2 === 0)    // [2, 4, 6, 8, 10]
  .map(n => n * n)              // [4, 16, 36, 64, 100]
  .reduce((sum, n) => sum + n, 0); // 220
```

## 6.5 Other Useful Array Methods

```js
const arr = [3, 1, 4, 1, 5, 9, 2, 6];

// Sort — modifies original array!
[3, 1, 2].sort();          // [1, 2, 3] — works for single-digit numbers
[10, 9, 1].sort();         // [1, 10, 9] ← WRONG! sorts as strings!
[10, 9, 1].sort((a, b) => a - b); // [1, 9, 10] ← correct numeric sort
[10, 9, 1].sort((a, b) => b - a); // [10, 9, 1] ← descending

// Sort objects by property
const people = [
  { name: "Charlie", age: 30 },
  { name: "Alice", age: 25 },
  { name: "Bob", age: 35 }
];
people.sort((a, b) => a.age - b.age);  // sort by age ascending
people.sort((a, b) => a.name.localeCompare(b.name)); // sort by name

// Reverse
[1, 2, 3].reverse();  // [3, 2, 1] — modifies original!

// Slice — extract portion (does NOT modify original)
const fruits = ["apple", "banana", "cherry", "date", "elderberry"];
fruits.slice(1, 3);  // ["banana", "cherry"] (index 1 to 3, not including 3)
fruits.slice(2);     // ["cherry", "date", "elderberry"]
fruits.slice(-2);    // ["date", "elderberry"] (last 2 elements)

// Concat — join arrays
[1, 2].concat([3, 4]);      // [1, 2, 3, 4]
[1, 2].concat([3], [4, 5]); // [1, 2, 3, 4, 5]

// Flat — flatten nested arrays
[1, [2, 3], [4, [5, 6]]].flat();    // [1, 2, 3, 4, [5, 6]] — 1 level deep
[1, [2, 3], [4, [5, 6]]].flat(2);   // [1, 2, 3, 4, 5, 6]   — 2 levels deep
[1, [2, [3, [4]]]].flat(Infinity);  // [1, 2, 3, 4] — all levels

// flatMap — map then flatten (1 level)
["hello world", "how are you"].flatMap(s => s.split(" "));
// ["hello", "world", "how", "are", "you"]

// Remove duplicates using Set
const withDups = [1, 2, 2, 3, 3, 3, 4];
const unique = [...new Set(withDups)]; // [1, 2, 3, 4]

// Fill
new Array(5).fill(0);       // [0, 0, 0, 0, 0]
[1, 2, 3, 4, 5].fill(0, 2, 4); // [1, 2, 0, 0, 5]
```

---

# 7. Objects in Detail

## 7.1 What is an Object?

An object is a collection of **key-value pairs**. Think of it like a real-world object with properties.

```js
// Creating an object
const person = {
  // key: value
  name: "Alice",
  age: 25,
  city: "Mumbai",
  isStudent: false,
  hobbies: ["reading", "coding"],  // value can be any type
  address: {                       // nested object
    street: "123 Main St",
    pincode: "400001"
  },
  greet: function() {              // method (function as value)
    return `Hi, I'm ${this.name}`;
  }
};

// Accessing properties
person.name;           // "Alice" — dot notation
person["age"];         // 25 — bracket notation
person["city"];        // "Mumbai"

// When to use bracket notation:
const key = "name";
person[key];           // "Alice" — dynamic key access!
person["first name"];  // if key has spaces (avoid spaces in keys)

// Modify
person.age = 26;
person["city"] = "Delhi";

// Add new property
person.email = "alice@example.com";

// Delete property
delete person.isStudent;

// Check if property exists
"name" in person;          // true
"salary" in person;        // false
person.hasOwnProperty("name"); // true
person.salary !== undefined;   // true (less reliable — can be explicitly set to undefined)
```

## 7.2 Object Methods

```js
const car = { brand: "Toyota", model: "Camry", year: 2023, price: 3000000 };

// Get all keys
Object.keys(car);    // ["brand", "model", "year", "price"]

// Get all values
Object.values(car);  // ["Toyota", "Camry", 2023, 3000000]

// Get key-value pairs
Object.entries(car);
// [["brand", "Toyota"], ["model", "Camry"], ["year", 2023], ["price", 3000000]]

// Loop through entries
for (const [key, value] of Object.entries(car)) {
  console.log(`${key}: ${value}`);
}

// Merge objects
const defaults = { color: "white", seats: 5, aircon: true };
const custom = { color: "black", seats: 7 };
const merged = { ...defaults, ...custom };
// { color: "black", seats: 7, aircon: true } — custom overrides defaults

// Also Object.assign
const merged2 = Object.assign({}, defaults, custom);

// Freeze — prevent modifications
const config = Object.freeze({ apiUrl: "https://api.example.com", timeout: 5000 });
config.apiUrl = "different";  // silently fails (or errors in strict mode)
config.apiUrl;  // still "https://api.example.com"

// Create from entries
const entries = [["name", "Alice"], ["age", 25]];
const obj = Object.fromEntries(entries); // { name: "Alice", age: 25 }

// Useful: convert Map to object
const map = new Map([["a", 1], ["b", 2]]);
const objFromMap = Object.fromEntries(map); // { a: 1, b: 2 }
```

## 7.3 Object Shorthand & Computed Keys

```js
// Property shorthand — when key = variable name
const name = "Alice";
const age = 25;

// Old way
const old = { name: name, age: age };

// Modern shorthand
const modern = { name, age };  // same result!

// Method shorthand
const calculator = {
  // Old way
  add: function(a, b) { return a + b; },

  // Modern shorthand
  subtract(a, b) { return a - b; },
  multiply(a, b) { return a * b; }
};

// Computed property names (dynamic keys)
const field = "name";
const user = {
  [field]: "Alice",           // key is the VALUE of field variable
  [`${field}_length`]: 5      // "name_length": 5
};
// { name: "Alice", name_length: 5 }

// Real use: creating objects from form inputs
function createUser(fields) {
  const user = {};
  fields.forEach(({ key, value }) => {
    user[key] = value;  // dynamic key!
  });
  return user;
}
```

---

# 8. Conditional Statements

## 8.1 if / else if / else

```js
const score = 75;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else if (score >= 70) {
  console.log("Grade: C");
} else if (score >= 60) {
  console.log("Grade: D");
} else {
  console.log("Grade: F");
}
// Output: "Grade: C"

// Single line (no braces needed for one statement — but braces are safer)
if (isLoggedIn) showDashboard();

// Nested conditions
const age = 20;
const hasID = true;

if (age >= 18) {
  if (hasID) {
    console.log("Can enter the club");
  } else {
    console.log("Need ID to enter");
  }
} else {
  console.log("Too young to enter");
}

// Better: use && to combine conditions
if (age >= 18 && hasID) {
  console.log("Can enter");
} else if (age < 18) {
  console.log("Too young");
} else {
  console.log("Need ID");
}
```

## 8.2 switch Statement

```js
const day = "Monday";

switch (day) {
  case "Monday":
  case "Tuesday":
  case "Wednesday":
  case "Thursday":
  case "Friday":
    console.log("Weekday");
    break;  // IMPORTANT — without break, falls through to next case!
  case "Saturday":
  case "Sunday":
    console.log("Weekend!");
    break;
  default:
    console.log("Invalid day");
}

// switch with return (in a function — no break needed)
function getDayType(day) {
  switch (day) {
    case "Saturday":
    case "Sunday":
      return "Weekend";
    default:
      return "Weekday";
  }
}

// Real-world example
function getStatusMessage(statusCode) {
  switch (statusCode) {
    case 200: return "OK";
    case 201: return "Created";
    case 400: return "Bad Request";
    case 401: return "Unauthorized";
    case 404: return "Not Found";
    case 500: return "Internal Server Error";
    default: return "Unknown Status";
  }
}
```

## 8.3 Truthy & Falsy Values

Understanding what evaluates to true or false is crucial.

```js
// FALSY values — these are all treated as false in conditions
if (false)     { } // false
if (0)         { } // zero
if (-0)        { } // negative zero
if ("")        { } // empty string
if (null)      { } // null
if (undefined) { } // undefined
if (NaN)       { } // NaN

// TRUTHY — everything else is true!
if (true)      { } // true
if (1)         { } // any non-zero number
if (-1)        { }
if ("hello")   { } // any non-empty string
if ("0")       { } // even "0" is truthy!
if ([])        { } // empty array is truthy!
if ({})        { } // empty object is truthy!

// Real-world pattern
function displayUser(user) {
  if (!user) {
    return "No user found";  // handles null, undefined, 0, ""
  }
  return `Hello, ${user.name}`;
}

// Common mistake
const items = [];
if (items) {  // ← this is TRUE! [] is truthy
  console.log("Has items");  // prints this even for empty array!
}

// Correct check for array content
if (items.length > 0) {
  console.log("Has items");
}
// or
if (items.length) {  // 0 is falsy, any other number is truthy
  console.log("Has items");
}
```

---

# 9. Loops

## 9.1 for Loop

```js
// Classic for loop — use when you know number of iterations
for (let i = 0; i < 5; i++) {
  console.log(i);  // 0, 1, 2, 3, 4
}

// Loop through array by index
const fruits = ["apple", "banana", "mango"];
for (let i = 0; i < fruits.length; i++) {
  console.log(`${i}: ${fruits[i]}`);
  // 0: apple, 1: banana, 2: mango
}

// Count down
for (let i = 10; i > 0; i--) {
  console.log(i);  // 10, 9, 8 ... 1
}

// Loop with step
for (let i = 0; i <= 20; i += 5) {
  console.log(i);  // 0, 5, 10, 15, 20
}
```

## 9.2 while Loop

```js
// while — use when you don't know number of iterations
let count = 0;
while (count < 5) {
  console.log(count);
  count++;  // ALWAYS update the condition, or you'll get infinite loop!
}

// Real-world: retry until success
async function fetchWithRetry(url, maxRetries = 3) {
  let attempts = 0;
  while (attempts < maxRetries) {
    try {
      const response = await fetch(url);
      if (response.ok) return response.json();
      attempts++;
    } catch (err) {
      attempts++;
      if (attempts === maxRetries) throw err;
      await sleep(1000 * attempts); // wait longer each retry
    }
  }
}

// do...while — runs at LEAST once
let userInput;
do {
  userInput = prompt("Enter a number between 1 and 10:");
} while (userInput < 1 || userInput > 10);
```

## 9.3 for...of — Looping Arrays

```js
// for...of — cleanest way to loop arrays
const fruits = ["apple", "banana", "mango"];

for (const fruit of fruits) {
  console.log(fruit);  // apple, banana, mango
}

// With index using entries()
for (const [index, fruit] of fruits.entries()) {
  console.log(`${index}: ${fruit}`);
}

// Works on strings too
for (const char of "hello") {
  console.log(char);  // h, e, l, l, o
}

// Works on Map and Set
const set = new Set([1, 2, 3]);
for (const value of set) {
  console.log(value);
}
```

## 9.4 for...in — Looping Objects

```js
const person = { name: "Alice", age: 25, city: "Mumbai" };

for (const key in person) {
  console.log(`${key}: ${person[key]}`);
  // name: Alice
  // age: 25
  // city: Mumbai
}

// NOTE: for...in also iterates over inherited properties
// Prefer Object.keys() for safer iteration:
for (const key of Object.keys(person)) {
  console.log(key);
}
```

## 9.5 break & continue

```js
// break — exit the loop entirely
for (let i = 0; i < 10; i++) {
  if (i === 5) break;  // stop when i reaches 5
  console.log(i);  // 0, 1, 2, 3, 4
}

// continue — skip current iteration, continue with next
for (let i = 0; i < 10; i++) {
  if (i % 2 === 0) continue;  // skip even numbers
  console.log(i);  // 1, 3, 5, 7, 9
}

// Real-world: find first match and stop
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" }
];

let found = null;
for (const user of users) {
  if (user.name === "Bob") {
    found = user;
    break;  // no need to keep looking
  }
}
// (use Array.find() instead — this is just to demonstrate break)
```

---

# 10. Functions

## 10.1 Function Declaration

```js
// Function declaration — hoisted (can call before it's defined)
greet("Alice");  // works! function declarations are hoisted

function greet(name) {
  return `Hello, ${name}!`;
}

// Parameters vs Arguments
// Parameters = placeholders in function definition
// Arguments = actual values passed when calling

function add(a, b) {   // a and b are PARAMETERS
  return a + b;
}
add(3, 5);  // 3 and 5 are ARGUMENTS → returns 8

// Default parameters
function greet(name = "World", greeting = "Hello") {
  return `${greeting}, ${name}!`;
}
greet();             // "Hello, World!"
greet("Alice");      // "Hello, Alice!"
greet("Bob", "Hi");  // "Hi, Bob!"
```

## 10.2 Function Expression

```js
// Function expression — NOT hoisted (must define before calling)
const greet = function(name) {
  return `Hello, ${name}!`;
};
greet("Alice");  // "Hello, Alice!"

// Named function expression (name only accessible inside the function)
const factorial = function fact(n) {
  if (n <= 1) return 1;
  return n * fact(n - 1);  // can use 'fact' inside
};
factorial(5);  // 120
```

## 10.3 Arrow Functions

```js
// Arrow function — concise syntax introduced in ES6
// Syntax: (parameters) => { body }

// Full syntax
const add = (a, b) => {
  return a + b;
};

// Implicit return (no braces needed for single expression)
const add = (a, b) => a + b;

// Single parameter (no parentheses needed)
const double = n => n * 2;
const square = n => n * n;

// No parameters
const greet = () => "Hello!";
const getTime = () => new Date().toISOString();

// Return an object (wrap in parentheses!)
const createUser = (name, age) => ({ name, age });
// WITHOUT parentheses, {} would be treated as function body!

// Arrow functions in array methods — very common!
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);
```

**Important: Arrow functions have no `this`** (more on this in section 13)

## 10.4 Rest Parameters & Arguments Object

```js
// Rest parameters — collect remaining arguments into an array
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3);        // 6
sum(1, 2, 3, 4, 5);  // 15
sum();               // 0

// Mix of fixed and rest params
function logMessage(level, ...messages) {
  console.log(`[${level}]`, ...messages);
}
logMessage("INFO", "User logged in", "ID:", 123);
// [INFO] User logged in ID: 123

// Arguments object (old way — only in regular functions, not arrows)
function oldSum() {
  let total = 0;
  for (let i = 0; i < arguments.length; i++) {
    total += arguments[i];
  }
  return total;
}
// Use rest parameters instead — much cleaner
```

## 10.5 Higher-Order Functions

A function that takes a function as argument or returns a function.

```js
// Function that takes a function as argument
function doTwice(fn, value) {
  return fn(fn(value));
}

const double = x => x * 2;
doTwice(double, 3);  // double(double(3)) = double(6) = 12

// Function that returns a function
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const triple = multiplier(3);
const quadruple = multiplier(4);

triple(5);    // 15
quadruple(5); // 20

// Real-world: event handler factory
function createLogger(prefix) {
  return function(message) {
    console.log(`[${prefix}] ${message}`);
  };
}

const logError = createLogger("ERROR");
const logInfo = createLogger("INFO");

logError("Database connection failed");  // [ERROR] Database connection failed
logInfo("Server started on port 3000");  // [INFO] Server started on port 3000
```

## 10.6 Pure Functions

A pure function always returns the same output for the same input and has no side effects.

```js
// ❌ Impure — depends on external state, has side effects
let total = 0;
function addToTotal(amount) {
  total += amount;  // modifies external variable — side effect!
  return total;
}
addToTotal(10);  // 10
addToTotal(10);  // 20 — different result for same input!

// ✅ Pure — same input, same output, no side effects
function add(a, b) {
  return a + b;  // no external state, no side effects
}
add(5, 10);  // always 15

// ❌ Impure — modifies the input array (mutation)
function addItem(arr, item) {
  arr.push(item);  // modifies original!
  return arr;
}

// ✅ Pure — creates new array
function addItem(arr, item) {
  return [...arr, item];  // returns new array
}
```

---

# 11. Scope & Hoisting

## 11.1 Scope

Scope determines where a variable is accessible.

```js
// Global scope — accessible everywhere
const globalVar = "I'm global";

function outer() {
  // Function scope — accessible inside this function and its children
  const outerVar = "I'm in outer";

  function inner() {
    // Block scope
    const innerVar = "I'm in inner";

    console.log(globalVar);  // ✅ accessible (parent scope)
    console.log(outerVar);   // ✅ accessible (parent scope)
    console.log(innerVar);   // ✅ accessible (own scope)
  }

  inner();
  // console.log(innerVar);  // ❌ not accessible (child scope)
}

// console.log(outerVar);  // ❌ not accessible
```

**Scope chain:** JavaScript looks up the scope chain to find variables — from innermost to outermost.

```js
const x = "global";

function outer() {
  const x = "outer";  // shadows global x

  function inner() {
    // No local x — looks up scope chain → finds outer's x
    console.log(x);  // "outer"
  }
  inner();
}

function another() {
  // No local x — looks up scope chain → finds global x
  console.log(x);  // "global"
}
```

## 11.2 Hoisting

JavaScript "hoists" (moves) declarations to the top of their scope before executing.

```js
// Function declarations — FULLY hoisted (can use before declaration)
sayHello();  // ✅ Works!
function sayHello() {
  console.log("Hello!");
}

// var — declaration hoisted, but NOT initialization
console.log(name);  // undefined (not an error, but confusing!)
var name = "Alice";
console.log(name);  // "Alice"
// This is what actually happens:
// var name;           ← hoisted to top
// console.log(name);  ← undefined
// name = "Alice";
// console.log(name);  ← "Alice"

// let and const — hoisted but NOT initialized (Temporal Dead Zone)
console.log(age);  // ❌ ReferenceError: Cannot access 'age' before initialization
let age = 25;
// let/const are hoisted but you can't access them before declaration
// The period before declaration = Temporal Dead Zone (TDZ)

// Function expressions — NOT hoisted
sayBye();  // ❌ TypeError: sayBye is not a function
const sayBye = function() {
  console.log("Bye!");
};
```

---

# 12. Closures

## 12.1 What is a Closure?

A closure is a function that **remembers** variables from its outer scope even after that outer function has finished running.

```js
function outer() {
  const message = "Hello";  // local variable in outer

  function inner() {
    console.log(message);  // inner "closes over" message
  }

  return inner;  // return the inner function
}

const sayHello = outer();  // outer() finishes running, message "should be gone"
sayHello();  // "Hello" — but it's NOT gone! closure preserved it ✅
```

## 12.2 Why Closures are Useful

```js
// 1. Data Privacy — create private variables
function createCounter() {
  let count = 0;  // count is private — only accessible through returned functions

  return {
    increment() { count++; },
    decrement() { count--; },
    getCount()  { return count; },
    reset()     { count = 0; }
  };
}

const counter = createCounter();
counter.increment(); // count = 1
counter.increment(); // count = 2
counter.increment(); // count = 3
counter.decrement(); // count = 2
counter.getCount();  // 2
// counter.count;    // undefined — can't access count directly!

// 2. Function factories — create specialized functions
function createMultiplier(factor) {
  return (number) => number * factor;  // closes over factor
}

const double = createMultiplier(2);
const triple = createMultiplier(3);
const times10 = createMultiplier(10);

double(5);  // 10
triple(5);  // 15
times10(5); // 50

// 3. Memoization — cache results
function memoize(fn) {
  const cache = {};  // closed over by returned function
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache[key] !== undefined) {
      console.log("Cache hit!");
      return cache[key];
    }
    cache[key] = fn(...args);
    return cache[key];
  };
}

const slowSquare = (n) => {
  // Simulate slow computation
  let result = 0;
  for (let i = 0; i < 1000000; i++) result += n;
  return result;
};

const fastSquare = memoize(slowSquare);
fastSquare(5);  // calculates and caches
fastSquare(5);  // "Cache hit!" — instantly returns cached result
fastSquare(10); // calculates for new arg
```

## 12.3 Common Closure Mistake

```js
// Classic loop closure bug
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// Expected: 0, 1, 2
// Actual: 3, 3, 3  ← all closures share the SAME i (var is function-scoped)

// Fix 1: Use let (block-scoped — each iteration gets its own i)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// 0, 1, 2 ✅

// Fix 2: IIFE (Immediately Invoked Function Expression)
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(() => console.log(j), 1000);
  })(i);  // capture i as j immediately
}
// 0, 1, 2 ✅
```

---

# 13. The `this` Keyword

## 13.1 What is `this`?

`this` refers to the object that is calling the function. It changes depending on how the function is called.

```js
// 1. Global context — this = window (browser) or global (Node)
console.log(this);  // Window object in browser

// 2. Inside a method — this = the object the method belongs to
const person = {
  name: "Alice",
  greet() {
    console.log(this.name);  // "Alice" — this = person object
  }
};
person.greet();  // "Alice"

// 3. Function (non-strict mode) — this = global object
function showThis() {
  console.log(this);  // Window object
}

// 4. Arrow functions — NO own this, inherits from surrounding scope
const obj = {
  name: "Alice",
  regular: function() {
    console.log(this.name);  // "Alice" — this = obj
  },
  arrow: () => {
    console.log(this.name);  // undefined — this = outer scope (global), not obj!
  }
};

// 5. Constructor (with new) — this = newly created object
function Person(name, age) {
  this.name = name;  // this = new object being created
  this.age = age;
}
const alice = new Person("Alice", 25);
alice.name;  // "Alice"
```

## 13.2 Losing `this` — A Common Bug

```js
const user = {
  name: "Alice",
  greet() {
    console.log(`Hello, ${this.name}`);
  }
};

user.greet();  // "Hello, Alice" ✅

// Problem: this is lost when method is detached from object
const greetFn = user.greet;  // detach the method
greetFn();  // "Hello, undefined" ❌ — this = global, not user

// Fix 1: bind() — permanently bind this
const boundGreet = user.greet.bind(user);
boundGreet();  // "Hello, Alice" ✅

// Fix 2: Arrow function wrapper
const arrowGreet = () => user.greet();
arrowGreet();  // "Hello, Alice" ✅

// Fix 3: call() — call with specific this, one time
user.greet.call({ name: "Bob" });  // "Hello, Bob"

// Fix 4: apply() — same as call, but args as array
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}
greet.apply({ name: "Alice" }, ["Hello", "!"]);  // "Hello, Alice!"
greet.call({ name: "Alice" }, "Hello", "!");      // "Hello, Alice!"
```

---

# 14. Prototypes & Inheritance

## 14.1 What is a Prototype?

Every object in JavaScript has a hidden link to another object called its **prototype**. When you access a property that doesn't exist on an object, JS looks up the prototype chain.

```js
const arr = [1, 2, 3];
// arr has no .map() method directly
// but arr's prototype (Array.prototype) does!
arr.map(n => n * 2);  // works because of prototype lookup

// The chain:
// arr → Array.prototype → Object.prototype → null

// You can see it:
Object.getPrototypeOf(arr) === Array.prototype;  // true
```

## 14.2 Constructor Functions (Pre-ES6 Inheritance)

```js
// Constructor function (function used with 'new')
function Animal(name, sound) {
  this.name = name;
  this.sound = sound;
}

// Add method to prototype (shared by all instances — efficient!)
Animal.prototype.speak = function() {
  return `${this.name} says ${this.sound}!`;
};

Animal.prototype.toString = function() {
  return `Animal(${this.name})`;
};

const dog = new Animal("Dog", "Woof");
const cat = new Animal("Cat", "Meow");

dog.speak();  // "Dog says Woof!"
cat.speak();  // "Cat says Meow!"

// dog and cat share the same speak function (not duplicated)
dog.speak === cat.speak;  // true — same function on prototype

// Inheritance
function Dog(name) {
  Animal.call(this, name, "Woof");  // call parent constructor
  this.type = "dog";
}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.fetch = function() {
  return `${this.name} fetches the ball!`;
};

const rex = new Dog("Rex");
rex.speak();   // "Rex says Woof!" — inherited from Animal
rex.fetch();   // "Rex fetches the ball!" — own method
rex instanceof Dog;    // true
rex instanceof Animal; // true
```

---

# 15. Classes

## 15.1 Class Syntax (ES6)

Classes are "syntactic sugar" over prototype-based inheritance — easier to read and write.

```js
class Animal {
  // Constructor — runs when new Animal() is called
  constructor(name, sound) {
    this.name = name;
    this.sound = sound;
  }

  // Methods — automatically added to prototype
  speak() {
    return `${this.name} says ${this.sound}!`;
  }

  toString() {
    return `Animal(${this.name})`;
  }

  // Static methods — called on class, not instance
  static create(name, sound) {
    return new Animal(name, sound);
  }

  // Getters — access like property, not method call
  get info() {
    return `${this.name} (${this.sound})`;
  }

  // Setters
  set nickname(value) {
    this._nickname = value.trim();
  }

  get nickname() {
    return this._nickname || this.name;
  }
}

const dog = new Animal("Dog", "Woof");
const cat = Animal.create("Cat", "Meow");  // static method

dog.speak();  // "Dog says Woof!"
dog.info;     // "Dog (Woof)" — getter, no ()
dog.nickname = "  Buddy  ";
dog.nickname; // "Buddy" — setter trimmed it
```

## 15.2 Inheritance with extends

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);  // MUST call super() before using this
    this.breed = breed;
  }

  // Override parent method
  speak() {
    return `${this.name} barks!`;
  }

  // Call parent method with super
  introduce() {
    return `${super.speak()} and wags tail`;
  }

  fetch() {
    return `${this.name} fetches!`;
  }
}

class GuideDog extends Dog {
  constructor(name, breed, owner) {
    super(name, breed);
    this.owner = owner;
  }

  guide() {
    return `${this.name} guides ${this.owner}`;
  }
}

const rex = new GuideDog("Rex", "Labrador", "John");
rex.speak();     // "Rex barks!" — from Dog
rex.fetch();     // "Rex fetches!" — from Dog
rex.guide();     // "Rex guides John" — own method
rex.introduce(); // "Rex makes a sound and wags tail" — super method from Animal via Dog

rex instanceof GuideDog; // true
rex instanceof Dog;      // true
rex instanceof Animal;   // true
```

## 15.3 Private Fields (ES2022)

```js
class BankAccount {
  #balance = 0;      // private field — only accessible inside class
  #pin;

  constructor(initialBalance, pin) {
    this.#balance = initialBalance;
    this.#pin = pin;
  }

  deposit(amount) {
    if (amount <= 0) throw new Error("Amount must be positive");
    this.#balance += amount;
    return this;  // return this for chaining
  }

  withdraw(amount, pin) {
    if (pin !== this.#pin) throw new Error("Wrong PIN");
    if (amount > this.#balance) throw new Error("Insufficient funds");
    this.#balance -= amount;
    return this;
  }

  get balance() {
    return this.#balance;
  }
}

const account = new BankAccount(1000, "1234");
account.deposit(500).deposit(200);  // method chaining
account.balance;     // 1700
// account.#balance; // ❌ SyntaxError — private!
account.withdraw(200, "1234");
account.balance;     // 1500
```

---

# 16. Error Handling

## 16.1 try / catch / finally

```js
// Basic error handling
try {
  // Code that might throw an error
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  // Handle the error
  console.error("Something went wrong:", error.message);
} finally {
  // Always runs — cleanup code goes here
  console.log("This always runs");
}

// Real-world example
function parseJSON(jsonString) {
  try {
    const data = JSON.parse(jsonString);  // throws if invalid JSON
    return { success: true, data };
  } catch (error) {
    return { success: false, error: error.message };
  }
}

parseJSON('{"name": "Alice"}');  // { success: true, data: { name: "Alice" } }
parseJSON("invalid json");        // { success: false, error: "Unexpected token i..." }
```

## 16.2 Throwing Errors

```js
// Throw built-in errors
throw new Error("Something went wrong");
throw new TypeError("Expected a string");
throw new RangeError("Value out of range");
throw new SyntaxError("Invalid syntax");

// Custom error classes
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = "ValidationError";
    this.field = field;
  }
}

class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = "NetworkError";
    this.statusCode = statusCode;
  }
}

// Using custom errors
function validateEmail(email) {
  if (!email.includes("@")) {
    throw new ValidationError("Invalid email address", "email");
  }
  return true;
}

try {
  validateEmail("notanemail");
} catch (error) {
  if (error instanceof ValidationError) {
    console.log(`Field ${error.field}: ${error.message}`);
    // Field email: Invalid email address
  } else {
    console.error("Unexpected error:", error);
  }
}
```

---

# 17. Destructuring

## 17.1 Array Destructuring

```js
// Extract values from arrays
const colors = ["red", "green", "blue"];

// Old way
const first = colors[0];
const second = colors[1];

// Destructuring
const [first, second, third] = colors;
// first = "red", second = "green", third = "blue"

// Skip elements
const [, , blue] = colors;  // blue = "blue"

// Default values
const [a, b, c, d = "purple"] = colors;
// d = "purple" (wasn't in array)

// Swap variables
let x = 1, y = 2;
[x, y] = [y, x];  // x = 2, y = 1

// Rest element
const [head, ...tail] = [1, 2, 3, 4, 5];
// head = 1, tail = [2, 3, 4, 5]

// From function return
function getCoordinates() {
  return [40.7128, -74.0060];
}
const [latitude, longitude] = getCoordinates();
```

## 17.2 Object Destructuring

```js
const user = {
  id: 1,
  name: "Alice",
  age: 25,
  email: "alice@example.com",
  address: {
    city: "Mumbai",
    country: "India"
  }
};

// Basic destructuring
const { name, age, email } = user;

// Rename while destructuring
const { name: userName, email: userEmail } = user;
// userName = "Alice", userEmail = "alice@example.com"

// Default values
const { name, salary = 50000 } = user;
// salary = 50000 (wasn't in user)

// Nested destructuring
const { address: { city, country } } = user;
// city = "Mumbai", country = "India"

// Rest in objects
const { id, name, ...rest } = user;
// id = 1, name = "Alice"
// rest = { age: 25, email: "...", address: {...} }

// In function parameters — very common in React!
function displayUser({ name, age, email = "N/A" }) {
  console.log(`${name} (${age}) — ${email}`);
}
displayUser(user);  // "Alice (25) — alice@example.com"

// Array of objects
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" }
];

for (const { id, name } of users) {
  console.log(`${id}: ${name}`);
}
```

---

# 18. Spread & Rest

## 18.1 Spread Operator (...)

Spread expands an iterable (array, object, string) into individual elements.

```js
// Spread in arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

const combined = [...arr1, ...arr2];    // [1, 2, 3, 4, 5, 6]
const withExtra = [0, ...arr1, 3.5, ...arr2, 7];

// Copy an array (shallow copy)
const original = [1, 2, 3];
const copy = [...original];
copy.push(4);
console.log(original);  // [1, 2, 3] — not modified!

// Spread in objects
const defaults = { theme: "light", language: "en", notifications: true };
const userPrefs = { theme: "dark", fontSize: 16 };

const settings = { ...defaults, ...userPrefs };
// { theme: "dark", language: "en", notifications: true, fontSize: 16 }
// userPrefs.theme overrides defaults.theme

// Copy an object
const userCopy = { ...user };

// Spread with function calls
function sum(a, b, c) { return a + b + c; }
const nums = [1, 2, 3];
sum(...nums);  // same as sum(1, 2, 3)

// Spread a string
[..."hello"];  // ["h", "e", "l", "l", "o"]

// Real-world: immutable updates (common in React)
const state = { count: 0, name: "Alice", isLoading: false };

// Update one property without modifying original
const newState = { ...state, count: state.count + 1 };
// { count: 1, name: "Alice", isLoading: false }
```

## 18.2 Rest Parameters

Rest collects multiple arguments into an array. Opposite of spread.

```js
// Rest in function parameters
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3, 4, 5);  // 15

// Mix of regular and rest
function log(level, timestamp, ...messages) {
  console.log(`[${level}] [${timestamp}]`, ...messages);
}
log("INFO", "2024-01-01", "Server started", "on port", 3000);

// Rest in destructuring
const [first, second, ...remaining] = [1, 2, 3, 4, 5];
// first = 1, second = 2, remaining = [3, 4, 5]

const { a, b, ...others } = { a: 1, b: 2, c: 3, d: 4 };
// a = 1, b = 2, others = { c: 3, d: 4 }
```

---

# 19. ES6+ Modern Features

## 19.1 Optional Chaining (?.)

Access nested properties safely without checking each level.

```js
const user = {
  name: "Alice",
  address: {
    city: "Mumbai"
  }
};

const noAddress = { name: "Bob" };

// Old way — verbose and error-prone
const city = user.address && user.address.city;        // "Mumbai"
const city2 = noAddress.address && noAddress.address.city;  // undefined (no error)

// Optional chaining — clean!
user?.address?.city;          // "Mumbai"
noAddress?.address?.city;     // undefined (no error!)
null?.anything?.deeply;       // undefined — safely returns undefined

// With methods
user?.getProfile?.();         // calls if getProfile exists, else undefined
user?.hobbies?.includes("coding");  // works if hobbies is array

// With arrays
const users = [{ name: "Alice" }];
users?.[0]?.name;   // "Alice"
users?.[5]?.name;   // undefined (index doesn't exist)

// Real-world
function displayCity(user) {
  return user?.address?.city ?? "City not provided";
}
```

## 19.2 Nullish Coalescing (??)

Provide default only when value is null or undefined (not 0 or "").

```js
const age = 0;
const name = "";

// || includes 0 and "" as falsy (often wrong!)
age || 18;     // 18 — WRONG! 0 is valid age
name || "Anonymous"; // "Anonymous" — might be wrong for empty string

// ?? only triggers for null/undefined
age ?? 18;     // 0 — correct!
name ?? "Anonymous";  // "" — correct!

null ?? "default";      // "default"
undefined ?? "default"; // "default"
0 ?? "default";         // 0
"" ?? "default";        // ""
false ?? "default";     // false
```

## 19.3 Map & Set

```js
// Map — like object but keys can be ANY type, maintains insertion order
const map = new Map();

map.set("name", "Alice");    // string key
map.set(1, "number key");    // number key
map.set(true, "bool key");   // boolean key
const objKey = {};
map.set(objKey, "object as key!");

map.get("name");    // "Alice"
map.get(1);         // "number key"
map.has("name");    // true
map.size;           // 4

map.delete("name");
map.clear();

// Iterating a Map
const scores = new Map([
  ["Alice", 95],
  ["Bob", 87],
  ["Charlie", 92]
]);

for (const [name, score] of scores) {
  console.log(`${name}: ${score}`);
}

scores.forEach((score, name) => console.log(`${name}: ${score}`));

// Convert to array
[...scores.keys()];    // ["Alice", "Bob", "Charlie"]
[...scores.values()];  // [95, 87, 92]
[...scores.entries()]; // [["Alice", 95], ["Bob", 87], ...]

// Set — collection of UNIQUE values
const set = new Set([1, 2, 2, 3, 3, 3, 4]);
// {1, 2, 3, 4} — duplicates automatically removed!

set.add(5);
set.add(2);  // already exists — ignored
set.has(3);  // true
set.size;    // 5
set.delete(3);

for (const value of set) {
  console.log(value);
}

// Most common use — remove duplicates from array
const withDups = [1, 2, 2, 3, 3, 3, 4];
const unique = [...new Set(withDups)];  // [1, 2, 3, 4]
```

## 19.4 Symbol

```js
// Symbols are unique identifiers
const id1 = Symbol("id");
const id2 = Symbol("id");
id1 === id2;  // false — every Symbol is unique!

// Use as unique object keys (won't clash with other keys)
const USER_ID = Symbol("userId");
const user = {
  name: "Alice",
  [USER_ID]: 12345  // private-ish key
};

user[USER_ID];  // 12345
// Symbols don't show in for...in or Object.keys
Object.keys(user);  // ["name"] — Symbol not included

// Well-known symbols (advanced)
class MyArray {
  [Symbol.iterator]() {
    // Make object iterable with for...of
  }
}
```

## 19.5 Generators

```js
// Generator — function that can pause and resume
function* count() {
  yield 1;  // pause here
  yield 2;  // pause here
  yield 3;  // pause here
}

const counter = count();
counter.next();  // { value: 1, done: false }
counter.next();  // { value: 2, done: false }
counter.next();  // { value: 3, done: false }
counter.next();  // { value: undefined, done: true }

// Infinite sequence
function* naturals() {
  let n = 1;
  while (true) {
    yield n++;  // yields 1, 2, 3, 4, ... forever
  }
}

const nums = naturals();
nums.next().value;  // 1
nums.next().value;  // 2
nums.next().value;  // 3

// Use for...of (with break to avoid infinite loop!)
for (const n of naturals()) {
  if (n > 5) break;
  console.log(n);  // 1, 2, 3, 4, 5
}
```

---

# 20. How JavaScript Runs — The Engine

## 20.1 Parsing & Compilation

When JavaScript runs, here's what happens internally:

```
Your Code (text)
      │
   Parsing
      │
  AST (Abstract Syntax Tree)
  — represents your code as a tree structure
      │
  Interpretation / Compilation (JIT)
  — V8 first interprets (fast start)
  — then compiles hot code to machine code (fast execution)
      │
  Machine Code → CPU executes it
```

## 20.2 Memory — Stack vs Heap

```js
// STACK — stores primitives and function calls
// Fast, limited size, automatically managed
let a = 5;      // 5 stored directly on stack
let b = a;      // COPY of 5 stored — a and b are independent
b = 10;
console.log(a); // 5 — a unchanged

// HEAP — stores objects and arrays
// Larger, slower, garbage collected
let obj1 = { name: "Alice" };  // object stored in heap
let obj2 = obj1;               // obj2 points to SAME heap location!
obj2.name = "Bob";
console.log(obj1.name);  // "Bob" — obj1 was changed!

// This is why you need to spread-copy objects
let obj3 = { ...obj1 };  // NEW object in heap
obj3.name = "Charlie";
console.log(obj1.name);  // "Bob" — obj1 unchanged ✅

// GARBAGE COLLECTION
// When no references to an object remain, V8 automatically frees the memory
let temp = { bigData: new Array(1000000) };
temp = null;  // no more reference → garbage collector will free the memory
```

---

# 21. The Event Loop

## 21.1 The Big Picture

JavaScript is **single-threaded** — it can only do ONE thing at a time. But it can handle multiple operations appearing to happen simultaneously using the event loop.

```
           JavaScript Runtime
    ┌──────────────────────────────┐
    │  CALL STACK                  │
    │  (where code executes)       │
    │                              │
    │  ┌──────────────────┐       │
    │  │  greet("Alice")  │       │
    │  ├──────────────────┤       │
    │  │  main()          │       │
    │  └──────────────────┘       │
    └──────────┬───────────────────┘
               │
    ┌──────────▼───────────────────┐
    │  WEB APIs / Node.js APIs     │
    │  (setTimeout, fetch, fs...)  │
    │  These run OUTSIDE JS thread │
    └──────────┬───────────────────┘
               │
    ┌──────────▼───────────────────┐
    │  CALLBACK QUEUE (Macrotask)  │
    │  (setTimeout callbacks)      │
    └──────────┬───────────────────┘
               │
    ┌──────────▼───────────────────┐
    │  MICROTASK QUEUE             │
    │  (Promise .then, async/await)│
    │  Runs BEFORE callback queue! │
    └──────────┬───────────────────┘
               │
    ┌──────────▼───────────────────┐
    │  EVENT LOOP                  │
    │  Moves from queues to stack  │
    │  when stack is empty         │
    └──────────────────────────────┘
```

## 21.2 Execution Order

```js
console.log("1 — sync");  // runs immediately

setTimeout(() => {
  console.log("2 — setTimeout");  // callback queue (macrotask)
}, 0);

Promise.resolve().then(() => {
  console.log("3 — Promise");  // microtask queue
});

queueMicrotask(() => {
  console.log("4 — queueMicrotask");  // microtask queue
});

console.log("5 — sync");

// Output ORDER:
// 1 — sync
// 5 — sync
// 3 — Promise     ← microtasks run BEFORE macrotasks
// 4 — queueMicrotask
// 2 — setTimeout  ← macrotask runs last
```

## 21.3 Why Non-Blocking Matters

```js
// BLOCKING code — nothing else can run during this!
function blockFor3Seconds() {
  const start = Date.now();
  while (Date.now() - start < 3000) {
    // busy loop — blocks EVERYTHING for 3 seconds
  }
  return "done";
}

// ❌ This freezes the entire browser/app for 3 seconds
blockFor3Seconds();
console.log("Can't run until block is done");

// NON-BLOCKING code — schedules work, event loop handles it
setTimeout(() => {
  console.log("3 seconds later");
}, 3000);

console.log("This runs immediately!");  // doesn't wait!
// While waiting, JS can handle other events, clicks, API responses, etc.
```

---

# 22. Callbacks

## 22.1 What is a Callback?

A callback is a function passed as an argument to another function, to be called later.

```js
// Simple callback
function doSomething(callback) {
  console.log("Doing something...");
  callback();  // call the function that was passed in
}

doSomething(function() {
  console.log("Callback called!");
});

// Real-world: setTimeout uses callback
setTimeout(function() {
  console.log("3 seconds passed!");
}, 3000);

// Array methods use callbacks
[1, 2, 3].forEach(function(num) {
  console.log(num);
});
```

## 22.2 Callback Hell (The Problem)

```js
// Scenario: Read file → Parse JSON → Query database → Send email → Log result
// Each step depends on the previous one

readFile("config.json", function(err, data) {
  if (err) return console.error(err);

  parseJSON(data, function(err, config) {
    if (err) return console.error(err);

    queryDatabase(config.dbUrl, function(err, users) {
      if (err) return console.error(err);

      sendEmail(users[0].email, function(err) {
        if (err) return console.error(err);

        logResult("Email sent", function(err) {
          if (err) return console.error(err);
          console.log("All done!");
          // ← deeply nested, hard to read, hard to maintain
        });
      });
    });
  });
});
```

---

# 23. Promises

## 23.1 What is a Promise?

A Promise represents a value that will be available **in the future** (or an error that occurred). It has three states:

```
Promise states:
  PENDING   → waiting, not yet resolved or rejected
  FULFILLED → operation succeeded, has a value
  REJECTED  → operation failed, has an error

Promise is SETTLED when it's either fulfilled or rejected (can't change after)
```

## 23.2 Creating and Using Promises

```js
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
  // Do async work here
  const success = true;

  if (success) {
    resolve("It worked!");   // fulfill with a value
  } else {
    reject(new Error("It failed!"));  // reject with an error
  }
});

// Using a Promise
myPromise
  .then(value => {
    console.log("Success:", value);  // "Success: It worked!"
  })
  .catch(error => {
    console.error("Error:", error.message);
  })
  .finally(() => {
    console.log("Always runs");  // cleanup code
  });

// Promisifying callback-based code
function readFilePromise(path) {
  return new Promise((resolve, reject) => {
    fs.readFile(path, "utf8", (err, data) => {
      if (err) reject(err);
      else resolve(data);
    });
  });
}

// Now use it cleanly
readFilePromise("config.json")
  .then(data => JSON.parse(data))
  .then(config => console.log(config))
  .catch(err => console.error("Failed:", err));
```

## 23.3 Promise Methods

```js
// Promise.all — wait for ALL promises, fails if any fail
const p1 = fetch("/api/users");
const p2 = fetch("/api/products");
const p3 = fetch("/api/orders");

const [users, products, orders] = await Promise.all([p1, p2, p3]);
// All 3 run in PARALLEL — much faster than sequential!

// Promise.allSettled — wait for ALL, get all results regardless
const results = await Promise.allSettled([
  fetch("/api/users"),
  fetch("/api/broken-endpoint"),  // this might fail
  fetch("/api/orders")
]);

results.forEach(result => {
  if (result.status === "fulfilled") {
    console.log("Success:", result.value);
  } else {
    console.log("Failed:", result.reason);
  }
});

// Promise.race — first one to settle wins (success OR failure)
const fastest = await Promise.race([
  fetch("/api/server1/data"),
  fetch("/api/server2/data"),
  new Promise((_, reject) => setTimeout(() => reject(new Error("Timeout")), 5000))
]);

// Promise.any — first one to SUCCEED wins
const data = await Promise.any([
  fetch("/api/replica1/data"),
  fetch("/api/replica2/data"),
  fetch("/api/replica3/data")
]);
// Returns first success, ignores failures unless ALL fail
```

## 23.4 Promise Chaining

```js
// Each .then() returns a new Promise — chain them!
fetch("/api/user/1")
  .then(response => {
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return response.json();  // returns a Promise
  })
  .then(user => {
    console.log("Got user:", user.name);
    return fetch(`/api/orders?userId=${user.id}`);  // returns a Promise
  })
  .then(response => response.json())
  .then(orders => {
    console.log("Orders:", orders.length);
    return orders.filter(o => o.status === "pending");
  })
  .then(pending => {
    console.log("Pending orders:", pending.length);
  })
  .catch(error => {
    // Catches any error from any step above!
    console.error("Error in chain:", error.message);
  });
```

---

# 24. Async / Await

## 24.1 What is Async/Await?

Async/await is **syntactic sugar over Promises** — it makes asynchronous code look synchronous and much easier to read.

```js
// Promise-based (readable, but chains can get long)
function getUser(id) {
  return fetch(`/api/users/${id}`)
    .then(res => res.json())
    .then(user => {
      return fetch(`/api/orders?userId=${user.id}`)
        .then(res => res.json())
        .then(orders => ({ user, orders }));
    });
}

// Async/await — same thing, looks synchronous
async function getUser(id) {
  const userRes = await fetch(`/api/users/${id}`);
  const user = await userRes.json();

  const ordersRes = await fetch(`/api/orders?userId=${user.id}`);
  const orders = await ordersRes.json();

  return { user, orders };  // automatically wrapped in a Promise
}
```

## 24.2 async Functions

```js
// async function ALWAYS returns a Promise
async function hello() {
  return "Hello!";  // wraps in Promise automatically
}

hello();  // Promise { "Hello!" }
hello().then(v => console.log(v));  // "Hello!"
const result = await hello();  // "Hello!"

// await pauses execution until Promise resolves
async function fetchData() {
  console.log("start");
  const data = await someAsyncOperation();  // waits here
  console.log("data ready:", data);          // runs after wait
  console.log("end");
}
```

## 24.3 Error Handling with try/catch

```js
async function getUser(userId) {
  try {
    const response = await fetch(`/api/users/${userId}`);

    if (!response.ok) {
      throw new Error(`HTTP Error: ${response.status}`);
    }

    const user = await response.json();
    return user;
  } catch (error) {
    if (error.name === "TypeError") {
      console.error("Network error — are you offline?");
    } else {
      console.error("Failed to get user:", error.message);
    }
    throw error;  // re-throw so caller knows it failed
  } finally {
    console.log("Request complete");  // cleanup
  }
}
```

## 24.4 Common Async Mistakes

```js
// ❌ MISTAKE 1: Forgetting await
async function bad() {
  const data = fetchData();    // returns Promise, not data!
  console.log(data.name);     // undefined — data is a Promise object
}

// ✅ FIX
async function good() {
  const data = await fetchData();  // wait for the actual data
  console.log(data.name);          // works!
}

// ❌ MISTAKE 2: Sequential when parallel is faster
async function slow() {
  const users = await fetchUsers();      // 1 second
  const products = await fetchProducts();  // 1 second
  // Total: 2 seconds — but these don't depend on each other!
}

// ✅ FIX: Run in parallel with Promise.all
async function fast() {
  const [users, products] = await Promise.all([
    fetchUsers(),
    fetchProducts()
  ]);
  // Total: ~1 second (run simultaneously)
}

// ❌ MISTAKE 3: await inside forEach — doesn't work!
async function processOrders(orders) {
  orders.forEach(async (order) => {
    await processOrder(order);  // forEach ignores returned Promises!
  });
  // function returns before orders are processed!
}

// ✅ FIX: Use for...of for sequential
async function processSequential(orders) {
  for (const order of orders) {
    await processOrder(order);  // waits for each one
  }
}

// ✅ Or use Promise.all for parallel
async function processParallel(orders) {
  await Promise.all(orders.map(order => processOrder(order)));
}

// ❌ MISTAKE 4: Not handling errors
async function route() {
  const data = await fetchData();  // if this throws, unhandled rejection!
  res.json(data);
}

// ✅ FIX: Always wrap in try/catch
async function route() {
  try {
    const data = await fetchData();
    res.json(data);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
}
```

---

# 25. DOM Manipulation

## 25.1 What is the DOM?

The DOM (Document Object Model) is a tree representation of your HTML that JavaScript can interact with.

```html
<!-- HTML -->
<html>
  <body>
    <h1 id="title">Hello</h1>
    <ul class="list">
      <li>Item 1</li>
      <li>Item 2</li>
    </ul>
  </body>
</html>
```

```
DOM Tree:
document
  └── html
       └── body
            ├── h1#title → "Hello"
            └── ul.list
                 ├── li → "Item 1"
                 └── li → "Item 2"
```

## 25.2 Selecting Elements

```js
// By ID — returns single element or null
const title = document.getElementById("title");

// By CSS selector — returns first match
const firstItem = document.querySelector("li");
const byClass = document.querySelector(".list");
const byId = document.querySelector("#title");
const nested = document.querySelector(".card .title");

// By CSS selector — returns ALL matches (NodeList)
const allItems = document.querySelectorAll("li");
const allCards = document.querySelectorAll(".card");

// Convert NodeList to array for array methods
const itemsArray = [...document.querySelectorAll("li")];
itemsArray.forEach(item => console.log(item.textContent));

// Older methods (still used)
document.getElementsByClassName("list");  // HTMLCollection
document.getElementsByTagName("li");      // HTMLCollection
```

## 25.3 Reading & Modifying Elements

```js
const elem = document.getElementById("title");

// Read content
elem.textContent;   // "Hello" — just text, no HTML tags
elem.innerHTML;     // "<strong>Hello</strong>" — includes HTML

// Modify content
elem.textContent = "New Title";           // safe — escapes HTML
elem.innerHTML = "<strong>Bold!</strong>"; // renders HTML — XSS risk with user input!

// Attributes
elem.getAttribute("id");          // "title"
elem.setAttribute("class", "big"); // set attribute
elem.removeAttribute("class");     // remove attribute
elem.hasAttribute("id");           // true

// CSS Classes
elem.classList.add("active");       // add class
elem.classList.remove("active");    // remove class
elem.classList.toggle("active");    // add if absent, remove if present
elem.classList.contains("active");  // true/false
elem.classList.replace("old", "new");

// Inline Styles
elem.style.color = "red";
elem.style.backgroundColor = "blue";  // camelCase!
elem.style.fontSize = "24px";
elem.style.display = "none";   // hide element
elem.style.display = "block";  // show element

// Read computed styles (what the browser actually renders)
const computedStyle = window.getComputedStyle(elem);
computedStyle.fontSize;  // "16px" (even if not set inline)

// Data attributes
// HTML: <div data-user-id="123" data-role="admin">
elem.dataset.userId;   // "123"
elem.dataset.role;     // "admin"
elem.dataset.newProp = "value";  // creates data-new-prop attribute
```

## 25.4 Creating & Inserting Elements

```js
// Create a new element
const newItem = document.createElement("li");
newItem.textContent = "New Item";
newItem.classList.add("item");

// Insert into DOM
const list = document.querySelector("ul");
list.appendChild(newItem);           // add at END of list
list.prepend(newItem);               // add at BEGINNING
list.insertBefore(newItem, refElem); // before a specific element

// Modern methods
list.append(newItem, anotherItem, "text");  // multiple items, text nodes
list.prepend(newItem);

elem.before(newItem);  // insert before elem (as sibling)
elem.after(newItem);   // insert after elem (as sibling)

// innerHTML for multiple elements (faster but XSS risk!)
list.innerHTML = `
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
`;

// NEVER do this with user input:
// list.innerHTML = userInput;  // XSS vulnerability!
// Do this instead:
const safe = document.createElement("li");
safe.textContent = userInput;  // textContent escapes HTML
list.appendChild(safe);

// Remove elements
elem.remove();                    // remove from DOM
list.removeChild(childElement);   // remove specific child

// Clone
const clone = elem.cloneNode(true);  // true = deep clone (includes children)
```

## 25.5 Traversing the DOM

```js
const list = document.querySelector("ul");

// Children
list.children;          // HTMLCollection of child elements
list.childNodes;        // NodeList including text nodes and comments
list.firstElementChild; // first child element
list.lastElementChild;  // last child element

// Parent
const item = document.querySelector("li");
item.parentElement;     // parent element
item.parentNode;        // parent node (could be document)

// Siblings
item.nextElementSibling;     // next sibling element
item.previousElementSibling; // previous sibling element

// Find closest ancestor matching selector
item.closest(".container");  // walks up DOM tree
item.closest("body");        // the body element

// Real-world: click on list item, get its data
document.querySelector("ul").addEventListener("click", (e) => {
  const clickedItem = e.target.closest("li");  // handle clicks on children too
  if (clickedItem) {
    const id = clickedItem.dataset.id;
    console.log("Clicked item:", id);
  }
});
```

---

# 26. Events

## 26.1 Adding Event Listeners

```js
const button = document.querySelector("#myBtn");

// addEventListener — preferred method
button.addEventListener("click", function(event) {
  console.log("Clicked!", event);
});

// Arrow function
button.addEventListener("click", (event) => {
  console.log("Clicked!");
});

// Named function (can be removed later)
function handleClick(event) {
  console.log("Clicked!");
}
button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick);  // remove it

// Options
button.addEventListener("click", handleClick, {
  once: true,    // only fire once, then auto-remove
  passive: true, // improves scroll performance
  capture: true  // fire during capture phase (advanced)
});
```

## 26.2 The Event Object

```js
document.addEventListener("click", (event) => {
  event.target;           // element that was clicked
  event.currentTarget;    // element with the listener
  event.type;             // "click"
  event.clientX;          // mouse X position
  event.clientY;          // mouse Y position
  event.key;              // for keyboard events: "Enter", "a", etc.
  event.keyCode;          // numeric key code (older, use event.key)
  event.preventDefault(); // prevent default browser behavior
  event.stopPropagation();// stop event from bubbling up
});

// Mouse events
elem.addEventListener("mousedown", handler);  // button pressed
elem.addEventListener("mouseup", handler);    // button released
elem.addEventListener("mousemove", handler);  // mouse moves
elem.addEventListener("mouseenter", handler); // mouse enters (no bubbling)
elem.addEventListener("mouseleave", handler); // mouse leaves (no bubbling)
elem.addEventListener("mouseover", handler);  // mouse over (bubbles)
elem.addEventListener("mouseout", handler);   // mouse out (bubbles)

// Keyboard events
input.addEventListener("keydown", (e) => {
  if (e.key === "Enter") submitForm();
  if (e.key === "Escape") closeModal();
  if (e.ctrlKey && e.key === "s") saveFile();  // Ctrl+S
});

// Form events
form.addEventListener("submit", (e) => {
  e.preventDefault();  // prevent page refresh!
  const data = new FormData(form);
  console.log(data.get("email"));
});

input.addEventListener("input", (e) => {
  console.log("Current value:", e.target.value);  // fires on every keystroke
});

input.addEventListener("change", handler);  // fires when input loses focus
input.addEventListener("focus", handler);   // element gains focus
input.addEventListener("blur", handler);    // element loses focus

// Window events
window.addEventListener("resize", () => {
  console.log(window.innerWidth, window.innerHeight);
});

window.addEventListener("scroll", () => {
  console.log(window.scrollY);  // pixels scrolled from top
});

window.addEventListener("load", () => {
  console.log("Page fully loaded including images");
});

document.addEventListener("DOMContentLoaded", () => {
  console.log("HTML parsed, DOM ready (before images load)");
});
```

## 26.3 Event Bubbling & Delegation

```js
// Events bubble UP the DOM tree by default
// click on <button> → fires on button → then on div → then on body → then on window

document.querySelector("button").addEventListener("click", (e) => {
  console.log("Button clicked");
  // e.stopPropagation();  // would stop bubbling here
});

document.querySelector("div").addEventListener("click", (e) => {
  console.log("Div also fires!");  // still fires because of bubbling
});

// EVENT DELEGATION — attach ONE listener to parent, handle ALL children
// Much more efficient than adding listeners to every child!
const todoList = document.querySelector("#todo-list");

todoList.addEventListener("click", (event) => {
  // Check what was actually clicked
  if (event.target.matches(".delete-btn")) {
    const li = event.target.closest("li");
    li.remove();
  }

  if (event.target.matches(".complete-btn")) {
    const li = event.target.closest("li");
    li.classList.toggle("completed");
  }
});

// This works even for dynamically added list items!
// You don't need to re-add listeners when new <li> are created
```

---

# 27. Fetch API & HTTP Requests

## 27.1 Basic Fetch

```js
// GET request
fetch("https://api.example.com/users")
  .then(response => response.json())
  .then(users => console.log(users))
  .catch(error => console.error("Error:", error));

// With async/await (much cleaner)
async function getUsers() {
  try {
    const response = await fetch("https://api.example.com/users");

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const users = await response.json();
    return users;
  } catch (error) {
    console.error("Failed to fetch users:", error);
  }
}
```

## 27.2 POST, PUT, DELETE Requests

```js
// POST — send data to server
async function createUser(userData) {
  const response = await fetch("https://api.example.com/users", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": "Bearer " + getToken()
    },
    body: JSON.stringify(userData)  // convert object to JSON string
  });

  if (!response.ok) throw new Error("Failed to create user");
  return response.json();
}

const newUser = await createUser({ name: "Alice", email: "alice@example.com" });

// PUT — update/replace entire resource
async function updateUser(id, userData) {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(userData)
  });
  return response.json();
}

// PATCH — partial update
async function updateUserName(id, name) {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name })
  });
  return response.json();
}

// DELETE
async function deleteUser(id) {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: "DELETE"
  });
  if (!response.ok) throw new Error("Failed to delete");
  return true;
}
```

## 27.3 Response Methods

```js
const response = await fetch(url);

// Check status
response.status;     // 200, 404, 500, etc.
response.ok;         // true if status 200-299
response.statusText; // "OK", "Not Found", etc.

// Headers
response.headers.get("content-type");  // "application/json"

// Read body (can only read ONCE!)
response.json();    // parse as JSON → returns Promise
response.text();    // get as plain text → returns Promise
response.blob();    // get as binary (images, files) → returns Promise
response.arrayBuffer(); // raw binary data
```

## 27.4 Building a Reusable API Client

```js
class ApiClient {
  constructor(baseUrl, getToken) {
    this.baseUrl = baseUrl;
    this.getToken = getToken;
  }

  async request(path, options = {}) {
    const url = `${this.baseUrl}${path}`;
    const headers = {
      "Content-Type": "application/json",
      ...options.headers
    };

    const token = this.getToken?.();
    if (token) headers["Authorization"] = `Bearer ${token}`;

    const response = await fetch(url, { ...options, headers });

    if (!response.ok) {
      const error = await response.json().catch(() => ({}));
      throw new Error(error.message || `HTTP ${response.status}`);
    }

    if (response.status === 204) return null;  // no content
    return response.json();
  }

  get(path, params) {
    const query = params ? "?" + new URLSearchParams(params) : "";
    return this.request(path + query);
  }

  post(path, data) {
    return this.request(path, { method: "POST", body: JSON.stringify(data) });
  }

  put(path, data) {
    return this.request(path, { method: "PUT", body: JSON.stringify(data) });
  }

  patch(path, data) {
    return this.request(path, { method: "PATCH", body: JSON.stringify(data) });
  }

  delete(path) {
    return this.request(path, { method: "DELETE" });
  }
}

// Usage
const api = new ApiClient("https://api.example.com", () => localStorage.getItem("token"));

const users = await api.get("/users", { page: 1, limit: 10 });
const newUser = await api.post("/users", { name: "Alice", email: "alice@example.com" });
await api.delete(`/users/${userId}`);
```

---

# 28. LocalStorage & SessionStorage

## 28.1 Web Storage APIs

```js
// localStorage — persists even after browser closes (permanent until cleared)
// sessionStorage — cleared when tab is closed

// Store data
localStorage.setItem("username", "Alice");
localStorage.setItem("theme", "dark");
localStorage.setItem("user", JSON.stringify({ id: 1, name: "Alice" })); // objects need JSON

// Read data
const username = localStorage.getItem("username");  // "Alice"
const missing = localStorage.getItem("nonexistent"); // null

// Objects need JSON.parse
const user = JSON.parse(localStorage.getItem("user")); // { id: 1, name: "Alice" }

// Remove
localStorage.removeItem("username");
localStorage.clear();  // remove everything!

// Check what's stored
localStorage.length;   // number of items
localStorage.key(0);   // get key at index 0

// sessionStorage — same API but cleared when tab closes
sessionStorage.setItem("cart", JSON.stringify([]));
sessionStorage.getItem("cart");
sessionStorage.removeItem("cart");
```

## 28.2 Safe Storage Helper

```js
// LocalStorage can fail (quota exceeded, private browsing, etc.)
const storage = {
  get(key, defaultValue = null) {
    try {
      const item = localStorage.getItem(key);
      return item !== null ? JSON.parse(item) : defaultValue;
    } catch {
      return defaultValue;
    }
  },

  set(key, value) {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      return true;
    } catch {
      console.warn(`Storage full or unavailable`);
      return false;
    }
  },

  remove(key) {
    try {
      localStorage.removeItem(key);
    } catch {}
  }
};

// Usage
storage.set("cart", [{ id: 1, qty: 2 }]);
const cart = storage.get("cart", []);  // [] if not found
storage.remove("cart");
```

---

# 29. Modules (import / export)

## 29.1 Named Exports & Imports

```js
// math.js — named exports
export function add(a, b) { return a + b; }
export function subtract(a, b) { return a - b; }
export function multiply(a, b) { return a * b; }
export const PI = 3.14159;

// Export at end
function divide(a, b) { return a / b; }
const E = 2.71828;
export { divide, E };

// main.js — named imports
import { add, subtract, PI } from "./math.js";
import { divide as div } from "./math.js";  // rename on import

console.log(add(2, 3));  // 5
console.log(PI);         // 3.14159

// Import everything
import * as math from "./math.js";
math.add(2, 3);      // 5
math.PI;             // 3.14159
```

## 29.2 Default Exports

```js
// userService.js — default export (only one per file)
class UserService {
  async getAll() { ... }
  async getById(id) { ... }
  async create(data) { ... }
}

export default UserService;
// or
export default class UserService { ... }
export default function fetchData() { ... }

// Import default — any name works
import UserService from "./userService.js";
import MyUserService from "./userService.js";  // also works!
```

## 29.3 Dynamic Imports

```js
// Load module only when needed (code splitting!)
async function loadMap() {
  const { MapComponent } = await import("./MapComponent.js");
  const map = new MapComponent("container");
  map.render();
}

// Load on user action
button.addEventListener("click", async () => {
  const module = await import("./heavyFeature.js");
  module.default.initialize();
});
```

---

# 30. Important Array Methods

Complete reference for the most commonly used array methods.

```js
const numbers = [1, 2, 3, 4, 5];
const users = [
  { id: 1, name: "Alice", age: 25, active: true },
  { id: 2, name: "Bob", age: 30, active: false },
  { id: 3, name: "Charlie", age: 22, active: true }
];

// TRANSFORMATION
numbers.map(n => n * 2);              // [2, 4, 6, 8, 10]
users.map(u => u.name);              // ["Alice", "Bob", "Charlie"]
users.map(({ id, name }) => ({ id, name })); // pick fields

// FILTERING
numbers.filter(n => n > 3);          // [4, 5]
users.filter(u => u.active);         // Alice, Charlie
users.filter(u => u.age >= 25);      // Alice, Bob

// REDUCING
numbers.reduce((sum, n) => sum + n, 0);          // 15
users.reduce((max, u) => u.age > max.age ? u : max); // oldest user

// SEARCHING
users.find(u => u.name === "Bob");              // { id: 2, name: "Bob", ... }
users.find(u => u.id === 999);                  // undefined
users.findIndex(u => u.name === "Bob");         // 1
numbers.indexOf(3);                             // 2
numbers.includes(3);                            // true
users.some(u => u.active);                      // true (at least one active)
users.every(u => u.active);                     // false (Bob is inactive)

// SORTING
[...users].sort((a, b) => a.age - b.age);       // sort by age ascending
[...users].sort((a, b) => b.age - a.age);       // descending
[...users].sort((a, b) => a.name.localeCompare(b.name)); // alphabetical

// FLATTENING
[[1, 2], [3, 4], [5]].flat();                   // [1, 2, 3, 4, 5]
users.flatMap(u => u.hobbies || []);            // all hobbies from all users

// SLICING
numbers.slice(1, 3);    // [2, 3] — doesn't modify original
numbers.slice(-2);      // [4, 5] — last 2

// JOINING
["a", "b", "c"].join("-");                      // "a-b-c"
["Hello", "World"].join(" ");                   // "Hello World"

// CHECKING
Array.isArray([]);  // true
Array.isArray({});  // false

// CREATING
Array.from({ length: 5 }, (_, i) => i);   // [0, 1, 2, 3, 4]
Array.from({ length: 5 }, (_, i) => i + 1); // [1, 2, 3, 4, 5]
Array.from("hello");                       // ["h", "e", "l", "l", "o"]
[...Array(5).keys()];                      // [0, 1, 2, 3, 4]

// COPY (shallow)
[...numbers];                // spread copy
numbers.slice();             // slice copy
Array.from(numbers);         // from copy
```

---

# 31. Important Object Methods

```js
const user = { name: "Alice", age: 25, city: "Mumbai" };

// Keys, Values, Entries
Object.keys(user);    // ["name", "age", "city"]
Object.values(user);  // ["Alice", 25, "Mumbai"]
Object.entries(user); // [["name", "Alice"], ["age", 25], ["city", "Mumbai"]]

// From entries
Object.fromEntries([["a", 1], ["b", 2]]);  // { a: 1, b: 2 }
Object.fromEntries(Object.entries(user).map(([k, v]) => [k, String(v)]));
// Convert all values to strings

// Merge
Object.assign({}, obj1, obj2);  // merge into new object
const merged = { ...obj1, ...obj2 };  // same with spread (preferred)

// Check property
"name" in user;                  // true (checks prototype chain too)
user.hasOwnProperty("name");     // true (own properties only)

// Freeze / Seal
Object.freeze(obj);  // no changes allowed
Object.seal(obj);    // can change values, can't add/delete properties

// Property descriptors (advanced)
Object.defineProperty(obj, "id", {
  value: 123,
  writable: false,    // can't change value
  enumerable: false,  // won't show in for...in
  configurable: false // can't delete or reconfigure
});

// Loop through object
for (const [key, value] of Object.entries(user)) {
  console.log(`${key}: ${value}`);
}

Object.keys(user).forEach(key => {
  console.log(key, user[key]);
});

// Transform object values
const doubled = Object.fromEntries(
  Object.entries(prices).map(([item, price]) => [item, price * 2])
);
```

---

# 32. Common Patterns & Best Practices

## 32.1 Guard Clauses (Early Returns)

```js
// ❌ Deeply nested — hard to read
function processOrder(order) {
  if (order) {
    if (order.items.length > 0) {
      if (order.user.isActive) {
        if (order.payment.isValid) {
          // Actually process the order
          return processPayment(order);
        } else {
          throw new Error("Invalid payment");
        }
      } else {
        throw new Error("User inactive");
      }
    } else {
      throw new Error("No items");
    }
  } else {
    throw new Error("No order");
  }
}

// ✅ Guard clauses — flat, easy to read
function processOrder(order) {
  if (!order) throw new Error("No order");
  if (order.items.length === 0) throw new Error("No items");
  if (!order.user.isActive) throw new Error("User inactive");
  if (!order.payment.isValid) throw new Error("Invalid payment");

  // Happy path — no nesting!
  return processPayment(order);
}
```

## 32.2 IIFE (Immediately Invoked Function Expression)

```js
// Runs immediately, creates its own scope
(function() {
  const privateVar = "can't access this outside";
  console.log("runs immediately!");
})();

// With arrow function
(() => {
  console.log("also runs immediately!");
})();

// Useful for avoiding global variable pollution
// or creating isolated scope for a piece of code
```

## 32.3 Useful Patterns

```js
// DEEP CLONE an object (when shallow copy isn't enough)
const deep = JSON.parse(JSON.stringify(original));
// Note: doesn't work for functions, undefined, Dates, RegExp

// Modern way (2023)
const deep2 = structuredClone(original);  // ✅ handles more types

// DEBOUNCE — delay execution until user stops typing/clicking
function debounce(fn, delay) {
  let timer;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

const debouncedSearch = debounce((query) => {
  console.log("Searching:", query);
}, 500);

// Only searches 500ms after user stops typing
input.addEventListener("input", (e) => debouncedSearch(e.target.value));

// THROTTLE — limit how often a function runs
function throttle(fn, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

const throttledScroll = throttle(() => {
  console.log("scroll position:", window.scrollY);
}, 100);

window.addEventListener("scroll", throttledScroll);

// PIPE — compose functions left to right
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);

const process = pipe(
  str => str.trim(),
  str => str.toLowerCase(),
  str => str.replace(/\s+/g, "-")
);
process("  Hello World  ");  // "hello-world"

// ONCE — function that runs only once
function once(fn) {
  let called = false;
  let result;
  return function(...args) {
    if (!called) {
      called = true;
      result = fn.apply(this, args);
    }
    return result;
  };
}

const initOnce = once(() => {
  console.log("Initialized!");
  return "done";
});

initOnce();  // "Initialized!" — returns "done"
initOnce();  // nothing logged — returns "done" (cached)
initOnce();  // nothing logged — returns "done" (cached)
```

## 32.4 Best Practices Summary

```js
// 1. Use const by default, let when you need to reassign, never var
const name = "Alice";
let score = 0;

// 2. Use strict equality ===, never ==
"5" === 5;  // false (correct)

// 3. Use optional chaining to avoid errors
user?.address?.city;  // undefined instead of TypeError

// 4. Use nullish coalescing for defaults
const port = process.env.PORT ?? 3000;

// 5. Avoid mutating arrays/objects — create new ones
const newArr = [...arr, newItem];        // don't push
const newObj = { ...obj, key: value };   // don't assign directly

// 6. Use meaningful variable names
const x = users.filter(u => u.active);  // ❌
const activeUsers = users.filter(u => u.active);  // ✅

// 7. Keep functions small and focused (single responsibility)
// ❌ one function that does everything
// ✅ many small, testable functions

// 8. Always handle errors
try {
  const data = await fetchData();
} catch (err) {
  // handle it!
}

// 9. Use array methods instead of loops when possible
// More readable and often more expressive
const doubled = nums.map(n => n * 2);  // ✅ vs for loop

// 10. Avoid deeply nested code — use early returns, guard clauses
function validate(user) {
  if (!user) return false;
  if (!user.email) return false;
  if (!user.email.includes("@")) return false;
  return true;
}
```

---

# Quick Reference — JavaScript Cheat Sheet

## Data Types
| Type | Example | typeof |
|------|---------|--------|
| Number | `42`, `3.14` | `"number"` |
| String | `"hello"` | `"string"` |
| Boolean | `true`, `false` | `"boolean"` |
| undefined | `let x;` | `"undefined"` |
| null | `let x = null;` | `"object"` ← bug |
| Object | `{}`, `[]` | `"object"` |
| Function | `function(){}` | `"function"` |

## Common String Methods
| Method | Example | Result |
|--------|---------|--------|
| `length` | `"hello".length` | `5` |
| `toUpperCase()` | `"hi".toUpperCase()` | `"HI"` |
| `includes()` | `"hello".includes("ell")` | `true` |
| `split()` | `"a,b".split(",")` | `["a","b"]` |
| `trim()` | `" hi ".trim()` | `"hi"` |
| `slice()` | `"hello".slice(1,3)` | `"el"` |
| `replace()` | `"hello".replace("l","r")` | `"herlo"` |

## Common Array Methods
| Method | What it does | Returns |
|--------|-------------|---------|
| `push()` | Add to end | New length |
| `pop()` | Remove from end | Removed item |
| `shift()` | Remove from start | Removed item |
| `unshift()` | Add to start | New length |
| `map()` | Transform each item | New array |
| `filter()` | Keep matching items | New array |
| `reduce()` | Boil down to single value | Any value |
| `find()` | Find first match | Item or undefined |
| `includes()` | Check if value exists | Boolean |
| `sort()` | Sort in place | Same array |
| `slice()` | Extract portion | New array |
| `splice()` | Remove/insert in place | Removed items |
| `flat()` | Flatten nested arrays | New array |
| `join()` | Array to string | String |

## Falsy Values
`false`, `0`, `""`, `null`, `undefined`, `NaN`

## Truthy — Everything Else!
Including: `[]`, `{}`, `"0"`, `"false"`, `-1`, `Infinity`

---

*Master these concepts and you'll have a rock-solid JavaScript foundation. Practice by building small projects after each section!*