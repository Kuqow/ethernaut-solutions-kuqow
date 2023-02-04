# Solution to challenge 5 : Token

No verifications are done when doing add/sub tokens operations. We will then abuse integer underflow.

![](https://github.com/kuqow/ethernaut-solutions-kuqow/blob/main/Pictures/token.png)

1. We transfer X tokens to a random address, making an integer underflow and successfully increasing our token balance : `contract.transfer("randAddr",50)`