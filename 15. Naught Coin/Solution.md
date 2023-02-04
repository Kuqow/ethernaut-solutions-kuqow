# Solution to challenge 15 : Naught Coin

[https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)

We want to transfer funds from our address, but we can't use the `transfer()` function because it is locked by the modifier `lockTokens` :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/naughtcoin.png)

BUT, this contract inherits from **ERC20.sol** functions, and there is another function available to transfer funds which is not covered by the modifier : `transferFrom(from,to,amount)` 

This function first requires an approval to spend an amount of tokens, then we will call :

1. ``contract.approve(player, "1000000000000000000000000")``
2. ``contract.transferFrom(player,instance,"1000000000000000000000000")``

We could also use a contract but it is not necessary.