---
description: 合約測試
---

# 合約測試 Unit Test

編譯好合約後

```
npx hardhat compile
```

可以寫一個測試來測試合約功能，並執行以下指令來跑測試

```
npx hardhat test
```

## 範例

```javascript
import {
  loadFixture,
} from "@nomicfoundation/hardhat-toolbox/network-helpers";
import { expect } from "chai";
import { ethers } from "hardhat";

describe("contract", function () {
  // We define a fixture to reuse the same setup in every test.
  // We use loadFixture to run this setup once, snapshot that state,
  // and reset Hardhat Network to that snapshot in every test.
  async function deployFixture() {
    const TestContract = await ethers.getContractFactory("TestContract");
    const testContract = await TestContract.deploy();

    return { testContract };
  }
  
  it("should ....", async function () {
      const { testContract } = await loadFixture(deployFixture);
      // do something
      const result = await testContract.add(1, 2);
      expect(result).to.equal(3);
  }
}
```

##
