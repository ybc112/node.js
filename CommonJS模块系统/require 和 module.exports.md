

### **CommonJS 模块系统详解**

CommonJS 是 Node.js 中实现模块化的标准规范，核心思想是 **"每个文件是一个模块，模块间通过 `require` 和 `module.exports` 交互"**。以下是详细讲解：

---

### **1. 基本概念**
- **模块作用域**：每个文件默认是独立的模块，变量、函数默认私有，不会污染全局。
- **导出内容**：通过 `module.exports` 或 `exports` 对象暴露模块内部功能。
- **导入内容**：通过 `require` 函数加载其他模块。

---

### **2. 核心语法**
#### **(1) 导出模块（`module.exports` 和 `exports`）**
- **方式1：直接赋值给 `module.exports`**
  ```javascript
  // math.js
  const add = (a, b) => a + b;
  module.exports = add; // 导出一个函数
  ```

- **方式2：挂载到 `exports` 对象**
  ```javascript
  // math.js
  exports.add = (a, b) => a + b;
  exports.subtract = (a, b) => a - b;
  ```

- **注意**：
  - `exports` 是 `module.exports` 的引用，直接赋值 `exports = ...` 会断开引用，导致导出失败。
  - 正确用法是修改 `exports` 的属性或直接使用 `module.exports`。

---

#### **(2) 导入模块（`require`）**
- **基本语法**：
  ```javascript
  const module = require('module-name');
  ```

- **加载规则**：
  1. **核心模块**（如 `fs`、`path`）：直接写模块名。
     ```javascript
     const fs = require('fs');
     ```
  2. **文件模块**：需写路径（相对或绝对）。
     ```javascript
     const math = require('./math.js'); // 加载当前目录下的 math.js
     ```
  3. **目录模块**：需有 `index.js` 或 `package.json` 入口文件。
     ```javascript
     const utils = require('./utils'); // 加载 ./utils/index.js
     ```

---

### **3. 模块加载机制**
1. **缓存机制**：
   - 模块第一次加载时执行并缓存，后续 `require` 直接返回缓存结果。
   ```javascript
   // a.js
   console.log('Module A loaded');
   module.exports = {};

   // app.js
   require('./a'); // 输出 "Module A loaded"
   require('./a'); // 不会重复执行，无输出
   ```

2. **加载顺序**：
   - Node.js 会按以下顺序查找模块：
     1. 核心模块（如 `fs`）。
     2. 文件模块（如 `./module.js`）。
     3. `node_modules` 目录中的第三方模块。

---

### **4. 实战示例**
#### **(1) 导出单个功能**
```javascript
// greeting.js
module.exports = (name) => `Hello, ${name}!`;

// app.js
const greet = require('./greeting');
console.log(greet('Alice')); // 输出 "Hello, Alice!"
```

#### **(2) 导出多个功能**
```javascript
// math.js
exports.add = (a, b) => a + b;
exports.multiply = (a, b) => a * b;

// app.js
const math = require('./math');
console.log(math.add(2, 3));      // 5
console.log(math.multiply(2, 3)); // 6
```

#### **(3) 导出类**
```javascript
// Person.js
class Person {
  constructor(name) {
    this.name = name;
  }
}
module.exports = Person;

// app.js
const Person = require('./Person');
const alice = new Person('Alice');
console.log(alice.name); // "Alice"
```

---

### **5. 常见问题**
#### **(1) 循环依赖**
- **问题**：模块 A 引用模块 B，模块 B 又引用模块 A，可能导致未初始化的值。
- **解决**：避免循环依赖，或通过事件、方法延迟访问依赖项。

#### **(2) 路径问题**
- **相对路径**：`require('./module')` 表示当前目录下的模块。
- **绝对路径**：`require('/path/to/module')` 不推荐，跨环境可能失效。

---

### **6. CommonJS vs ES6 模块**
| 特性          | CommonJS                     | ES6 模块                     |
|---------------|------------------------------|------------------------------|
| **语法**      | `require` / `module.exports` | `import` / `export`          |
| **加载方式**  | 同步加载（适合服务器）       | 异步加载（适合浏览器）       |
| **静态分析**  | 不支持（动态加载）           | 支持（编译时静态分析）       |
| **Tree-shaking** | 不支持                    | 支持（优化未使用代码）       |

---

### **7. 总结**
- **CommonJS 是 Node.js 的模块化标准**，通过 `require` 和 `module.exports` 实现模块的导入导出。
- **模块加载是同步且缓存的**，适合服务器端开发。
- **避免循环依赖**，合理设计模块结构。

