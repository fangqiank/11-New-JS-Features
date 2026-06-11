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

通过一个可能抛出同步错误的函数，演示了如何用 `Promise.try` 统一同步和异步错误，让错误处理路径更一致。

```js
Promise.try(() => {
  // 同步抛错也会进入 .catch
  return JSON.parse('{invalid}');
})
.then(handle)
.catch(handleError);
```

> Chrome 128+, Node 22+

---

## 04 - Import Attributes

在代码示例和注释中说明了 `import ... with { type: "json" }` 的语法，强调原生导入 JSON 的能力，并提示未来可扩展至 CSS 等资源。

```js
// 原生导入 JSON（模块环境）
import config from './data.json'
  with { type: 'json' };

// 未来可能支持 CSS 等资源类型
```

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
