## 概念

`Proxy` 是 `es6` 中新增的元编程（即对于编程语言本身进行编程，使其改变某些默认行为）特性之一，它本身也是一个对象，通常称之为“代理”，那么它是怎么进行工作的呢？我们先从构造器讲起一步一步讲起

## 构造器

`Proxy` 有两种构造方式，如下：

- 使用*构造函数*进行创建，有两个入参，第一个是需要代理的对象，第二个是需要在 `Proxy` 上注册的处理函数，如下：

  ```js
  const obj = { a: 1 }
  const handlers = {
    /**
    * 触发时机是： Reflect.get(..)、. 属性运算符、[..]属性运算符
    * target 被代理对象
    * propKey 被访问的属性名
    * context 代理本身
    */
    get(target, propKey, context) {
      // 自定义操作
      console.log('Proxy log：', target, propKey, context)

      return Reflect.get(target, propKey, context)
    }
  }

  const pObj = new Proxy(obj, handlers) // 创建 Proxy 实例

  pObj.a // Proxy log： {a: 1} a Proxy {a: 1}
  ```

  - 按照以上方式我们就可以成功创建一个 `Proxy` 实例，通过 `pObj` 对象访问 `obj` 对象，就可以在获取到属性值前有机会执行额外的逻辑，比如上面的 `console.log(..)`

- 通过*构造函数*创建的 `Proxy` 实例，在创建后就不能修改，于是这个代理状态就会一直保持下去，那能不能创建一个能取消代理状态的实例呢？答案是可以的，如下：

  ```js
  const obj = { a: 1 }
  const handlers = {
    /**
    * 触发时机是： Reflect.get(..)、. 属性运算符、[..]属性运算符
    * target 被代理对象
    * propKey 被访问的属性名
    * context 代理本身
    */
    get(target, propKey, context) {
      // 自定义操作
      console.log('Proxy log：', target, propKey, context)

      return Reflect.get(target, propKey, context)
    }
  }

  const { proxy: pObj, revoke } = Proxy.revocable(obj, handlers) // 创建 Proxy 实例

  pObj.a // Proxy log： {a: 1} a Proxy {a: 1}

  revoke()

  pObj.a // Uncaught TypeError: Cannot perform 'get' on a proxy that has been revoked
  ```

  - 如上所述我们通过 `Proxy.revocable(..)` 成功创建了一个可以取消代理状态的的代理实例，当代理状态被取消时，再次访问代理实例会报错，试想一个这样的场景：当你把代理实例分发的第三方，使第三方通过代理访问你的数据，当数据模型变更时，使这个代理失效，这样第三方就可以通过错误获知模型已经发生变化，从而请求更新这个引用

## Reflect 说明

`Reflect` 对象也是 `es6` 中新增的元编程特性之一，它有着和 `Proxy` 一一对应的方法，部分功能和 `Object` 上的同名函数类似，这个对象的设计目的有如下：

- 将语言内部方法从 `Object` 对象转移到 `Reflect` 对象上，且未来的新方法都将只部署在 `Reflect` 对象上

- 修改某些 `Object` 对应方法的返回结果，让其变得更合理，比如：

  ```js
  // 老写法
  try {
    Object.defineProperty(target, property, attributes);
    // success
  } catch (e) {
    // failure
  }

  // 新写法
  if (Reflect.defineProperty(target, property, attributes)) {
    // success
  } else {
    // failure
  }
  ```

- 把 `Object` 对象操作从命令式都变成函数行为，比如：

  ```js
  // 老写法
  'assign' in Object // true

  // 新写法
  Reflect.has(Object, 'assign') // true
  ```

- `Reflect` 对象的方法与 `Proxy` 对象的方法一一对应，能确保对象的原生行为能够正常进行，所以它们总是一起使用，如下：

  ```js
  const obj = { a: 1 }
  const handlers = {
    set(target, propKey, proValue, context) {
      // 确认对象的属性赋值成功
      const isOk = Reflect.set(target, propKey, proValue, context)

      if (isOk) {
        console.log('Proxy set log：', target, propKey, proValue, context)
      }

      return isOk
    }
  }

  const pObj = new Proxy(obj, handlers) // 创建 Proxy 实例

  pObj.a = 6 // Proxy set log： {a: 6} a 6 Proxy {a: 6}
  ```

