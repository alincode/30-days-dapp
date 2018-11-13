# 自製 DApp Starter Kit

ABI 裡記載了智能合約的結構，我們可以使用它來寫了一個小模組來自動產生 DApp。

### 設計理念

* 可以自動產生 Web3 的 `myContract.methods.myMethod().send()` 和 `myContract.methods.myMethod().call()`
* 可以自動產生 DApp HTML 樣板

### 使用說明

**Option**

* ABI (必要)
* CONTRACT_ADDRESS (必要)
* WEB3_CDN_URL (可選)

```js
let newOptions = {
  ABI: [],
  CONTRACT_ADDRESS: '0x....',
  WEB3_CDN_URL : 'https://cdn.jsdelivr.net/gh/ethereum/web3.js@1.0/dist/web3.min.js'
};
```

**公開函式**

* function getSourceCode(newOptions)
* function generateHtml(options, filename = 'demo')

#### 範例

執行

```sh
node example.js
```

example.js

```sh
const generator = require('eth-dapp-generator');
const ABI = require('./ABI.json');

const CONTRACT_ADDRESS = '0x829af434f0a8934a431338c1a532abdc8d2c7dfc';

let options = {
  ABI,
  CONTRACT_ADDRESS
};

// generator.getSourceCode(options);
generator.generateHtml(options);
```

ABI.json

```json
[{
    "constant": true,
    "inputs": [],
    "name": "getName",
    "outputs": [{
      "name": "",
      "type": "string"
    }],
    "payable": false,
    "stateMutability": "view",
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "getName2",
    "outputs": [{
      "name": "",
      "type": "string"
    }],
    "payable": false,
    "stateMutability": "view",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [{
      "name": "_name",
      "type": "string"
    }],
    "name": "setName2",
    "outputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [{
      "name": "_name",
      "type": "string"
    }],
    "name": "setName",
    "outputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

#### 自動產生的 `demo.html` 檔

```html

<!DOCTYPE html><html>
  <head>
    <title>Hello World DApp</title>
    <link href='https://fonts.googleapis.com/css?family=Open+Sans:400,700' rel='stylesheet' type='text/css'>
    <link href='https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css' rel='stylesheet' type='text/css'>
  </head>
  <body class="container">

  <h1>Hello World DApp</h1>
    
    <div style ="margin-top:10px;">
      <input type="text" id="setName__name"/> <a href="#" id="setNameClick" class="btn btn-primary">setName</a>
    </div> 
    <div style ="margin-top:10px;">
      <input type="text" id="setName2__name"/> <a href="#" id="setName2Click" class="btn btn-primary">setName2</a>
    </div>
    <div id="result"></div>
    <script src ="https://cdn.jsdelivr.net/gh/ethereum/web3.js@1.0/dist/web3.min.js"></script>
    
    <script>
      async function web3Init() {
        if (ethereum) {
          window.web3 = new Web3(ethereum);
          try {
            const accounts = await ethereum.enable();
            web3.eth.defaultAccount = accounts[0];
          } catch (error) {}
        } else if (web3) {
          window.web3 = new Web3(web3.currentProvider);
        } else {
          console.log('Non-Ethereum browser detected. You should consider trying MetaMask!');
        }
      }

      web3Init();

      const ABI = [{"constant":true,"inputs":[],"name":"getName","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"getName2","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"string"}],"name":"setName2","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"string"}],"name":"setName","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"}];
      
      const CONTRACT_ADDRESS = '0x353df9459c6d62d294530484bdbd6b9b45fd1c98';

      window.myContract = new web3.eth.Contract(ABI, CONTRACT_ADDRESS);

      // ===== DOM Event =====
      
      async function setName(event) {
        let _name = document.getElementById("setName__name").value;
        let data = await myContract.methods.setName(_name).send({
          from: web3.eth.defaultAccount
        }).then(values => {
          render();
        });
      }

      document.getElementById("setNameClick").addEventListener("click", setName);
   
      async function setName2(event) {
        let _name = document.getElementById("setName2__name").value;
        let data = await myContract.methods.setName2(_name).send({
          from: web3.eth.defaultAccount
        }).then(values => {
          render();
        });
      }

      document.getElementById("setName2Click").addEventListener("click", setName2);
  
      // ===== load =====
      
      async function getName() {
        let returnValue = await myContract.methods.getName().call();
        document.getElementById("result").append(returnValue + ",");
      }
   
      async function getName2() {
        let returnValue = await myContract.methods.getName2().call();
        document.getElementById("result").append(returnValue + ",");
      }
  
      function render() {
        
        getName();
        getName2();
       
      }

      window.onload = function () {
        render();
      };
    </script>
  </body>
</html>
```