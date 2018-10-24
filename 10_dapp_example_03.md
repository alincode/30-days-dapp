# 實戰練習：簡化版 King of the Ether (2/3)

使用到的語法

* new Web3
* web3.currentProvider
* new web3.eth.Contract

### Step1: 匯入模組

如果對這些模組不是很熟悉，可以先回顧[前端輕量化模組](04_lightweight_module.md)篇。

```js
const Web3 = require('web3');
const html = require('nanohtml');
const csjs = require('csjs-inject');
const morphdom = require('morphdom');
```

### Step2: 初始化 web3.js

```js
if (typeof web3 !== 'undefined') {
  web3 = new Web3(web3.currentProvider);
} else {
  alert('initial failed');
}
```

### Step3: 連接智能合約

```js
const ABI = require('./abi.json');

const DEFAULT_ADDRESS = '0x0c972a74a9806f249968720035ebda65b653360c';
const contractAddress = localStorage.constract || DEFAULT_ADDRESS;
const myContract = new web3.eth.Contract(ABI, contractAddress);
```

### Step4: 宣告 CSS inject 物件

```js
const css = csjs `
  .box {
    margin: 10px;
  }
  .input {
    margin-top: 10px;
    margin-right: 10px;
    width: 500px;
    font-size: 20px;
  }
  .button {
    margin-top: 10px;
    margin-right: 10px;
    font-size: 20px;
    width: 180px;
    background-color: #4CAF50;
    color: white;
  }
  .result {
    padding: 10px;
    font-size: 40px;
    color: red;
  }
  img {
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 5px;
    width: 150px;
  }
`
```

原始碼：<https://github.com/alincode/king-sandbox>