- 详情可查看[ECMAScript 6 入门之 Reflect](http://es6.ruanyifeng.com/#docs/reflect)

## 可代理的行为 TODO: 需要验证

- 见上面的 `handlers` 对象，其实可以在代理上定义的函数目前有 `13` 种之多，为节约篇幅，这里只列出行为的定义，实例代码可参照[ECMAScript 6 入门之 Proxy](http://es6.ruanyifeng.com/#docs/proxy)，如下：

  ```js
  const handlers = {
    /**
     * 触发方式：
     *
     * Reflect.get(pObj, 'a')
     * pObj.a
     * pObj['a']
     */
    get(target, propKey, context) {...}

    /**
     * 触发方式：
     *
     * Reflect.set(pObj, 'a', 666)
     * pObj.a = 888
     * ({ a: pObj.a } = obj)
     */
    set(target, propKey, value, context) {...}

    /**
     * 触发方式：
     *
     * Reflect.isExtensible(pObj)
     * Object.isExtensible(pObj)
     * 
     * returns Boolean
     */
    isExtensible(target) {...}

    /**
     * 触发方式：
     *
     * Reflect.deleteProperty(pObj, 'a')
     * delete pObj.a
     * 
     * returns Boolean
     */
    delete(target, propKey) {...}

    /**
     * 触发方式：
     *
     * Reflect.getOwnPropertyDescriptor(pObj, 'a')
     * Object.getOwnPropertyDescriptor(pObj, 'a')
     * 
     * returns Boolean
     */
    getOwnPropertyDescriptor(target, propKey) {...}

    /**
     * 触发方式：
     *
     * Object.defineProperty(pObj, 'b', { writable: false, value: 888 })
     * Reflect.defineProperty(pObj, 'c', { writable: false, value: 999 })
     * 
     */
    defineProperty(target, propKey) {...}

    /**
     * 触发方式：
     *
     * Reflect.getPrototypeOf(pObj)
     * Object.getPrototypeOf(pObj)
     * pObj.__proto__
     * Object.isPrototypeOf(pObj)
     * pObj instanceof Object
     * 
     */
    getPrototypeOf(target) {...}

    /**
     * 触发方式：
     *
     * Reflect.setPrototypeOf(pObj, { b: 1 });
     * Object.setPrototypeOf(pObj, { c: 2 });
     * pObj.__proto__ = { c: 66 }
     * 
     */
    setPrototypeOf(target, proto) {...}

    /**
     * 触发方式：
     *
     * Reflect.preventExtensions(pObj)
     * Object.preventExtensions(pObj)
     * 
     * 注意：当调用了此函数后，对象就变成了不可扩展的，再次调用 set 会返回 false
     * Reflect.set(pObj, 'b', 666) // false
     */
    preventExtensions(target) {...}

    /**
     * 触发时机是：
     *
     * Reflect.ownKeys(pObj)
     * Object.getOwnPropertyNames(pObj)
     * Object.getOwnPropertySymbols(pObj)
     * Object.keys(pObj)
     * for (var i in proxy) { console.log(i) }
     * JSON.stringify(pObj)
     *
     * 注意：
     *
     * 1. 该方法返回被代理的对象所有自身属性的属性名
     * 2. 使用Object.keys方法时，有三类属性会被ownKeys方法自动过滤：目标对象上不存在的属性、属性名为 Symbol 值、不可遍历（enumerable）的属性
     */
    ownKeys(target) {...}

    /**
     * 触发方式：
     *
     * Reflect.has(pObj, 'a')
     * 'a' in pObj
     * 
     * 注意：
     *
     * 1. 可以利用 has 屏蔽某些属性，从而不被 in 找到
     * 2. 原对象不可配置或者禁止扩展，has 方法会报错
     * 3. has 方法不判断属性是对象自身的还是继承的，因此调用 Object.hasOwnProperty 不能返回正确值
     * 4. for...in 中的 in 运算符，has 拦截不生效
     */
    has(target, propKey) {...}

    /**
     * 触发方式：
     *
     * pObj()
     * pObj.call()
     * pObj.apply()
     * Reflect.apply(pObj, thisObj, args) // pObj 目标函数，thisObj 目标上下文，args 函数的参数
     * 
     * 注意：被代理的对象必须是函数，否则会报错
     */
    apply(target, object, args) {...}

    /**
     * 触发方式：
     *
     * new pObj()
     * Reflect.construct(pObj, [])
     * 
     * 注意：被代理的对象必须是函数，且 construct 方法必须返回一个对象，否则会报错
     */
    construct(target, args) {..}
  }

  const obj = { a: 1 }
  const pObj = new Proxy(obj, handlers) // 创建 Proxy 实例
  ```

## 常用场景

- 在 `set` 时进行数据验证，符合条件的才能设置成功，比如属性只能设置数字，当设置布尔值时抛出错误

- 在 `get` 时进行数据格式转换，比如把源对象的属性值转换成特定格式

- 实现私有变量，比如限制某一类属性的访问和修改权限

- 实现部分操作的预警，比如在删除属性或修改属性值时抛出警告

- 实现数据绑定，将数据变化同步到各方（重点），比如 `Vue3` 就将使用 `Proxy` 作为其观察者机制，取代之前使用的 `Object.defineProperty`，取代原因如下：

  - `Object.defineProperty` 无法监控到数组下标的变化，`vue` 的解决方案是用以下几种方法来监听数组：

    ```js
    push()
    pop()
    shift()
    unshift()
    splice()
    sort()
    reverse()
    ```

    - 可以看到还是具有一定的局限性

  - `Object.defineProperty` 只能劫持对象的属性，因此要对每个对象的每个属性进行遍历，`Vue 2.x`里，是通过递归结合遍历 `data` 对象来实现对数据的监听，所以属性值也是对象的话，还需要进行深度遍历，从性能上考虑，消耗还是有点大

  - [vue3 Proxy 源码实现](https://github.com/vuejs/vue-next/blob/02478b48eb0f0cb9ef00f80216e161a288c0b220/packages/reactivity/src/baseHandlers.ts#L14)

## 结语

如上所述，`Proxy` 的给我们在操作数据时提供了极大的便利，相信在不久以后，`Proxy` 技术将会成为前端领域人人必备的技能，你还有什么理由不学习它呢？
