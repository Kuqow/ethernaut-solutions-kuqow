No verifications are done when doing add/sub tokens operations. We will then abuse integer underflow.

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/token.png)

1. We transfer X tokens to a random address, making an integer underflow and successfully increasing our token balance : `contract.transfer("randAddr",50)`