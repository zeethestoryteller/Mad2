Here are structured notes based on the lecture [W6_L3: Vue - managing components | single file components and build tools](https://www.youtube.com/watch?v=zjGtbOZAS9A) that you can write directly into your notebook:

---

### **1. Limitations of Simple Vue Implementations**

* **Global Namespace:** Components require unique names and go into a global namespace. This causes issues and naming conflicts when importing third-party libraries.
* **String Templates:** Using backticks for templates is hard to edit and manage without extra editor support.
* **Global CSS Styling:** Styles apply globally at the top-level HTML file. Block scoping (styling specific only to one component) is not straightforward.
* **No Build Step :**
* Avoids a compiler/build phase (unlike C vs Python).
* Makes handling **backwards compatibility** difficult because you cannot easily use tools like **Babel** to automatically add **polyfills** for older browsers.



---

### **2. Single File Components (SFCs) (`[00:05:00]`)**

Vue solves these problems using `.vue` files, which split the code into three clear logical sections:

1. **Template :** Pure HTML structure (plus interpolated variables like `{{ variable }}`).
2. **Script :** JavaScript logic utilizing ES6 modules (`export default`, imports, etc.).
3. **Style :** CSS styling that can be marked as **scoped**, ensuring styles apply only to that specific component.

* **Separation of Concerns:** Although HTML, CSS, and JS are in one file, they remain cleanly separated by sections because logically they belong together.

---

### **3. Build Tools & Node Package Manager (npm)**

* **Compilation:** Browsers cannot read `.vue` files directly, so a build/packaging tool is required to convert `.vue` files into standard JS, CSS, and HTML.
* *Examples:* Webpack, Rollup, ESBuild.


* **npm (Node Package Manager):**
* Systematically manages JavaScript modules and dependencies.
* **Drawbacks:** Can consume significant disk space (heavy `node_modules` folders) and introduces potential security concerns like **supply chain attacks** (`[00:11:22]`) from third-party packages.
