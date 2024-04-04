# Hardhat 寫測試

## 獲取地址餘額

```javascript
const ownerAddress = await owner.getAddress();
const initialOwnerBalance = await ethers.provider.getBalance(ownerAddress);
```

## 獲取 function gas 與 gas price

現在的 etherjs v6 版本使用 js 原生 BIgInt 即可。

```
// 呼叫 function 後獲取 receipt，即可獲取到 gasUser, gasPrice
const stakeTx = await staker.connect(owner).stake();
const stakeReceipt = await stakeTx.wait();
const stakeGasUsed = BigInt(stakeReceipt.gasUsed) * BigInt(stakeReceipt.gasPrice);
```

```typescript
it("Should unstake correctly after the unbonding period", async function () {
    const ownerAddress = await owner.getAddress();
    const initialOwnerBalance = await ethers.provider.getBalance(ownerAddress);

    // Stake
    const stakeTx = await staker.connect(owner).stake({ value: ethers.parseEther("1") });
    const stakeReceipt = await stakeTx.wait();

    const stakeGasUsed = BigInt(stakeReceipt.gasUsed) * BigInt(stakeReceipt.gasPrice);
    const afterStakeBalance = BigInt(initialOwnerBalance) - BigInt(ethers.parseEther("1")) - BigInt(stakeGasUsed);

    expect(await ethers.provider.getBalance(ownerAddress)).to.equal(afterStakeBalance);
  });
```

## 時間機器

可設置當前時間

```typescript
// Fast forward time by 7 days for testing
await ethers.provider.send("evm_increaseTime", [7 * 24 * 60 * 60]);
await ethers.provider.send("evm_mine");
```

## Event 參數

```typescript
await expect(<Contract instance>.connect(addr1).cancelUnstake(tokenId)).to.emit(<Contract instance>, <Event name>)
.withArgs(addr1.address, tokenId, 1);
```
