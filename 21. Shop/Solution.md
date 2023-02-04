# Solution to challenge 21 : Shop

Our goal here is to change the `bool public isSold` to true while having a `uint public price < 100 

We can see that during the buying process, the interface function `price()` is called  twice :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/shop1.png)

We can't manipulate data using contract variables like we did with **Challenge 11** because the ``view`` attributes prevents us from changing the state.

**BUT** there is a trick, looking at the contract's code we see that its `isSold` variable is being changed just before the second call, meaning that we can change our `return (price)` value according to it.

1. If `isSold == false` : we first return a price higher than 100 to pass the `if()`
2. If `isSold == true` : we return a price lower than 100 in order to succeed the challenge

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/shop2.png)