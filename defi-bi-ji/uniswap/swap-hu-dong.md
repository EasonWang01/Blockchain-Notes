# Swap 互動

以下使用 hardhat test case 撰寫，並在模擬環境下配置 fork 主網特定區塊

> 當 swap 時有提供 ETH 都會被自動轉為 WETH

```javascript
const { ethers } = require("hardhat");
const { expect } = require("chai");

describe("USDC Balance Check", function () {
  it("Should return the USDC balance of the address", async function () {
    const usdcAddress = "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"; // USDC Contract Address
    const addressToCheck = "0x7713974908Be4BEd47172370115e8b1219F4A5f0"; // Address whose balance you want to check

    // USDC Contract ABI (Simplified; use the full ABI from Etherscan)
    const usdcAbi = [
      "function balanceOf(address owner) view returns (uint256)",
    ];

    // Connect to the USDC contract
    const usdcContract = new ethers.Contract(
      usdcAddress,
      usdcAbi,
      ethers.provider
    );

    // Fetch the balance
    const balance = await usdcContract.balanceOf(addressToCheck);

    // Log the balance (optional)
    console.log("USDC Balance:", balance.toString());

    const mainnetAccount = addressToCheck; // Replace with a mainnet account address
    await hre.network.provider.request({
      method: "hardhat_impersonateAccount",
      params: [mainnetAccount],
    });

    // Set the balance of the impersonated account
    await hre.network.provider.send("hardhat_setBalance", [
      mainnetAccount,
      "0x8AC7230489E80000000", // 40960 ETH
    ]);

    const ethBalance = await ethers.provider.getBalance(addressToCheck);

    // Log the balance (optional)
    console.log("ETH Balance:", ethers.formatEther(ethBalance), "ETH");

    const swapRouterAddress = "0xe592427a0aece92de3edee1f18e0157c05861564"; // Uniswap V3 SwapRouter
    const WETH = "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2"; // Wrapped ETH address on Ethereum Mainnet
    const USDC = "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"; // USDC address

    // SwapRouter ABI (Simplified, only including the required function)
    const swapRouterAbi = [
      "function exactInputSingle((address tokenIn, address tokenOut, uint24 fee, address recipient, uint256 deadline, uint256 amountIn, uint256 amountOutMinimum, uint160 sqrtPriceLimitX96)) external payable returns (uint256 amountOut)",
    ];

    // Set up the SwapRouter contract interface
    const swapRouterContract = new ethers.Contract(
      swapRouterAddress,
      swapRouterAbi,
      await ethers.provider.getSigner()
    );

    const swapParams = {
      tokenIn: WETH,
      tokenOut: USDC,
      fee: 3000, // Pool fee, 0.3% for USDC-ETH
      recipient: addressToCheck, // Your account address
      deadline: Math.floor(Date.now() / 1000) + 60 * 20, // Transaction deadline (20 minutes from now)
      amountIn: ethers.parseEther("1"), // Amount of ETH to swap
      amountOutMinimum: 0, // Set to 0 or a minimum amount of USDC you would like to receive
      sqrtPriceLimitX96: 0, // No price limit
    };

    // Perform the swap - sending ETH along with the transaction
    const tx = await swapRouterContract.exactInputSingle(swapParams, {
      value: swapParams.amountIn,
    });
    await tx.wait();

    console.log(`Swap executed, transaction hash: ${tx.hash}`);

    const USDCBalanceNew = await usdcContract.balanceOf(addressToCheck);
    console.log("USDC Balance:", USDCBalanceNew.toString());
  });
});

```
