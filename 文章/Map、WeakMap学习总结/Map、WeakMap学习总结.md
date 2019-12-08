## 什么是 Map

简单来说 `Map` 是 `JavaScript` 中的一种数据类型，它和 `Object` 的类似，不过 `Object` 的 `key` 只能是字符串，而 `Map` 的 `key` 可以是任意数据类型，比如对象、数组、字符串等。看下面例子：

- `Object`

```js
const m = {}
const x = { a: 1 }, y = { b: 2 }
m[x] = '1'
m[y] = '2'

console.log(m[x], m[y]) // '2'，原因是 x、y 在设置为 m 的 key 时自动调用 toString，值都是 '[object Object]'，所以 m 只设置了一个键
```

- Map

```js
const m = new Map()
const x = { a: 1 }, y = { b: 2 }
m.set(x, y)

console.log(m.get(x)) // { b: 2 }
```

### Map 的说明

- 首先讲讲 `Map` 的构造方式，它可以通过上面代码的方式创建，也可以在构造器中传入一个迭代器，这个迭代器必须返回一列数组，这列数组中的每个数组的第一个元素是 `key`，第二个元素是 `value`，如下：

  ```js
  const x = { a: 1 }, y = { b: 2 }
  const m = new Map([ [x, y] ])

  console.log(m.get(x)) // { b: 2 }
  ```

  - 上面所述的入参结构和 `Map` 实例 `entries` 方法返回的结果完全一样，所以，这就很方便创建一个 `Map` 实例的副本，如下：

  ```js
  const m = new Map()
  const x = { a: 1 }, y = { b: 2 }
  m.set(x, y)

  const m2 = new Map(m.entries())

  console.log(m2.get(x)) // { b: 2 }
  ```

  - 又因为 `Map` 实例的默认迭代器与 `entries` 的结果相同，所以更推荐如下写法：

  ```js
  const m = new Map()
  const x = { a: 1 }, y = { b: 2 }
  m.set(x, y)

  const m2 = new Map(m) // 注意这里没有再调用 entries 方法

  console.log(m2.get(x)) // { b: 2 }
  ```

- `size` 属性返回 `Map` 实例的长度

- `clear` 方法可以清空整个 `Map` 实例的内容

- `delete` 方法可以删除指定的元素，删除成功返回 `true`，如果删除的元素没有在 `Map` 实例中，则返回 `false`，比如：

  ```js
  const m = new Map()
  const x = { a: 1 }, y = { b: 2 }
  m.set(x, y)

  console.log(m.delete(x)) // true
  console.log(m.delete(y)) // false
  ```

- `entries` 方法返回一个迭代器，我们可以通过解构或者 `Array.from()` 把结构转为数组，比如：

  ```js
  const m = new Map()
  const x = { a: 1 }, y = { b: 2 }
  m.set(x, y)

  const arr1 = [...m.entries()]

  console.log(arr1[0][0]) // { a: 1 }
  console.log(arr1[0][1]) // { b: 2 }

  const arr2 = Array.from(m.entries())

  console.log(arr2[0][0]) // { a: 1 }
  console.log(arr2[0][1]) // { b: 2 }
  ```

- `forEach` 方法将会以插入顺序对 `Map` 实例中的每一个键值对执行一次参数中提供的回调函数，比如：

  ```js
  const m = new Map()
  const x = { a: 1 }, y = { b: 2 }
  m.set(x, y)

  m.forEach((key, value, map) => {
      console.log(key, value, map) // {b: 2} {a: 1} Map(1) {{…} => {…}}
  })
  ```

- `values` 方法可以返回 `Map` 实例中值上的迭代器，使用方法同 `entries` 方法

- `keys` 方法可以返回 `Map` 实例中键上的迭代器，使用方法同 `entries` 方法

- `get` 方法可以返回指定 key 的元素

- `set` 方法可以向 `Map` 实例中增加元素

- `has` 方法可以判断某个元素是否在当前 `Map` 实例中

### Map 应用场景

- 当你定义了一个 `Object` 后，需要知道这个 `Object` 有多少键时，你是否想到了 `Map`？

- 当你在使用 `Object.keys` 时，你是否想到了 `Map`？

- 当你的 `Object` 的键值涉及频繁增删时，你是否想到了 `Map`？

### 注意

上面说的 Map，如果使用对象作为 key，那么当这个对象被丢弃，试图让其被 GC （垃圾回收）时，Map 实例本身仍然会持有其引用，所以还需要我们手动从 Map 实例中删除这个元素来支持 GC

## 什么是 WeakMap

WeakMap 是 Map 的一种变体，二者的多数特性都是一样的，区别在于以下几点：

  1. WeakMap 只支持 Object 作为键

  2. 如果 Object 本身被 GC，对应的在 WeakMap 中的元素也会被移除（这是重点）

  3. WeakMap 自身的特性只有 delete、get、set、has，行为和 Map 是一致

### WeakMap 的说明

- WeakMap 的构造方式，其实写法还是和 Map 一样，不过这里有几点需要注意：

  - WeakMap 的键只能是 Object，不然会报错，比如：

    ```js
    const m = new WeakMap()
    const x = 4, y = 6
    m.set(x, y) // Uncaught TypeError: Invalid value used as weak map key
    ```

  - WeakMap 的构造器接收的迭代器里面所有的键也都必须是 Object，比如：
  
    - 报错

      ```js
      const m = new Map()
      const x = 4, y = 6, w ={ w: 1}, z ={ z: 2}
      m.set(x, y)

      const m2 = new WeakMap(m) // Uncaught TypeError: Invalid value used as weak map key
      ```

    - 成功

      ```js
      const m = new Map()
      const x = { a: 1 }, y = { b: 2 }
      m.set(x, y)

      const m2 = new WeakMap(m)

      console.log(m2) // WeakMap {{…} => {…}}
      ```

### WeakMap 应用场景

- 可以用来储存 Dom 元素，当这些元素从 document 中被移除时，不会引发内存泄漏
