VNode 也叫做 Virtual DOM，其实就是一棵以 JavaScript 对象（VNode 节点）作为基础的树，用对象属性来描述节点，实际上它只是一层对真实 DOM 的抽象。最终可以通过一系列操作使这棵树映射到真实环境上。

## 如何生成 VNode？

比如我目前有这么一个 Vue 组件：

```html
<template>
  <span class="demo" v-show="isShow"> This is a span. </span>
</template>
```

转换成 VNode 后的情况：

```js
{
    tag: 'span',
    data: {
        /* 指令集合数组 */
        directives: [
            {
                /* v-show指令 */
                rawName: 'v-show',
                expression: 'isShow',
                name: 'show',
                value: true
            }
        ],
        /* 静态class */
        staticClass: 'demo'
    },
    text: undefined,
    children: [
        /* 子节点是一个文本VNode节点 */
        {
            tag: undefined,
            data: undefined,
            text: 'This is a span.',
            children: undefined
        }
    ]
}
```

那么如何生成的呢？

## 实现一个 VNode

```js
class VNode {
  constructor(tag, data, children, text, elm) {
    /*当前节点的标签名*/
    this.tag = tag;
    /*当前节点的一些数据信息，比如props、attrs等数据*/
    this.data = data;
    /*当前节点的子节点，是一个数组*/
    this.children = children;
    /*当前节点的文本*/
    this.text = text;
    /*当前虚拟节点对应的真实dom节点*/
    this.elm = elm;
  }
}
```

上面的 html 就可以使用下面 render 方法生产 VNode：

```js
function render() {
  return new VNode(
    "span",
    {
      /* 指令集合数组 */
      directives: [
        {
          /* v-show指令 */
          rawName: "v-show",
          expression: "isShow",
          name: "show",
          value: true,
        },
      ],
      /* 静态class */
      staticClass: "demo",
    },
    [new VNode(undefined, undefined, undefined, "This is a span.")]
  );
}
```
