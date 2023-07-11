# React-square
# React-井字游戏

https://zh-hans.react.dev/learn/tutorial-tic-tac-toe
将压缩包解压，打开终端并使用 cd 命令切换到你解压后的目录。
使用 npm install 安装依赖。
运行 npm start 启动本地服务器，按照提示在浏览器中查看运行效果。
## 构建棋盘

```jsx
import React from 'react'
//组件
function 棋子() {
  return <button>1</button>;
}
export default function 棋盘() {
    <棋子/>
  // ...
}
```

### 通过 props 传递数据 

```jsx
function 棋子({ value }) {
  return <button>{ value }</button>;
}
export default function 棋盘() {
    <棋子  value="1"/>
  // ...
}
```

![](https://cdn.staticaly.com/gh/845415120/picx-images-hosting@master/20230711/image.295cfcbt2mdc.webp)

### 点击交互

```jsx
//棋子组件
function 棋子({ value }) {
    function 鼠标点击事件() {
    console.log('clicked!');
  }
  return (<button  onClick={鼠标点击}>{ value }</button>);
}
export default function 棋盘() {
    <棋子  value="1"/>
  // ...
}
```

我们希望 棋子组件能够“记住”它被单击过，并用“X”填充它。

为了“记住”一些东西，组件使用 *state*。

React 提供了一个名为 `useState` 的特殊函数，可以从组件中调用它来让它“记住”一些东西

```jsx
import { useState } from 'react';

function 棋子() {
  const [value, setValue] = useState(null);

  function handleClick() {
   setValue('X');
  }
     return (<button  onClick={handleClick}>{ value }</button>);
}
```

 `value` 存储值

`setValue` 是可用于更改值的函数

![](https://cdn.staticaly.com/gh/845415120/picx-images-hosting@master/20230711/tictac-adding-x-s.4wsc3gpxjmg0.gif)

## 获取九个方框的state

`Board` 需要以某种方式知道 9 个 `Square` 组件中每个组件的 state。

**最好的方法是将游戏的 state 存储在 `Board` 父组件中，而不是每个 `Square` 中**

编辑 `Board` 组件，使其声明一个名为 `squares` 的 state 变量，该变量默认为对应于 **9 个方块的 9 个空值数组：**

```jsx
// ...
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
     <棋子 value={squares[0]} />
  );
}
```

从 Board 组件接收 `value` props

```jsx
function 棋子({value}) {
  return <button className="square">{value}</button>;
}
```



### 交替放置“X”和“O”

现在，每个 棋子都会收到一个 `value` props，对于空方块，该 props 将是 `'X'`、`'O'` 或 `null`。

接下来，你需要更改单击 `Square` 时发生的情况

### 棋子点击事件 `onSquareClick`

```jsx
function 棋子({value,onSquareClick }) {
  return (<button  onClick={onSquareClick }>{ value }</button>);
}
```

将 `鼠标点击` 函数添加到 `棋子`组件的 props 中：

在`棋盘`中声明一个函数

```jsx
<棋子 value={squares[0]} onSquareClick={handleClick} />
```

## handleClick函数更新

```jsx
  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    setSquares(nextSquares);
  }
```

`handleClick` 函数使用 JavaScript 数组的 `slice()` 方法创建 `squares` 数组（`nextSquares`）的副本。然后，`handleClick` 更新 `nextSquares` 数组，将 `X` 添加到第一个（`[0]` 索引）方块。

### 使用箭头函数在组件中使用

```
  <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
```

### 交替落子 

```jsx
export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  return (
    //...
  );
}

```

![](https://cdn.staticaly.com/gh/845415120/picx-images-hosting@master/20230711/o-replaces-x.5fu4yl016ns0.gif)

### 宣布获胜者 

添加一个名为 `calculateWinner` 的辅助函数，它接受 9 个方块的数组，检查获胜者并根据需要返回 `'X'`、`'O'` 或 `null`

```jsx
function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

增加条件判断

```jsx
function handleClick(i) {
  if (squares[i] || calculateWinner(squares)) {
    return;
  }
```

为了让玩家知道游戏何时结束，你可以显示“获胜者：X”或“获胜者：O”等文字

```jsx
  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = "Winner: " + winner;
  } else {
    status = "Next player: " + (xIsNext ? "X" : "O");
  }
```

在棋盘中增加

```jsx
    <div className="status">{status}</div>
```
