# 使用寵物商店樣版快速開發 DApp (3/3)

#### Step 1: 實作初始化 web3

src/js/app.js

```js
initWeb3: async function () {
  App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
  web3 = new Web3(App.web3Provider);
  return App.initContract();
},
```

#### Step 2：實例化智能合約

```js
initContract: function () {

  $.getJSON('Adoption.json', function (data) {
    // data 是符合 truffle contractschema 格式的 JSON 檔案
    var AdoptionArtifact = data;
    App.contracts.Adoption = TruffleContract(AdoptionArtifact);
    // 設定合約的 provider
    App.contracts.Adoption.setProvider(App.web3Provider);
    // 執行 App.markAdopted() 函示
    return App.markAdopted();
  });

  return App.bindEvents();
},
```
> truffle-contract-schema 文件：<https://github.com/trufflesuite/truffle/tree/next/packages/truffle-contract-schema>
> truffle-contract 文件：<https://github.com/trufflesuite/truffle/tree/next/packages/truffle-contract>

#### Step 3：實作 markAdopted 函示

```js
markAdopted: function (adopters, account) {
  var adoptionInstance;
  App.contracts.Adoption.deployed().then(function (instance) {
    adoptionInstance = instance;
    // 因為 getAdopters 是具有 view 修飾符的函示，所以不用建立一筆交易 (transation)，可以直接用 call 函示來取得回傳值。
    return adoptionInstance.getAdopters.call();
  }).then(function (adopters) {
    adopters.forEach(function(adopter, index) {
      if (adopters !== '0x0000000000000000000000000000000000000000') {
        $('.panel-pet').eq(index).find('button').text('Success').attr('disabled', true);
      }
    });
  }).catch(function (err) {
    console.log(err.message);
  });
},
```

#### Step 4： 實作 handleAdopt 事件

```js
handleAdopt: function (event) {
  event.preventDefault();
  var petId = parseInt($(event.target).data('id'));
  var adoptionInstance;

  web3.eth.getAccounts(function (error, accounts) {
    if (error) {
      console.log(error);
    }
    var account = accounts[0];
    App.contracts.Adoption.deployed().then(function (instance) {
      adoptionInstance = instance;
      // 建立一筆交易，執行智能合約的 adopt 函式
      return adoptionInstance.adopt(petId, {
        from: account
      });
    }).then(function (result) {
      // console.log(result);
      return App.markAdopted();
    }).catch(function (err) {
      console.log(err.message);
    });
  });
}
```

#### Step 5：完整 src/js/app.js

```js
App = {
  web3Provider: null,
  contracts: {},

  init: async function () {
    // 從 json 載入寵物基本資訊，並產生 html。
    $.getJSON('../pets.json', function (data) {
      var petsRow = $('#petsRow');
      var petTemplate = $('#petTemplate');

      for (i = 0; i < data.length; i++) {
        petTemplate.find('.panel-title').text(data[i].name);
        petTemplate.find('img').attr('src', data[i].picture);
        petTemplate.find('.pet-breed').text(data[i].breed);
        petTemplate.find('.pet-age').text(data[i].age);
        petTemplate.find('.pet-location').text(data[i].location);
        petTemplate.find('.btn-adopt').attr('data-id', data[i].id);

        petsRow.append(petTemplate.html());
      }
    });

    return await App.initWeb3();
  },

  // Step 1：實作初始化 web3
  initWeb3: async function () {
    App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
    web3 = new Web3(App.web3Provider);
    return App.initContract();
  },

  // Step 2：實例化智能合約
  initContract: function () {

    $.getJSON('Adoption.json', function (data) {
      // data 是符合 truffle contractschema 格式的 JSON 檔案
      var AdoptionArtifact = data;
      App.contracts.Adoption = TruffleContract(AdoptionArtifact);
      // 設定合約的 provider
      App.contracts.Adoption.setProvider(App.web3Provider);
      // 執行 App.markAdopted() 函示
      return App.markAdopted();
    });

    return App.bindEvents();
  },

  bindEvents: function () {
    $(document).on('click', '.btn-adopt', App.handleAdopt);
  },

  // 標記已經被領養的寵物
  markAdopted: function () { // Step 3
    var adoptionInstance;
    App.contracts.Adoption.deployed().then(function (instance) {
      adoptionInstance = instance;
      // 因為 getAdopters 是具有 view 修飾符的函示，所以不用建立一筆交易 (transation)，可以直接用 call 函示來取得回傳值。
      return adoptionInstance.getAdopters.call();
    }).then(function (adopters) {
      adopters.forEach(function (adopter, index) {
        // 如果已經有人領養了，則停用按鈕。
        if (adopter !== '0x0000000000000000000000000000000000000000') {
          $('.panel-pet').eq(index).find('button').text('Success').attr('disabled', true);
        }
      });
    }).catch(function (err) {
      console.log(err.message);
    });
  },

  handleAdopt: function (event) { // Step 4
    event.preventDefault();
    var petId = parseInt($(event.target).data('id'));
    var adoptionInstance;

    web3.eth.getAccounts(function (error, accounts) {
      if (error) {
        console.log(error);
      }
      var account = accounts[0];
      App.contracts.Adoption.deployed().then(function (instance) {
        adoptionInstance = instance;
        // 建立一筆交易，執行智能合約的 adopt 函式
        return adoptionInstance.adopt(petId, {
          from: account
        });
      }).then(function (result) {
        // console.log(result);
        return App.markAdopted();
      }).catch(function (err) {
        console.log(err.message);
      });
    });
  }

};

$(function () {
  $(window).load(function () {
    App.init();
  });
});
```

#### Step 6：啟動寵物商店 DApp

```sh
npm run dev
```

> 完整原始碼：<https://github.com/alincode/truffle-pet-shop-sandbox>