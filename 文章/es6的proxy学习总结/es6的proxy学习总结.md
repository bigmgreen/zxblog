## 概念

Proxy 是 es6 中新增的元编程（即对于编程语言本身进行编程，使其改变某些默认行为）特性之一，它本身也是一个对象，本文中称为“代理”，那么它是怎么进行工作的呢？我们先从构造器讲起一步一步探讨

## 构造器

Proxy 有两种构造方式，如下：

- 使用构造函数进行创建，有两个入参，第一个是需要代理的对象，第二个是需要在 Proxy 上注册的处理函数，如下：

    ```js
    const obj = { a: 1 }
    const handlers = {
        /**
        * 触发时机是： Reflect.get(..)、. 属性运算符、[..]属性运算符
        * target 被代理对象
        * key 被访问的属性名
        * context 代理本身
        */
        get(target, key, context) {
            // 自定义操作
            console.log('Proxy log：', target, key, context)

            return Reflect.get(target, key, context)
        }
    }

    const pObj = new Proxy(obj, handlers)

    pObj.a // Proxy log： {a: 1} a Proxy {a: 1}
    ```

    - 

## 应用场景

## 总结

如上所述，Proxy 的给我们在操作数据时提供了极大的便利，相信在不久以后，Proxy 技术将会成为前端领域人人必备的技能，你还有什么理由不学习它呢？
