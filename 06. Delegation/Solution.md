We want to call this ``pwn()`` function to get contract ownership, but we don't know contract address.
![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/delegation1.png)
The second contract has a fallback function doing a `delegatecall()` and taking ``msg.data`` as input. We want to trigger this fallback function and include the ``pwn()`` function signature as data.
![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/delegation2.png)
Function signatures is the first four bytes of the Keccak-256 hash :
- JS : `web3.eth.abi.encodeFunctionSignature("pwn()")`
- Solidity : `abi.encodeWithSignature("pwn()")`

Sadly if we use another contract to exploit this vulnerability, the contract will be the `msg.sender` and will then claim ownership.

JS solution : `contract.sendTransaction({data:web3.eth.abi.encodeFunctionSignature("pwn()")})` 