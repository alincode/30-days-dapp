# web3.js 初始化

### 安裝 web3.js

方法一：使用 `NPM` 來安裝 `web3.js`

```
npm install --save web3
```

方法二：使用 CDN

```html
<script src="https://cdn.jsdelivr.net/gh/ethereum/web3.js/dist/web3.min.js"></script>
```

方法三：從 [Github 下載](https://github.com/ethereum/web3.js) 打包好的`dist/web3.min.js` 檔案。

### provider

指向你要連接的 `JSON-RPC Server` 位置

**為 web3 初始化 provider**

```js
// 引入模組
const Web3 = require('web3');

// HTTP provider
const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));

// Socket provider
const web3 = new Web3(new Web3.providers.WebsocketProvider('ws://localhost:8546'));

// IPC provider
const net = require('net');
const web3 = new Web3(new Web3.providers.IpcProvider('/Users/myuser/Library/Ethereum/geth.ipc', net));
```

**更換 provider**

```js
web3.setProvider(new Web3.providers.HttpProvider('http://localhost:8545'));
web3.setProvider(new Web3.providers.WebsocketProvider('ws://localhost:8546'));
web3.setProvider(new Web3.providers.IpcProvider('/Users/myuser/Library/Ethereum/geth.ipc', net));
```

**currentProvider**

常與 `MetaMask` 搭配使用

```js
const web3 = new Web3(web3.currentProvider);
```

## web3.eth

**web3.eth.getBalance**

查詢餘額


語法
```js
web3.eth.getBalance(address [, defaultBlock] [, callback])
```

範例
```js
const address = '0x06012c8cf97BEaD5deAe237070F9587f8E7A266d';
web3.eth.getBalance(address, (err, balance) => {
  
});
```