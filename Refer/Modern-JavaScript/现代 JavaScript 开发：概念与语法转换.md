# 现代 JavaScript 开发

# ECMAScript 的过去、现在与未来

# ECMAScript 各版本特性概述

> [ECMAScript 2017（ES8）特性概述]() 整理自 [ES8 was Released and here are its Main New Features](https://parg.co/b10)，归纳于笔者的[现代 JavaScript 开发：语法基础与实践技巧]()系列文章中；也欢迎关注[前端每周清单系列](https://parg.co/bh1)获得一手资讯。

## ECMAScript 2017（ES8）Features

ECMAScript 2017 或 ES8 与 2017 年六月底由 TC39 正式发布，可以在[这里](https://www.ecma-international.org/ecma-262/8.0/index.html)浏览完整的版本；而 ES8 中代表性的特征包括了字符串填充、对象值遍历、对象的属性描述符获取、 函数参数列表与调用中的尾部逗号、异步函数、共享内存与原子操作等。

### 字符串填充
ES8 中添加了内置的字符串填充函数，分别为 padStart 与 padEnd，该函数能够通过填充字符串的首部或者尾部来保证字符串达到固定的长度；开发者可以指定填充的字符串或者使用默认的空格，函数的声明如下：
```
str.padStart(targetLength [, padString])

str.padEnd(targetLength [, padString])
```
如上所示，函数的首个参数为目标长度，即最终生成的字符串长度；第二个参数即是指定的填充字符串：
```
'es8'.padStart(2);          // 'es8'
'es8'.padStart(5);          // '  es8'
'es8'.padStart(6, 'woof');  // 'wooes8'
'es8'.padStart(14, 'wow');  // 'wowwowwowwoes8'
'es8'.padStart(7, '0');     // '0000es8'
'es8'.padEnd(2);          // 'es8'
'es8'.padEnd(5);          // 'es8  '
'es8'.padEnd(6, 'woof');  // 'es8woo'
'es8'.padEnd(14, 'wow');  // 'es8wowwowwowwo'
'es8'.padEnd(7, '6');     // 'es86666'
```
### 对象指遍历

`Object.values` 函数会返回指定对象的可枚举的属性值数组，数组中值顺序与 `for-in` 循环保持一致，函数的声明为：
```
Object.values(obj)
```
首个参数 `obj` 即为需要遍历的目标对象，它可以为某个对象或者数组（数组可以看做键为下标的对象）：
```
const obj = { x: 'xxx', y: 1 };
Object.values(obj); // ['xxx', 1]

const obj = ['e', 's', '8']; // same as { 0: 'e', 1: 's', 2: '8' };
Object.values(obj); // ['e', 's', '8']

// when we use numeric keys, the values returned in a numerical 
// order according to the keys
const obj = { 10: 'xxx', 1: 'yyy', 3: 'zzz' };
Object.values(obj); // ['yyy', 'zzz', 'xxx']
Object.values('es8'); // ['e', 's', '8']
```
而 `Object.entries` 方法则会将某个对象的可枚举属性与值按照二维数组的方式返回，数组中顺序与 `Object.values` 保持一致，该函数的声明与使用为：
```
const obj = { x: 'xxx', y: 1 };
Object.entries(obj); // [['x', 'xxx'], ['y', 1]]

const obj = ['e', 's', '8'];
Object.entries(obj); // [['0', 'e'], ['1', 's'], ['2', '8']]

const obj = { 10: 'xxx', 1: 'yyy', 3: 'zzz' };
Object.entries(obj); // [['1', 'yyy'], ['3', 'zzz'], ['10': 'xxx']]
Object.entries('es8'); // [['0', 'e'], ['1', 's'], ['2', '8']]
```
### 对象的属性描述符获取
`getOwnPropertyDescriptors` 函数会返回指定对象的某个指定属性的描述符；该属性必须是对象自己定义而不是继承自原型链，函数的声明为：
```
Object.getOwnPropertyDescriptor(obj, prop)
```
`obj` 即为源对象，而 `prop` 即为需要查看的属性名；结果中包含的键可能有 configurable、enumerable、writable、get、set 以及 value。
```
const obj = { get es8() { return 888; } };
Object.getOwnPropertyDescriptor(obj, 'es8');
// {
//   configurable: true,
//   enumerable: true,
//   get: function es8(){}, //the getter function
//   set: undefined
// }
```
### 函数参数列表与调用中的尾部逗号

该特性允许我们在定义或者调用函数时添加尾部逗号而不报错：
```
function es8(var1, var2, var3,) {
  // ...
}
es8(10, 20, 30,);
```
### 异步函数
ES8 中允许使用 async/await 语法来定义与执行异步函数，async 关键字会返回某个 AsyncFunction 对象；在内部实现中虽然异步函数与迭代器的实现原理类似，但是其并不会被转化为迭代器函数：
```
function fetchTextByPromise() {
  return new Promise(resolve => { 
    setTimeout(() => { 
      resolve("es8");
    }, 2000);
  });
}
async function sayHello() { 
  const externalFetchedText = await fetchTextByPromise();
  console.log(`Hello, ${externalFetchedText}`); // Hello, es8
}
sayHello();

console.log(1);
sayHello();
console.log(2);

// 调用结果
1 // immediately
2 // immediately
Hello, es8 // after 2 seconds
```
### 共享内存与原子操作
共享内存允许多个线程并发读写数据，而原子操作则能够进行并发控制，确保多个存在竞争关系的线程顺序执行。本部分则介绍了新的构造器 `SharedArrayBuffer` 与包含静态方法的命名空间对象 `Atomics`。`Atomic` 对象类似于 `Math`，我们无法直接创建其实例，而只能使用其提供的静态方法：
- add /sub - 增加或者减去某个位置的某个值
- and / or /xor - 进行位操作
- load - 获取值

# JavaScript 语法树与代码转化

![](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/04/1459949808babel.png)

## Babel

Babel 能够将输入的 JavaScript 代码根据不同的配置将代码进行适当地转化，其主要步骤分为解析（Parse）、转化（Transform）与生成（Generate）：

- 在解析步骤中，Babel 分别使用词法分析（Lexical Analysis）与语法分析（Syntactic Analysis）来将输入的代码转化为抽象语法树；其中词法分析步骤会将代码转化为令牌流，而语法分析步骤则是将令牌流转化为语言内置的 AST 表示。
- 在转化步骤中，Babel 会遍历上一步生成的令牌流，根据配置对节点进行添加、更新与移除等操作；Babel 本身并没有进行转化操作，而是依赖于外置的插件进行实际的转化。
- 最后的代码生成则是将上一步中经过转化的抽象语法树重新生成为代码，并且同时创建 SourceMap；代码生成相较于前两步会简单很多，其核心思想在于深度优先遍历抽象语法树，然后生成对应的代码字符串。

## 抽象语法树

抽象语法树（Abstract Syntax Tree, AST）的作用在于牢牢抓住程序的脉络，从而方便编译过程的后续环节（如代码生成）对程序进行解读。AST 就是开发者为语言量身定制的一套模型，基本上语言中的每种结构都与一种 AST 对象相对应。上文提及的解析步骤中的词法分析步骤会将代码转化为所谓的令牌流，譬如对于代码 `n * n`，其会被转化为如下数组：
```
[
  { type: { ... }, value: "n", start: 0, end: 1, loc: { ... } },
  { type: { ... }, value: "*", start: 2, end: 3, loc: { ... } },
  { type: { ... }, value: "n", start: 4, end: 5, loc: { ... } },
  ...
]
```
其中每个 `type` 是一系列描述该令牌属性的集合：
```
{
  type: {
    label: 'name',
    keyword: undefined,
    beforeExpr: false,
    startsExpr: true,
    rightAssociative: false,
    isLoop: false,
    isAssign: false,
    prefix: false,
    postfix: false,
    binop: null,
    updateContext: null
  },
  ...
}
```
这里的每一个 `type` 类似于 AST 中的节点都拥有 `start`、`end`、`loc` 等属性；在实际应用中，譬如对于 ES6 中的箭头函数，我们可以通过 `babylon` 解释器生成如下的 AST 表示：
```
// 源代码
(foo, bar) => foo + bar;

// 简化的 AST 表示
{
    "program": {
        "body": [
            {
                "type": "ExpressionStatement",
                "expression": {
                    "type": "ArrowFunctionExpression",
                    "params": [
                        {
                            "type": "Identifier",
                            "name": "foo"
                        },
                        {
                            "type": "Identifier",
                            "name": "bar"
                        }
                    ],
                    "body": {
                        "type": "BinaryExpression",
                        "left": {
                            "type": "Identifier",
                            "name": "foo"
                        },
                        "operator": "+",
                        "right": {
                            "type": "Identifier",
                            "name": "bar"
                        }
                    }
                }
            }
        ]
    }
}
```
这里可以使用[ AST Explorer ](http://astexplorer.net/)这个工具进行在线预览与编辑；在上述的 AST 表示中，顾名思义，ArrowFunctionExpression 就表示该表达式为箭头函数表达式。该函数拥有 foo 与 bar 这两个参数，参数所属的 Identifiers 类型是没有任何子节点的变量名类型；接下来我们发现加号运算符被表示为了 BinaryExpression 类型，并且其 `operator` 属性设置为 `+`，而左右两个参数分别挂载于 `left` 与 `right` 属性下。在接下来的转化步骤中，我们即是需要对这样的抽象语法树进行转换，该步骤主要由 Babel Preset 与 Plugin 控制；Babel 内部提供了 `babel-traverse` 这个库来辅助进行 AST 遍历，该库还提供了一系列内置的替换与操作接口。而经过转化之后的 AST 表示如下，在实际开发中我们也常常首先对比转化前后代码的 AST 表示的不同，以了解应该进行怎样的转化操作：
```
// AST shortened for clarity
{
    "program": {
        "type": "Program",
        "body": [
            {
                "type": "ExpressionStatement",
                "expression": {
                    "type": "Literal",
                    "value": "use strict"
                }
            },
            {
                "type": "ExpressionStatement",
                "expression": {
                    "type": "FunctionExpression",
                    "async": false,
                    "params": [
                        {
                            "type": "Identifier",
                            "name": "foo"
                        },
                        {
                            "type": "Identifier",
                            "name": "bar"
                        }
                    ],
                    "body": {
                        "type": "BlockStatement",
                        "body": [
                            {
                                "type": "ReturnStatement",
                                "argument": {
                                    "type": "BinaryExpression",
                                    "left": {
                                        "type": "Identifier",
                                        "name": "foo"
                                    },
                                    "operator": "+",
                                    "right": {
                                        "type": "Identifier",
                                        "name": "bar"
                                    }
                                }
                            }
                        ]
                    },
                    "parenthesizedExpression": true
                }
            }
        ]
    }
}
```

## 自定义插件

```
// Source Code
const func = (foo, bar) => foo + bar;

// Transformed Code
"use strict";
const _func = function(_foo, _bar) {
  return _foo + _bar;
};
```

```
import type NodePath from "babel-traverse";

export default function(babel) {
  const { types: t } = babel;

  return {
    name: "ast-transform", // not required
    visitor: {
      Identifier(path) {
        path.node.name = `_${path.node.name}`;
      },
      ArrowFunctionExpression(path: NodePath<BabelNodeArrowFunctionExpression>, state: Object) {
        // In some conversion cases, it may have already been converted to a function while this callback
        // was queued up.
        if (!path.isArrowFunctionExpression()) return;

        path.arrowFunctionToExpression({
          // While other utils may be fine inserting other arrows to make more transforms possible,
          // the arrow transform itself absolutely cannot insert new arrow functions.
          allowInsertArrow: false,
          specCompliant: !!state.opts.spec
        });
      }
    }
  };
}

```
## Flow 类型转化为 Swagger Decorator 注解
