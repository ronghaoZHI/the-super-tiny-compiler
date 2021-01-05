### purpose

```javascript

'(add 2 (subtract 4 2))' ==> 'add(2, subtract(4, 2))'

```


### tokenize

```javascript
const input = '(add 2 (subtract 4 2))'

const tokens = [
  { type: 'paren',  value: '('        },
  { type: 'name',   value: 'add'      },
  { type: 'number', value: '2'        },
  { type: 'paren',  value: '('        },
  { type: 'name',   value: 'subtract' },
  { type: 'number', value: '4'        },
  { type: 'number', value: '2'        },
  { type: 'paren',  value: ')'        },
  { type: 'paren',  value: ')'        }
];

```

### parser

```javascript
const ast = {
  type: 'Program',
  body: [{
    type: 'CallExpression',
    name: 'add',
    params: [{
      type: 'NumberLiteral',
      value: '2'
    }, {
      type: 'CallExpression',
      name: 'subtract',
      params: [{
        type: 'NumberLiteral',
        value: '4'
      }, {
        type: 'NumberLiteral',
        value: '2'
      }]
    }]
  }]
};
```
### transformer

下一个阶段 -- 转换。 同样，这只是
  从上述最后一步获取 AST 并对其进行更改。 它可以操纵
  AST 当作相同的语言进行处理，也可以将其翻译成一种全新的语言。

  下面看一下 如何转换 AST。

或许，你已经发现了，在AST 中包含着 看起来都很相似的元素，这些对象都有 type 属性，每一个都属于一个 AST 节点，这些定义了属性的节点在其上都描述了一个树的独立的部分。

在转换AST时，可以通过 添加/删除/替换属性 来操作节点
，可以添加新节点、删除节点、或者
丢弃现有的 AST，并基于它创建一个全新的 AST。

因为目标是转换成一种新的语言，因此将重点放在创建一种
  针对目标语言的全新 AST。

遍历 AST 

为了能访问到所有的节点，需要利用深度优先算法来遍历整个 AST。

```javascript
const ast = {
  type: 'Program',
  body: [{
    type: 'CallExpression',
    name: 'add',
    params: [{
      type: 'NumberLiteral',
      value: '2'
    }, {
      type: 'CallExpression',
      name: 'subtract',
      params: [{
        type: 'NumberLiteral',
        value: '4'
      }, {
        type: 'NumberLiteral',
        value: '2'
      }]
    }]
  }]
};
```
对于上述 AST 我们需要做一些处理：

1. Program -- AST顶级起始处理位置
2. CallExpression (add) --  
3. NumberLiteral (2) 
4. CallExpression (subtract)
5. NumberLiteral (4)
6. NumberLiteral (2)


```javascript
const newAst = {
  type: 'Program',
  body: [{
    type: 'ExpressionStatement',
    expression: {
      type: 'CallExpression',
      callee: {
        type: 'Identifier',
        name: 'add'
      },
      arguments: [{
        type: 'NumberLiteral',
        value: '2'
      }, {
        type: 'CallExpression',
        callee: {
          type: 'Identifier',
          name: 'subtract'
        },
        arguments: [{
          type: 'NumberLiteral',
          value: '4'
        }, {
          type: 'NumberLiteral',
          value: '2'
        }]
      }]
    }
  }]
};
```


## codeGenerator

```javascript
'add(2, subtract(4, 2))'
```

## finally