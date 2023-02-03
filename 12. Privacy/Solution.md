Multiple items that need less than 32 bytes are packed into a single storage slot if possible (https://docs.soliditylang.org/en/v0.4.24/miscellaneous.html)

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy1.png)

*uint8 = 8 bits = 1 byte / uint16 = 16 bits = 2 bytes*

To unlock the contract and win, we need to submit a `bytes16 key` corresponding to the content of `data[2]` :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy2.png)

We will use the same technique as in **8. Vault** with `web3.utils.getStorageAt(addr,pos)` , but we first need to know where to find our key :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy3.png)

After we simply have to use `web3.eth.getStorageAt(instance,5)` :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy4.png)

Then split this **bytes32** in a **bytes16**, we can use remix IDE :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy5.png)

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy6.png)

And finally **unlock** the contract and submit the instance :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/privacy7.png)
