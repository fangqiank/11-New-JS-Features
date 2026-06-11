# 现代 JavaScript 新特性一览

本页通过独立卡片直观展示 11 个新特性，帮助你快速理解它们如何简化代码。

---

## 01 - Set 集合运算

演示了 `union`（并集）、`intersection`（交集）、`difference`（差集）以及 `isSubsetOf`（子集判断）。你不再需要手写循环来比较两个集合。

```js
const A = new Set([1, 2, 3, 4]);
const B = new Set([3, 4, 5, 6]);

A.union(B);          // 并集: {1, 2, 3, 4, 5, 6}
A.intersection(B);   // 交集: {3, 4}
A.difference(B);     // 差集: {1, 2}
new Set([1, 2]).isSubsetOf(A); // true
A.isSupersetOf(new Set([2, 3])); // true
```

> Chrome 122+, Node 22+

---

## 02 - Iterator Helpers

展示如何对无限生成器进行 `map`、`filter` 和 `take` 操作，最后用 `toArray()` 收集结果。这让处理流式数据变得像操作数组一样自然。

```js
function* counter() {
  let i = 1;
  while (true) yield i++;
}

counter()
  .map(x => x * 10)
  .filter(x => x % 30 === 0)
  .take(5)
  .toArray(); // [30, 60, 90, 120, 150]
```

> Chrome 122+, Node 22+

---

## 03 - Promise.try

`Promise.try` 能将同步函数中抛出的错误自动转为 rejected Promise，统一错误处理路径。

与手动 `try-catch` 包装相比，`Promise.try` 让代码更简洁，同步抛错和异步 reject 都能被同一段 `.catch` 处理。

```js
// Promise.try 版本（推荐）
Promise.try(() => JSON.parse('{invalid}'))
  .catch(err => console.error(err));

// 等价的普通写法（多一层嵌套）
try { JSON.parse('{invalid}'); }
catch(err) { Promise.reject(err); }
```

点击卡片按钮会同时展示成功和失败两条路径，方便对比。

> Chrome 128+, Node 22+

---

## 04 - Import Attributes

`import attributes` 允许在导入语句中声明模块类型，比如 `type: "json"`。在模块环境中可以用静态导入，在普通脚本中用动态 `import()` 配合 `{ with: { type: 'json' } }` 实现同样功能。

```js
// 动态 import() 方式（普通脚本和模块都可使用）
const config = await import('./data.json',
  { with: { type: 'json' } });
```

页面中的卡片使用动态 `import()` 实际加载了 `data.json`，展示了导入结果包含的数据内容。

> Chrome 123+, Node 21+

---

## 05 - RegExp.escape

模拟用户输入包含特殊字符的场景，展示 `RegExp.escape` 如何自动转义字符串，安全地创建正则表达式。

```js
const input = 'example.com?q=hello&price=$19.99';
const escaped = RegExp.escape(input);
// 结果: 'example\\.com\\?q=hello&price=\\$19\\.99'

new RegExp(escaped).test('访问 example.com?q=hello&price=$19.99 获取信息');
// true
```

> Chrome 130+, Node 23+

---

## 06 - Array.fromAsync

使用异步生成器模拟分页 API 请求，展示 `Array.fromAsync` 如何自动迭代并汇总所有异步数据，无需手动 `for await...of`。

```js
async function* pages() {
  yield await fetchPage(1);
  yield await fetchPage(2);
  yield await fetchPage(3);
}

const all = await Array.fromAsync(pages());
// 汇总所有异步页数据
```

> Chrome 121+, Node 21+

---

## 07 - Error.isError

对比了 `Error.isError` 和 `instanceof Error`，突出前者在跨执行环境（如 iframe）下的可靠性。

```js
Error.isError(new Error('x'));       // true
Error.isError(new TypeError('x'));   // true
Error.isError({message: 'x'});      // false
Error.isError(null);                 // false
// 跨 realm（如 iframe）也可靠
```

> Chrome 127+, Node 22+

---

## 08 - Map upsert

通过缓存场景演示了 `getOrInsert` 和 `getOrInsertComputed`，后者在默认值计算成本高时非常有用（惰性求值）。

```js
const cache = new Map();

// 简单默认值
cache.getOrInsert('key', defaultValue);

// 惰性计算 —— 只在 key 不存在时执行回调
cache.getOrInsertComputed('key', () => expensiveCompute());
```

> Chrome 130+, Node 23+

---

## 09 - Math.sumPrecise

用一组浮点数求和，对比普通 `reduce` 和 `Math.sumPrecise` 的结果，展示其减少浮点运算误差的能力。

```js
const nums = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0];

nums.reduce((a, b) => a + b, 0); // 5.499999999999999
Math.sumPrecise(nums);           // 5.5
```

> Chrome 131+, Node 23+

---

## 10 - 显式资源管理

通过模拟数据库连接类（带 `Symbol.dispose` 方法），展示了资源如何在离开作用域时自动清理，避免泄漏。

```js
class Connection {
  [Symbol.dispose]() {
    // 自动清理连接
  }
}

using conn = new Connection();
// 离开作用域自动调用 dispose
```

页面中模拟了 `using` 的效果（通过 `try/finally` 手动调用 `[Symbol.dispose]`），展示了资源从创建到自动清理的完整生命周期。

> Chrome 125+, Safari 待支持

---

## 11 - Temporal API

通过获取当前时间并进行时间运算（如 `add`），对比了原生的时区、持续时间支持，并提示在部分浏览器需要 polyfill。

```js
const now = Temporal.Now.zonedDateTimeISO();
now.add({ hours: 2, minutes: 30 });
now.until(otherDate);
// 原生时区、格式化支持
```

> Chrome/Edge 已支持，Safari 需 polyfill（如 `@js-temporal/polyfill`）
