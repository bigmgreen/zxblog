# 写在最前面

1. flex 布局现在已经得到了广泛使用，尤其是在移动端

2. 它有很多属性，使用方式非常灵活，但是大多数情况下也只是应用到了寥寥几个属性，比如：align-items、align-content、align-self、justify-content 等

3. 固然这几个常用属性就可以帮助我们实现大多数布局效果，但是如果深入理解下其他属性的特点，你会发现，flex 还能做到更多

4. 本篇文章主要讲述以下属性：
    - order
    - flex
        - flex-grow
        - flex-shrink
        - flex-basis
    - flex-flow
        - flex-direction
        - flex-wrap

## 属性介绍、应用案例

1. order: 用整数值来定义排列顺序，数值小的排在前面，可以为负值

    - 就拿双飞翼布局举例，它要求页面分为左中右三部分，左右两边宽度固定，中间宽度自适应，代码如下：

    ```html
    <div>
      <div class="main">Main</div>
    　<div class="left">Left</div>
    　<div class="right">Right</div>
    </div>
    ```

    ```css
    .main {
      background: #D6D6D6;
      width: 100%;
      float: left;
      padding: 0 100px;
      box-sizing: border-box;
    }

    .left {
      background: #E79F6D;
      width: 100px;
      float: left;
      margin-left: -100%;
    }

    .right {
      background: #77BBDD;
      width: 100px;
      float: left;
      margin-right: -100px;
      position: relative;
      right: 100px;
    }
    ```

    - 双飞翼布局是一个很经典的案例，现在我们用 order 配合其他属性来实现它，代码如下：

    ```html
    <div class="content">
      <div class="main">Main</div>
      <div class="left">Left</div>
      <div class="right">Right</div>
    </div>
    ```

    ```css
    .content {
      display: flex;
    }

    .main {
      order: 2;
      background: #D6D6D6;
      flex: 4;
    }

    .left {
      order: 1;
      flex: 1;
      background: #E79F6D;
    }

    .right {
      order: 3;
      flex: 1;
      background: #77BBDD;
    }
    ```

    - 通过上面的案例，相信读者已经对 order 的应用场景有了判断

2. flex-flow: 复合属性，<' flex-direction '> || <' flex-wrap '>

    - flex-direction：定义弹性盒子元素的排列方向
        - row：横向从左到右排列（浏览器默认）
        - row-reverse：对齐方式与 row 相反
        - column：纵向从上往下排列
        - column-reverse：对齐方式与 column 相反
        - 以上属性的反转值不影响元素的绘制，语音和导航顺序，只改变流动方向。这与 <' writing-mode '> 和 <' direction '> 相同
        - 这个属性单独使用就跟上面说的一样，很简单，也很好理解，主要还是和其他属性组合在一起以发挥很大的作用，读者先有个印象，我会在案例中展示具体代码

    - flex-wrap：控制 flex 容器是单行或者多行
        - nowrap：当 flex 容器为单行，flex 子项可能会溢出容器（浏览器默认）
        - wrap：当 flex 容器为多行，flex 子项溢出的部分会被放置到新行，子项内部会发生断行
        - wrap-reverse：反转 wrap 排列
        - 由上面的定义可知使用了 flex 布局后，子项元素是默认排列在一行的，所以在遇到需要展示多行的情况下，这个属性是要设置为 wrap 了，用到的机会还是很多的

