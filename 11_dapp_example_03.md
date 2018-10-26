# 實戰練習：簡化版 King of the Ether (3/3)

有使用到的語法

* myContract.events.NoticeNewKing
* web3.eth.defaultAccount
* web3.utils.fromWei
* web3.eth.net.getId
* web3.eth.getAccounts

### Step5: 建立 DOM 元素

```js
const resultElement = html `<div></div>`
const inputName = html `<input class=${css.input} type="text" value="" placeholder="input your name"/>`;
const inputAmount = html `<input class=${css.input} type="number" value="" placeholder="input ether amount"/>`;
```

### Step6: 監聽智能合約事件

```js
// Generate filter options
const options = {
  // filter: {
  //   _from: process.env.WALLET_FROM,
  //   _to: process.env.WALLET_TO,
  //   _value: process.env.AMOUNT
  // },
  fromBlock: 'latest'
}

myContract.events.NoticeNewKing(options, async (error, event) => {
  if (error) {
    console.log(error)
    return
  }
  console.log('event:', event);
  const ether = web3.utils.fromWei(event.returnValues.amount, "ether");
  const newElement = html `<div class="${css.result}">The king's name is ${event.returnValues.name}, amount is ${ether} Ether</div>`
  morphdom(resultElement, newElement);
  return
})
```

### Step7: 實作 DOM 事件

```js
function replaceKing(event) {
  let account = web3.eth.defaultAccount;
  console.log('account: ', account);
  myContract.methods.replaceKing(inputName.value).send({
    from: account,
    value: web3.utils.toWei(inputAmount.value, "ether")
  }, (err, data) => {
    if (err) return console.error(err);
    console.log('>>> replaceKing ok.');
  });
}

function playerWithdrawal(event) {
  let account = web3.eth.defaultAccount;
  myContract.methods.playerWithdrawal().send({
    from: account
  }, (err, data) => {
    if (err) return console.error(err);
    console.log('>>> playerWithdrawal ok.');
  });
}
```

### Step8: 預載資料

```js
function start() {
  console.log('=== start ===');
  getNetworkId({});
}

function getNetworkId(result) {
  console.log('>>> 1');
  web3.eth.net.getId(function (err, networkId) {
    result.networkId = networkId;
    getAccounts(result);
  });
}

function getAccounts(result) {
  console.log('>>> 2');
  web3.eth.getAccounts(function (err, addresses) {
    const address = addresses[0];
    web3.eth.defaultAccount = address;
    result.account = address;
    getKingInfo(result);
  });
}

function getKingInfo(result) {
  console.log('>>> 3');
  myContract.methods.kingInfo().call((err, data) => {
    if (err) return console.error(err);
    if (data.amount != "0") {
      const ether = web3.utils.fromWei(data.amount, "ether");
      const newElement = html `<div class="${css.result}">The king's name is ${data.name}, amount is ${ether} Ether</div>`
      morphdom(resultElement, newElement);
    }
    render(result);
  })
}
```

### Step9: render html

```js
function render(result) {
  console.log('>>> result:', result);
  document.body.appendChild(html `
  <div class=${css.box} id="app">
    <h2>King of the Ether - ${getNetworkName(result.networkId)} Network</h2>
    <div>If you put more money here, you could be a King, too.</div>
    Name： ${inputName}<br>
    Amount： ${inputAmount}<br>
    <button class=${css.button} onclick=${replaceKing}>replace king</button>
    <button class=${css.button} onclick=${playerWithdrawal}>withdrawal</button>
    ${resultElement}
  </div>
 `)
}

if (typeof web3 !== 'undefined') start();
```

原始碼：<https://github.com/alincode/king-sandbox>