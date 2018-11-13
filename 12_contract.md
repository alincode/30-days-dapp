# 介紹 web3.eth.Contract - Methods

### clone 函示

```js
myContract.clone()
```

複製目前合約的實例

**傳入參數**

無

**回傳值**

新的合約實例物件

**範例**

```js
var contract1 = new eth.Contract(abi, address, {gasPrice: '12345678', from: fromAddress});

var contract2 = contract1.clone();
contract2.options.address = address2;

(contract1.options.address !== contract2.options.address);
> true
```

### deploy 函示

```js
myContract.deploy(options)
```

發布合約

**傳入參數**

options 物件

* data 字串：合約的 byte code
* arguments 陣列

**回傳值**

transaction 物件

* arguments 陣列：這個值取決於傳入參數的 arguments 陣列值
* call 函示
* send 函示
* estimateGas 函示
* encodeABI 函示

**範例**

```js
myContract.deploy({
    data: '0x12345...',
    arguments: [123, 'My String']
})
.send({
    from: '0x1234567890123456789012345678901234567891',
    gas: 1500000,
    gasPrice: '30000000000000'
}, function(error, transactionHash){ ... })
.on('error', function(error){ ... })
.on('transactionHash', function(transactionHash){ ... })
.on('receipt', function(receipt){
   console.log(receipt.contractAddress);
})
.on('confirmation', function(confirmationNumber, receipt){ ... })
.then(function(newContractInstance){
    console.log(newContractInstance.options.address);
});
```

**options 分別設定**

```js
myContract.options.data = '0x12345...';

myContract.deploy({
    arguments: [123, 'My String']
})
.send({
    from: '0x1234567890123456789012345678901234567891',
    gas: 1500000,
    gasPrice: '30000000000000'
})
.then(function(newContractInstance){
    console.log(newContractInstance.options.address);
});
```

**encodeABI 函示**

```js
myContract.deploy({
    data: '0x12345...',
    arguments: [123, 'My String']
})
.encodeABI();
> '0x12345...0000012345678765432'
```

**estimateGas 函示**

```js
myContract.deploy({
    data: '0x12345...',
    arguments: [123, 'My String']
})
.estimateGas(function(err, gas){
    console.log(gas);
});
```

### methods

```js
myContract.methods.myMethod([param1[, param2[, ...]]])
```

為 method 建立一個 transaction 物件，可以執行 `called`、`send`、`estimated` 函示。

智能合約的 method，可以透過以下方式呼叫：

* `myContract.methods.myMethod(123)`
* `myContract.methods['myMethod(uint256)'](123)`
* The signature: `myContract.methods['0x58cf5f10'](123)`

透過 Javascript 的合約物件，允許呼叫相同名稱，但不同傳入參數的函示。

**傳入參數**

method 的傳入參數，依賴於智能合約的 method，定義在 JSON interface 中。

**回傳值**

transaction 物件

* arguments 陣列：這個值取決於傳入參數的 arguments 陣列值
* call 函示
* send 函示
* estimateGas 函示
* encodeABI 函示

更詳細的 methods 使用方式，可以看下面的範例：

#### 範例

```js
myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, result){
    ...
});
```

```js
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(receipt){
    // 當情境是 contract.deploy({...}).send() 時，receipt 會是合約的實例。
});
```

除了 send 之外，還有聆聽事件

```js
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.on('transactionHash', function(hash){
    ...
})
.on('receipt', function(receipt){
    ...
})
.on('confirmation', function(confirmationNumber, receipt){
    ...
})
.on('error', console.error);
```

#### methods.myMethod.call 函示

```js
myContract.methods.myMethod([param1[, param2[, ...]]]).call(options[, callback])
```

將會呼叫執行在 `EVM` 環境的智能合約 `view` 或 `pure` 方法，並且不會送出任何 transaction。

**傳入參數**

1. `options` 物件 (可選)
  * from 字串 (可選)
  * gasPrice 字串 (可選)
  * gas 數字 (可選)：gas limit
2. `callback` 函示 (可選)：當智能合約的 method 被執行後，`callback` 函示會被觸發，函示的第一個參數是 error 物件，第二個參數是從智能合約傳過來的結果。

**回傳值**

回傳一個 Promise，內容會是一個值或是一個物件，如果智能合約的 mehtod 回傳值是多個值，那麼將會收到一個物件裡包含多個值。

**範例**

使用 callback 語法

```js
myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, result){
    ...
});
```

使用 promise 語法

```js
myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(result){
    ...
});
```

多個回傳值

```js
// Solidity
contract MyContract {
    function myFunction() returns(uint256 myNumber, string myString) {
        return (23456, "Hello!%");
    }
}
```

```js
// web3.js
var MyContract = new web3.eth.Contract(abi, address);
MyContract.methods.myFunction().call()
.then(console.log);
> Result {
    myNumber: '23456',
    myString: 'Hello!%',
    // 如果呼叫合約裡的 method，沒有定義回傳值名字，將會以這樣子呈現。
    0: '23456', 
    1: 'Hello!%'
}
```

