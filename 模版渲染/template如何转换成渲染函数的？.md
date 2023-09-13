compile 编译可以分成 `parse`、`optimize` 与 `generate` 三个阶段，最终需要得到渲染函数 render function。

示例 template：

```html
<div :class="c" class="demo" v-if="isShow">
  <span v-for="item in sz">{{item}}</span>
</div>
```

## parse

parse 会用正则等方式将 template 模板中进行字符串解析，得到指令、class、style 等数据，形成 AST.

> AST 是抽象语法树（Abstract Syntax Tree）的缩写。它是在计算机科学中用于表示源代码语法结构的一种数据结构。在编译器和解释器中，AST 通常是从源代码生成的中间表示形式，用于进行后续的分析、优化和执行。

这个过程比较复杂，会涉及到比较多的正则进行字符串解析，我们来看一下得到的 AST 的样子。

```js
{
    /* 标签属性的map，记录了标签上属性 */
    'attrsMap': {
        ':class': 'c',
        'class': 'demo',
        'v-if': 'isShow'
    },
    /* 解析得到的:class */
    'classBinding': 'c',
    /* 标签属性v-if */
    'if': 'isShow',
    /* v-if的条件 */
    'ifConditions': [
        {
            'exp': 'isShow'
        }
    ],
    /* 标签属性class */
    'staticClass': 'demo',
    /* 标签的tag */
    'tag': 'div',
    /* 子标签数组 */
    'children': [
        {
            'attrsMap': {
                'v-for': "item in sz"
            },
            /* for循环的参数 */
            'alias': "item",
            /* for循环的对象 */
            'for': 'sz',
            /* for循环是否已经被处理的标记位 */
            'forProcessed': true,
            'tag': 'span',
            'children': [
                {
                    /* 表达式，_s是一个转字符串的函数 */
                    'expression': '_s(item)',
                    'text': '{{item}}'
                }
            ]
        }
    ]
}


```

正则非常麻烦，这里略过。

然后继续。

因为我们解析 template 采用循环进行字符串匹配的方式，所以每匹配解析完一段我们需要将已经匹配掉的去掉，头部的指针指向接下来需要匹配的部分。

## parseHTML
