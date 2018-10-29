# 介紹 web3.eth.Contract - 建立合約實例與預設配置

web3.eth.Contract 可以讓你輕鬆地跟以太坊區塊鏈上的智能合約互動。

### 建立一個合約實例

```js
new web3.eth.Contract(jsonInterface[, address][, options])
```

> [ ] 代表的是可選擇，沒有 [ ] 代表的是必填。

**傳入參數**

* `jsonInterface` 物件 : 合約的 json interface
* `address` 字串 : 智能合約位址，也可以透過 `myContract.options.address = '0x1234..'` 語法設定。
* `options` 物件
  * `from` 字串
  * `gasPrice` 字串
  * `gas` 數字：等於同 gas limit
  * `data` 字串

**回傳值**

合約實例 (instance)，包含所有的方法跟事件。

**範例**

```js
var myContract = new web3.eth.Contract(
  [...],
  '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
  {
    from: '0x1234567890123456789012345678901234567891',
    gasPrice: '20000000000'// = 20 gwei
  }
);
```

### 預設配置

```
myContract.options
```

替建立合約的 `transaction` 做屬性設定

**屬性**

* `address` 字串
* `jsonInterface` 陣列
* `data` 字串：合約的 byte code
* `from` 字串
* `gasPrice` 字串
* `gas` 數字：等於 gas limit

**範例**

```js
myContract.options;
> {
    address: '0x1234567890123456789012345678901234567891',
    jsonInterface: [...],
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    gasPrice: '10000000000000',
    gas: 1000000
}

myContract.options.from = '0x1234567890123456789012345678901234567891';
myContract.options.gasPrice = '20000000000000';
myContract.options.gas = 5000000; // gas limit
```

#### options.address

所有從 `web3.js` 建立的 transaction 會註記這組 address 於 transaction 的 `to` 值。

```js
myContract.options.address;
> '0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae'

// 設定 address
myContract.options.address = '0x1234FFDD...';
```

#### options.jsonInterface

可以參考官方文件的 [json interface](thedocs.io/en/1.0/glossary.html#glossary-json-interface) 及 [Contract ABI Specification](https://solidity.readthedocs.io/en/develop/abi-spec.html) 章節

```js
myContract.options.jsonInterface;
> [{
    "type":"function",
    "name":"foo",
    "inputs": [{"name":"a","type":"uint256"}],
    "outputs": [{"name":"b","type":"address"}]
},{
    "type":"event",
    "name":"Event"
    "inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"bytes32","indexed":false}],
}]

// 設定 json interface
myContract.options.jsonInterface = [...];
```
