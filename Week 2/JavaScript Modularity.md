
# 1. JavaScript Modularity
Modularity is about bundling related functions, objects, and values into separate files for reusability. JavaScript handles this differently depending on the environment.

* **Why use them?** To avoid polluting the global namespace and to make code maintainable. Think of how you import specific tools from a library like NumPy in Python.
* **The Problem:** Browser-based scripts historically lacked a unified module system, leading to issues with synchronous loading (freezing the page while waiting for files to download).
* **Modern Solution:** ES6 Modules allow asynchronous loading, which is performance-friendly for browsers.

**Example: Basic Export and Import**
You must define the script type as `module` in your HTML to use these features:

```html
<script type="module" src="modularity.js"></script>

```

* **Module 1 (module1.js):** Exports a constant.

```javascript
export const c = 299792458;

```

* **Main File (modularity.js):** Imports `c`.

```javascript
import { c } from './module1.js';
console.log(c); // Outputs the speed of light

```

**2. Advanced Module Techniques**
JavaScript allows for flexible management of how data is shared between files.

* **Renaming Imports/Exports:** If you want to use a different name locally for clarity, you can rename imports (`import { c as speedOfLight }`) or exports during declaration.
* **Default Exports:** When a module is intended to export one primary thing, you use `export default`. This makes importing easier as you can name the import whatever you like in the receiving file. No curly braces needed.

**module2.js (The Module)**

```javascript
// You can only have one default per file
export default function add(a, b) {
    return a + b;
}

```

**main.js (The Import)**

```javascript
// Notice no curly braces are needed
import add from './module2.js';

console.log(add(5, 400)); // 405

```

* **Read-only Views:** When you import a value, you get a 'read-only' view. You cannot change it directly in the importing file. To modify exported variables, you must call exported functions that exist within the same scope as that variable.

**counter.js (The Module)**

```javascript
export let count = 0;

// This function provides a controlled function to change the state
export function increment() {
    count++;
}

```

**main.js (The Import)**

```javascript
import { count, increment } from './counter.js';

// count = 10; // ERROR. This will fail with 'TypeError'

increment(); // Works! This updates the internal module variable
console.log(count); // 1

```

**3. Object Orientation in JavaScript**
JavaScript objects are collections of named parameters and methods. Unlike Java or C++, it uses **Prototype-based Inheritance** rather than a class-based hierarchy.

* **Methods:** You can attach functions directly to objects, turning them into methods.

```javascript
// A simple object w/
const vehicle = {
    drive: function() { console.log("Driving"); }
};

// Create a new object that uses 'vehicle' as its prototype
const myCar = Object.create(vehicle);
myCar.type = "SUV";

// When you call .drive(), JavaScript looks at 'vehicle'
myCar.drive(); // Prints: "Driving" and delegates the call up via 'vehicle' object (Prototype Chain)

```

* **The this keyword:** This acts like `self` in Python. It allows a method to refer to other properties within the same object.

```javascript
const obj = {
    a: 5,
    b: function() { return this.a * 2; } // This refers to obj
};

```

**4. Object Copying and References**
A common "gotcha" in JavaScript is how objects are assigned.

* **References vs. Copy:** If you write `g = e`, you are only creating a pointer. Changing `g` will also change `e` because they point to the exact same memory location.
* **The Spread Operator (...):** To create an actual copy, use the spread operator. This creates a new object and copies the values, ensuring that updates to one don't affect the other.

```javascript
const original = { a: 1, b: 2, c: 3 };
const copy = { ...original }; // Spread Operator creates a new object

copy.a = 99;
console.log(original.a); // Still 1 (the original is unaffected)

```

**Note on "Deep" vs "Shallow" Copies:** The spread operator only performs a shallow copy. If your object contains other nested objects, the nested references will still point to the originals.
