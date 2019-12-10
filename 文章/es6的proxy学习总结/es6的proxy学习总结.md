## 概念

Proxy 是 es6 中新增的元编程（即对于编程语言本身进行编程，使其改变某些默认行为）特性之一，它本身也是一个对象，通常称之为“代理”，那么它是怎么进行工作的呢？我们先从构造器讲起一步一步讲起

## 构造器

Proxy 有两种构造方式，如下：

- 使用*构造函数*进行创建，有两个入参，第一个是需要代理的对象，第二个是需要在 Proxy 上注册的处理函数，如下：

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

  - 按照以上方式我们就可以成功创建一个 Proxy 实例，通过 pObj 对象访问 obj 对象，就可以在获取到属性值前有机会执行额外的逻辑，比如上面的 `console.log(..)`

- 通过*构造函数*创建的 Proxy 实例，在创建后就不能修改，于是这个代理状态就会一直保持下去，那能不能创建一个能取消代理状态的实例呢？答案是可以的，如下：

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

Reflect 对象也是 es6 中新增的元编程特性之一，它有着和 Proxy 一一对应的方法，部分功能和 Object 上的同名函数类似，这个对象的设计目的有如下：

  - 将语言内部方法从 Object 对象转移到 Reflect 对象上，且未来的新方法都将只部署在 Reflect 对象上

  - 修改某些 Object 对应方法的返回结果，让其变得更合理，比如：

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

  - 把 Object 对象操作从命令式都变成函数行为，比如：

    ```js
    // 老写法
    'assign' in Object // true

    // 新写法
    Reflect.has(Object, 'assign') // true
    ```

  - Reflect对象的方法与Proxy对象的方法一一对应，能确保对象的原生行为能够正常进行，所以它们总是一起使用，如下：

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

- 见上面的 `handlers` 对象，可以在代理上定义的函数目前有 13 种，为节约篇幅，这里只列出行为的定义，实例代码可参照[ECMAScript 6 入门之 Proxy](http://es6.ruanyifeng.com/#docs/proxy)，如下：

  ```js
  const handlers = {
    // 触发时机是：Reflect.get(..)、. 属性运算符、[..]属性运算符
    get(target, propKey, context) {...}

    // 触发时机是：Reflect.set(..)、赋值运算符 =、目标为对象属性的解构赋值，返回一个布尔值
    set(target, propKey, value, context) {...}

    // 触发时机是：Reflect.isExtensible(..) 或 Object.isExtensible(..)，返回一个布尔值
    isExtensible(target) {...}

    // 触发时机是：Reflect.deleteProperty(..)或 delete，返回一个布尔值
    delete(target, propKey) {...}
    
    // 触发时机是：Reflect.getOwnPropertyDescriptor(..) 或 Object.getOwnPropertyDescriptor(..)
    getOwnPropertyDescriptor(target, propKey) {...}

    // 触发时机是：Reflect.defineProperty(..) 或 Object.defineProperty(..)
    defineProperty(target, propKey) {...}

    // 触发时机是：Reflect.getPrototypeOf(..)、Object.getPrototypeOf(..)、__proto__、Object.isPrototypeOf(..)、instanceof
    getPrototypeOf(target) {...}

    // 触发时机是：Reflect.setPrototypeOf(..)、Object.setPrototypeOf(..)、__proto__
    setPrototypeOf(target, proto) {...}

    // 触发时机是：Reflect.preventExtensions(..) 或 Object.preventExtensions(..)
    preventExtensions(target) {...}

    /**
     * 触发时机是：
     * 
     * Object.getOwnPropertyNames(..)
     * Object.getOwnPropertySymbols(..)
     * Object.keys(..)
     * for...in
     * Reflect.getOwnPropertyNames(..)
     * Reflect.getOwnPropertySymbols(..)
     * Reflect.ownKeys(..)
     * JSON.stringify(..)
     * 
     * 该方法返回目标对象所有自身的属性的属性名
     */
    ownKeys(target) {...}

    // 触发时机是：Reflect.has(..)、Object.hasOwnProperty(..)、'prop' in obj
    has(target, propKey) {...}

    // 如果目标是函数，触发时机是：Reflect.apply(..)、call、apply，()调用运算符
    apply(target, object, args) {...}

    // 如果目标是构造函数，触发时机是：Reflect.construct(..) 或 new
    construct(target, args) {...}
  }
  ```

## 应用场景

## 结语

如上所述，Proxy 的给我们在操作数据时提供了极大的便利，相信在不久以后，Proxy 技术将会成为前端领域人人必备的技能，你还有什么理由不学习它呢？
