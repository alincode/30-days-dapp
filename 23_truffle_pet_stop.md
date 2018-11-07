# Truffle - 使用寵物商店樣版快速開發 DApp (1/2)

### Step 1：建立專案基礎建設

```sh
# 建立一個資料夾
mkdir truffle-pet-shop-sandbox

# 使用寵物商店樣版
truffle unbox pet-shop
```

### Step 2：安裝 DApp 相依模組並啟動 DApp

```sh
# 安裝必要套件
npm install

# 執行 DApp
npm run dev
```

![](assets/truffle/pet_store_dapp.png)


### Step3：新增一個領養的智能合約

```js
pragma solidity ^0.4.17;

contract Adoption {

  mapping(uint8 => address) public adopterMap;
  
  modifier vaildPetId(uint8 petId) {
      require(petId >= 0 && petId < 16);
      _;
  }
  
  // 領養
  function adopt(uint8 petId) public returns (uint8) {
    adopterMap[petId] = msg.sender;
    return petId;
  }

  // 取得領養人的位址
  function getAdopters() public view returns (address[]) {
    address[] storage adopters;
    for(uint8 i = 0; i < 16; i++) {
        adopters.push(adopterMap[i]);
    }
    return adopters;
  }
}
```

### Step4：新增一個發佈智能合約的 script

```js
// 這裡的名稱要對應到智能合約
var Adoption = artifacts.require("Adoption");

module.exports = function (deployer) {
  deployer.deploy(Adoption);
};
```

### Step5：開啟測試鏈

開啟 Ganache

![](assets/truffle/ganache_accounts.png)

### Step6：發布智能合約

```sh
# 編譯
truffle compile

# 發佈
truffle migrate
```

![](assets/truffle/pet_store_migrate.png)

![](assets/truffle/ganache_transactions.png)

今天先做到這，明日待續。