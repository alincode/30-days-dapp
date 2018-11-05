# 介紹 web3.eth.Contract 事件

**什麼時候會需要用到事件？**

智能合約需要盡量的保持簡單，因為每一個計算跟儲存都是有價的，很多操作其實並不適合直接在區塊鏈上執行，所以我們常在智能合約丟出事件來通知前端做一些後置處理，例如：更新頁面、顯示通知訊息，讓前端網頁跟智能合約做互動。

也可以當作一個額外的儲存空間，與儲存在狀態變數相比，存在事件的 log 中相對便宜，但要注意的是寫在事件的 log，智能合約無法直接讀取，但 `web3.js` 可以讀的到，所以適合存放僅前端需要讀取的資料。

### once 函示

訂閱事件，並且只執行一次。

**語法**

```js
myContract.once(event[, options], callback)
```

**傳入參數**

1. `event` 字串：智能合約裡面的事件名稱，或使用 `allEvents` 訂閱所有事件。
2. `options` 物件 (可選)
  * `filter` 物件 (可選)：允許你透過 indexed 參數，過濾訂閱的事件，例如 `{filter: {myNumber: [12,13]}}` 意思是有參數值是 12 或 13 的事件。
  * `topics` 陣列 (可選)：允許你手動過濾事件主題
3. `callback` 函示：第一個參數是 error 物件，第二個參數是 event 物件。有關 event 物件結構，你可以看 `getPastEvents` 的回傳值。

**回傳值**

無

**範例**

```js
myContract.once('MyEvent', {
    filter: { myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'}, 
    fromBlock: 0
}, function(error, event){ console.log(event); });
```

輸出值範例

```
> {
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
}
```

### events

訂閱一個特定的事件

**語法**

```js
myContract.events.MyEvent([options][, callback])
```

**傳入參數**

1. `options` 物件 (可選)
  * `filter` 物件 (可選)：允許你透過 indexed 參數，過濾訂閱的事件，例如 `{filter: {myNumber: [12,13]}}` 意思是有參數值是 12 或 13 的事件。
  * `fromBlock`數字 (可選)：指定特定區塊編號的事件
  * `topics` 陣列 (可選)：允許你手動過濾事件主題
2. `callback` 函示 (可選)：第一個參數是 error 物件，第二個參數是 event 物件。

**回傳值**

`EventEmitter` 有以下事件：

* `data`
* `changed`
* `error`

返回 event 物件的結構，如下：

* `event` 字串：事件名稱
* `signature` 字串，如果是匿名事件，值會是 `null`。
* `address` 字串
* `returnValues` 物件: 值來自於智能合約的事件傳入值，例如 `{myVar: 1, myVar2: '0x234...'}`。
* `logIndex` 數字：事件索引的編號
* `transactionIndex` 數字
* `transactionHash` 32 Bytes 的字串：transaction 的 hash
* `blockHash` 32 Bytes 的字串：區塊的 hash，如果狀態是 pending 的話，值會是 `null`。
* `blockNumber` 數字：這個 log 建立時的區塊編號，如果狀態是 pending 的話，值會是 `null`。
* `raw.data` 字串：資料包含為索引的 log 參數
* `raw.topics` 陣列：最多可保存 4 個 32 Bytes 的主題

**範例**

```js
myContract.once('MyEvent', {
    filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'},
    fromBlock: 0
}, function(error, event){ console.log(event); });
```

輸出值範例

```
> {
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
}

```
#### events.allEvents 函示

與 `events` 使用方式相同，但會接收來至於智能合約的所有事件，可以使用 `options` 來過濾要監聽的事件。

**語法**

```js
myContract.events.allEvents([options][, callback])
```

#### getPastEvents 函示

取得智能合約過去的事件

**語法**

```js
myContract.getPastEvents(event[, options][, callback])
```

**傳入參數**

1. `event` 字串：智能合約裡面的事件名稱，或使用 `allEvents` 訂閱所有事件。
2. `options` 物件 (可選)
  * `filter` 物件 (可選)：允許你透過 indexed 參數，過濾訂閱的事件，例如 `{filter: {myNumber: [12,13]}}` 意思是有參數值是 12 或 13 的事件。
  * `fromBlock`數字 (可選)：指定特定區塊編號的事件
  * `toBlock` - Number (optional): The block number to get events up to (Defaults to "latest").
  * `topics` 陣列 (可選)：允許你手動過濾事件主題
3. `callback` 函示 (可選)：第一個參數是 error 物件，第二個參數是 event 陣列物件。

**回傳值**

返回一個 Promise 的 event 陣列物件

**範例**

```js
myContract.getPastEvents('MyEvent', {
    filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'},
    fromBlock: 0,
    toBlock: 'latest'
}, function(error, events){ console.log(events); })
.then(function(events){
    console.log(events);
});
```

輸出值範例：`events` 結構

```
> [{
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
},{
    ...
}]
```