3. flex: 复合属性，none | <' flex-grow '> <' flex-shrink >'? || <' flex-basis '>

    - flex-grow: 用数值来定义扩展比率，不可以为负值（默认值是 0）

        - 先看看老版的左边宽度固定，右边宽度自适应布局来说，代码如下：

        ```html
        <div class="left">Left</div>
        <div class="main">Main</div>
        ```

        ```css
        .left {
          float: left;
          width: 220px;
          background-color: green;
        }

        .main {
          background-color: orange;
          margin-left: 220px;
        }
        ```

        - 这是也一个很经典的案例，现在我们用 flex-grow 来实现它，代码如下：

        ```html
        <div class="content">
          <div class="main">Main</div>
          <div class="left">Left</div>
        </div>
        ```

        ```css
        .main {
          background-color: orange;
          flex-grow: 1;
        }

        .content {
          display: flex;
        }

        .left {
          width: 220px;
          background-color: green;
        }
        ```

        - 再来看一个案例，有底部导航栏的布局（导航栏固定不动，内容可以滚动），代码如下：

        ```html
        <div class="main">...</div>
        <div class="footer">footer</div>
        ```

        ```css
        .main {
          padding-bottom: 50px;
        }

        .footer {
          background-color: green;
          position: fixed;
          left: 0;
          bottom: 0;
          height: 50px;
          width: 100%;
        }
        ```

        - 现在让我们来搭配 flex-direction 和 flex-flow 属性来实现它，代码如下：

        ```html
        <div class="content">
          <div class="main">Main</div>
          <div class="left">Left</div>
        </div>
        ```

        ```css
        .content {
          display: flex;
          flex-direction: column;
          height: 100vh;
        }

        .main {
          background-color: orange;
          flex-grow: 1;
          overflow: auto;
        }

        .left {
          background-color: green;
        }
        ```

        - 再引申下，上中下布局（上下固定不动，中滚动）是不是也就轻而易举了，留给读者

        - *我们平时设置的 `flex: 1` 其实就是设置的是 `flex-grow: 1`, 后面两个参数使用默认*

        - 总结：老方式实现上面布局代码固然是要少些，但是可读性欠佳；flex 虽然元素和属性多一点，但是它不仅能实现上面的各种布局，并且还能解决老式布局的一些问题：

          - float 是用来实现文字图片环绕效果的属性，在这里不是很合适，并且使用了浮动，相应的就需要清除浮动

          - fixed 的元素在 PC 端还好，但是在移动端，会有诸多问题

          - 所以当遇到类似需求时 flex 应该排在第一位了~

    - flex-shrink: 用数值来定义收缩比率，不允许负值（默认值是 1）

        - 这个属性不常用，在父项空间溢出时才会发挥作用，默认值是 1，它会根据其给定的值和父元素宽度重新计算各个子项的宽度，这时显式定义的宽度就不起作用了，代码如下：

        ```html
        <ul class="flex">
          <li>a</li>
          <li>b</li>
          <li>c</li>
        </ul>
        ```

        ```css
        .flex {
          display: flex;
          width: 400px;
          list-style: none;
        }

        .flex li {
          width: 200px;
          background-color: #0ff;
        }

        .flex li:nth-child(2) {
          background-color: #564656;
        }

        .flex li:nth-child(3) {
          flex-shrink: 3;
          background-color: #dd0;
        }
        ```

        1. 根据默认值 1 来计算，可以看到空间分成5份，其中 a 占 1 份，b 占 1 份，c 占 3 分，即 `1:1:3`
        2. 通过收缩因子，可得 `200 * 1 + 200 * 1 + 200 * 3 = 1000px`
        3. 于是我们可以计算a,b,c将被移除的溢出量是多少：

            ```txt
            - a 被移除溢出量：(200 * 1/ 1000) * 200，即约等于 40px
            - b 被移除溢出量：(200 * 1/ 1000) * 200，即约等于 40px
            - c 被移除溢出量：(200 * 3/ 1000) * 200，即约等于 120px
            ```

        4. 最后 a, b, c 的实际宽度分别为：200 - 40 = 160px, 200 - 40 = 160px, 200 - 120 = 80px

    - flex-basis: 设置或检索弹性盒伸缩基准值 <'length'> | <'percentage'> | auto
      - length | percentage: 用长度值或者百分比来定义宽度，不允许负值
      - auto：无特定宽度值，取决于其它属性值
      - *若所有子元素的基准值之和大于剩余空间，则会根据每项设置的基准值，按比率伸缩剩余空间*
      - 这个值很少用到这里就不做介绍了

## 结语

1. 通过上面的分析与实践，相信读者已经对 flex 有了更深入的理解，帮助大家更好的使用 flex，这正是笔者所期望的

2. 目前实践上很多布局方式，都可以使用 flex 来做到，快快的使用放弃 float、position、margin 来布局吧，这样不仅违背了其属性所代表的意义，而且其他还需要其他属性来辅助，写出来的代码不是很直观，甚至复杂

3. 笔者后面还会发掘更有趣的知识点供大家学习，有兴趣可以持续关注
