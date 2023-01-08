![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/force.png)

We want to make the balance of the contract greater than 0 but there are no functions to interact with.

- Create a smart contract with a `selfdestruct(addr)` **function**, sending funds to our **target address** when destroyed. We will also need a `receive() external payable` **function** (fallback) to receive the funds.
- **Send funds** to this smart contract
- **Trigger** the selfdestruct

***selfdestruct** is a keyword that is used to terminate a contract, remove the bytecode from the Ethereum blockchain, and send any contract funds to a specified address. The "targeted" contract cannot refuse the funds.*