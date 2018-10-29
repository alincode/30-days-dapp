# 常用網路與工具函示

### web3.eth.net.getId

取得目前網路的 ID

**語法**

```
web3.eth.net.getId([callback])
web3.bzz.net.getId([callback])
web3.shh.net.getId([callback])
```

**傳入參數**

無

**回傳值**

一個數字，代表網路 ID

**範例**

```js
await web3.eth.net.getId();
```

### web3.utils.randomHex

**語法**

```
web3.utils.randomHex(size)
```

**傳入參數**

1. `size` 數字：指定亂數的 bytes 大小

**回傳值**

字串：隨機產生的亂數

**範例**

```js
web3.utils.randomHex(32)
> "0xa5b9d60f32436310afebcfda832817a68921beb782fabf7915cc0460b443116a"

web3.utils.randomHex(4)
> "0x6892ffc6"

web3.utils.randomHex(2)
> "0x99d6"

web3.utils.randomHex(1)
> "0x9a"

web3.utils.randomHex(0)
> "0x"
```

### web3.utils._

* underscore 函式庫：<https://underscorejs.org/>
* 類似 `lodash` 一些常用的函示。

**範例**

```js
var _ = web3.utils._;

_.union([1,2],[3]);
> [1,2,3]

_.each({my: 'object'}, function(value, key){ ... })

```

### web3.utils.isAddress

檢查是否真的位址

**語法**

```
web3.utils.isAddress(address)
```

**傳入參數**

1. `address` 字串

**回傳值**

一個 boolean

**範例**

```js
web3.utils.isAddress('0xc1912fee45d61c87cc5ea59dae31190fffff232d');
> true

web3.utils.isAddress('c1912fee45d61c87cc5ea59dae31190fffff232d');
> true

web3.utils.isAddress('0XC1912FEE45D61C87CC5EA59DAE31190FFFFF232D');
> true

web3.utils.isAddress('0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
> true

web3.utils.isAddress('0xC1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
> false
```

### web3.utils.fromWei

從 `wei` 轉為指定單位

**語法**

```
web3.utils.fromWei(number [, unit])
```

**傳入參數**

1. `number`：接受格式字串、數字、BN，單位是 `wei`。
2. `uint`：接受格式字串 (非必填)，預設是 `ether`。

```
noether: ‘0’
wei: ‘1’
kwei: ‘1000’
Kwei: ‘1000’
babbage: ‘1000’
femtoether: ‘1000’
mwei: ‘1000000’
Mwei: ‘1000000’
lovelace: ‘1000000’
picoether: ‘1000000’
gwei: ‘1000000000’
Gwei: ‘1000000000’
shannon: ‘1000000000’
nanoether: ‘1000000000’
nano: ‘1000000000’
szabo: ‘1000000000000’
microether: ‘1000000000000’
micro: ‘1000000000000’
finney: ‘1000000000000000’
milliether: ‘1000000000000000’
milli: ‘1000000000000000’
ether: ‘1000000000000000000’
kether: ‘1000000000000000000000’
grand: ‘1000000000000000000000’
mether: ‘1000000000000000000000000’
gether: ‘1000000000000000000000000000’
tether: ‘1000000000000000000000000000000’
```

**回傳值**

一個字串或 `BN`

**範例**

```js
// 1 wei 轉為 ether 單位
web3.utils.fromWei('1', 'ether');
> "0.000000000000000001"

web3.utils.fromWei('1', 'finney');
> "0.000000000000001"

web3.utils.fromWei('1', 'szabo');
> "0.000000000001"

web3.utils.fromWei('1', 'shannon');
> "0.000000001"
```

### web3.utils.toWei

轉為 `wei` 單位

**語法**

```
web3.utils.toWei(number [, unit])
```

**傳入參數**

1. `number`：接受格式字串、數字、BN，單位是 `wei`。
2. `uint`：接受格式字串 (非必填)，預設是 `ether`。

**回傳值**

一個字串或 `BN`

**範例**

```js
// 1 ether 轉為 wei 單位
web3.utils.toWei('1', 'ether');
> "1000000000000000000"

web3.utils.toWei('1', 'finney');
> "1000000000000000"

web3.utils.toWei('1', 'szabo');
> "1000000000000"

web3.utils.toWei('1', 'shannon');
> "1000000000"
```

> Big Number (BN)：<http://mikemcl.github.io/bignumber.js/>