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

function tokenizer(input) {
  let tokens = []
  let current = 0;

  while(current < input.length) {
    let char = input[current]
    // '('  ')' 括号
    if(char === '(' || char === ')') {
      tokens.push({
        type: 'para',
        value: char
      })
      current++;
      continue;
    }
    //  空格
    let WHITESPACE = /\s/;
    if(WHITESPACE.test(char)) {
      current++;
      continue;
    }
    // 0-9 数字
    let NUMBERS = /[0-9]/;
    if(NUMBERS.test(char)) {
      //...
      let value = '';
      while(NUMBERS.test(char)) {
        value += char;
        char = input[++current]
      }
      tokens.push({
        type: 'number',
        value
      })
      continue;
    }
    // string "xx"  字符串
    if(char === '"') {
      // ...
    }
    // name
    let LETTERS = /[a-z]/i;
    if(LETTERS) {
      // ...
    }

    throw new TypeErroe(char)
  }
  return tokens
}

function parser(tokens) {


}

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

1. Program -- AST顶端节点（起始位置）
2. CallExpression (add) -- 移动到 Program body 的 第一个元素
3. NumberLiteral (2)  -- 移动到 CallExpression params 的第一个元素
4. CallExpression (subtract)  --  移动到 CallExpression params 的第二个元素
5. NumberLiteral (4) --  移动到 CallExpression params 的第一个元素
6. NumberLiteral (2) -- 移动到 CallExpression params 的第二个元素



var visitor = {
  NumberLiteral(node, parent) {},
  CallExpression(node, parent) {},
};



- Program
  - CallExpression
    - NumberLiteral
    - CallExpression
      - NumberLiteral
      - NumberLiteral



-> Program (enter)
  -> CallExpression (enter)
    -> Number Literal (enter)
    <- Number Literal (exit)
    -> Call Expression (enter)
       -> Number Literal (enter)
       <- Number Literal (exit)
       -> Number Literal (enter)
       <- Number Literal (exit)
    <- CallExpression (exit)
  <- CallExpression (exit)
<- Program (exit)


var visitor = {
  NumberLiteral: {
    enter(node, parent) {},
    exit(node, parent) {},
  }
};

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