單一回傳值

```js
// Solidity
contract MyContract {
    function myFunction() returns(string myString) {
        return "Hello!%";
    }
}
```

```js
// web3.js
var MyContract = new web3.eth.Contract(abi, address);
MyContract.methods.myFunction().call()
.then(console.log);
> "Hello!%"
```

#### methods.myMethod.send 函示

```js
myContract.methods.myMethod([param1[, param2[, ...]]]).send(options[, callback])
```

執行這個 method 後，合約會送出一個交易。

**傳入參數**

1. `options` 物件 (可選)
  * from 字串 (可選)
  * gasPrice 字串 (可選)
  * gas 數字 (可選)：gas limit
  * value (可選)：型別支援 `Number|String|BN|BigNumber`，轉帳金額單位是 `wei`。
2. `callback` 函示 (可選)：第一個參數是 error 物件，第二個參數是 32 bytes 的交易 hash 值。

**回傳值**

event emitter 與 Promise 整合

`PromiEvent`: A promise combined event emitter. Will be resolved when the transaction receipt is available, OR if this `send()` is called from a `someContract.deploy()`, then the promise will resolve with the new contract instance. Additionally the following events are available:


* 聆聽 `transactionHash` 事件：當交易送出後，收到事件通知，接收交易 hash 字串。
* 聆聽 `receipt` 事件：當交易 receipt 是有效的時候，會收到事件通知，接收 receipt 物件。
* 聆聽 `confirmation` 事件：交易被確認後，收到事件通知，接收的第一個參數是 `confirmationNumber`，第二個參數是 `receipt`。
* 聆聽 `error` 事件：當交易發送時發生錯誤，則事件會被觸發，接收一個 Error 物件。假如沒有足夠的 gas，第二個參數會是 `receipt`。

使用 callback 語法

```js
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, transactionHash){
    ...
});
```

使用 promise 語法

```js
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(receipt){
    // receipt can also be a new contract instance, when coming from a "contract.deploy({...}).send()"
});
```

使用 event emitter

```js
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.on('transactionHash', function(hash){
    ...
})
.on('confirmation', function(confirmationNumber, receipt){
    ...
})
.on('receipt', function(receipt){
    // receipt example
    console.log(receipt);
    > {
        "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
        "transactionIndex": 0,
        "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
        "blockNumber": 3,
        "contractAddress": "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
        "cumulativeGasUsed": 314159,
        "gasUsed": 30234,
        "events": {
            "MyEvent": {
                returnValues: {
                    myIndexedParam: 20,
                    myOtherIndexedParam: '0x123456789...',
                    myNonIndexParam: 'My String'
                },
                raw: {
                    data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
                    topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
                },
                event: 'MyEvent',
                signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
                logIndex: 0,
                transactionIndex: 0,
                transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
                blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
                blockNumber: 1234,
                address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
            },
            "MyOtherEvent": {
                ...
            },
            "MyMultipleEvent":[{...}, {...}] 
        }
    }
})
.on('error', console.error);
```

#### methods.myMethod.estimateGas 函示

```js
myContract.methods.myMethod([param1[, param2[, ...]]]).estimateGas(options[, callback])
```

預估執行合約 method 需要花費的 gas，估算的結果跟實際情況可能會有差異，因為合約的狀態，可能會依照兩者之間的時間不同。

**傳入參數**

1. `options` 物件 (可選)
  * from 字串 (可選)
  * gas 數字 (可選)：gas limit
  * value (可選)：型別支援 `Number|String|BN|BigNumber`，轉帳金額單位是 `wei`。
2. `callback` 函示 (可選)：第一個參數是 error 物件，第二個參數是預估的 gas 數量。

**回傳值**

一個 `Promise` 物件，內含一個數字，數字的意思是估計的 gas 數量。

**範例**

使用 `callback` 語法

```js
myContract.methods.myMethod(123).estimateGas({gas: 5000000}, function(error, gasAmount){
    if(gasAmount == 5000000)
        console.log('Method ran out of gas');
});
```

使用 `promise` 語法

```js
myContract.methods.myMethod(123).estimateGas({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(gasAmount){
    // ...
})
.catch(function(error){
    // ...
});
```

#### methods.myMethod.encodeABI 函示

```js
myContract.methods.myMethod([param1[, param2[, ...]]]).encodeABI()
```

為指定的合約方法進行 ABI 編碼，可用於發送交易、呼叫方法。

**傳入參數**

無

**回傳值**

編碼後 ABI byte 字串，可以用於交易發送或方法呼叫。

**範例**

```js
myContract.methods.myMethod(123).encodeABI();
> '0x58cf5f1000000000000000000000000000000000000000000000000000000000000007B'
```