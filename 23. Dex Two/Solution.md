# Solution to challenge 23 : DEX Two

Our goal here is to drain both tokens from the contract. 

If we take a look at the `swap()` function as the title tells us, we notice that the `require((from == token1 && to == token2) || (from == token2 && to == token1), "Invalid tokens");` condition is gone. From here it's pretty straightforward : we are going to use "fake" tokens.

Here are the steps to achieve this exploit : 

1. Create 2 fake tokens  `fake1` et `fake2` (for example) using the ERC20 solidity code from the challenge with at least 100 of supply. Your balance for this token will be equal to initial supply since you are the creator of this contract.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/DEXtwo1.png)

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/DEXtwo2.png)

2. Approve spending of both tokens for at least 100 tokens, using DEX address as the `spender`

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/DEXtwo3.png)

3. Then we need to send some tokens to the DEX address, why ? Because the `getSwapAmount()` uses `IERC20(from).balanceOf(address(this))` for the calculation. For ease of use, we will send 100 tokens, as a result, `IERC20(to).balanceOf(address(this)))/IERC20(from).balanceOf(address(this))` will be equal to **1**.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/DEXtwo4.png)

4. Now, we swap 100 `fake1` for 100 `token1` : `contract.swap("fakeTOKEN1_address","realTOKEN1_address",100)`

5. Now we can check DEX's balance using `contract.balanceOf("realTOKEN1_address",instance)`, and we see that its balance is equal to zero !

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/DEXtwo5.png)

6. **Repeat for ``token2`` and **submit your instance** ! 