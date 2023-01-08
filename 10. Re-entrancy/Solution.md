[https://docs.soliditylang.org/en/v0.8.17/security-considerations.html#re-entrancy](https://docs.soliditylang.org/en/v0.8.17/security-considerations.html#re-entrancy)

The withdrawal call is done before removing the amount from the balances mapping. There are no reentrancy protections.

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/reentrancy1.png)

Basically we want to :
- Send funds to be registered as a sender and to be authorized to withdraw

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/reentrancy2.png)

- Withdraw an amount 

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/reentrancy3.png)

- The funds received will trigger the receive functon of our contract, we will then ask to withdraw funds, again and again until we extracted everything

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/reentrancy4.png)

The result :

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/reentrancy5.png)
