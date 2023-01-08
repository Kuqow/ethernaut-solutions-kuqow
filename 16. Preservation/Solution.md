Once again, `delegateCall` is our friend, this function  is tricky to use and wrong usage or incorrect understanding can lead to devastating results.

When using `delegatecall` :

1.  `delegatecall` preserves context (storage, caller, etc...)
2.  Storage layout must be the same for the contract calling `delegatecall` and the contract getting called

In the LibraryContract, `uint storedTime is at STORAGE SLOT 0`
In the Preservation contract, `address timeZone1Library is at STORAGE SLOT 0`

Thus, if we call the delegateCall to change time it will change `timeZone1Library` 

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/preservation1.png)

If we change this value to another owned contract address, and if this contract has a setTime() function, it will be executed. Steps to do :

1. Create a contract with the same storage structure as Preservation :

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/preservation2.png)

3. Create a `setTime(uint)` function, the ``delegateCall`` uses a constant function signature, so the names must match. This function will change owner to the player address : `owner = xxxx`

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/preservation3.png)

2. Call ``setFirstTime`` with our address as value : ``uint(uint160(address(this)))``, this will change `timeZone1Library` to the address given

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/preservation4.png)

3. Call `setFirstTime` again, this time it will call the function that is inside our contract, and we will finally become owner.

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/preservation5.png)
