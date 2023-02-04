# Solution to challenge 18 : MagicNumber

This one is pretty tricky if you are not used to assembly code and can be achieved in at least **two different ways**.

We want to create a smart contract with a function `whatIsTheMeaningOfLife()` that returns the number 42. This contract code must be very small and contain **42 opcodes at least**, so we have to use assembly code to build it.

List of opcodes : https://www.ethervm.io/
EVM opcodes simulation : https://www.evm.codes/playground

**EVM Assembly notes :**
- Stack size is 256 bits / 32 bytes
- Deploying an ethereum smart contract involves sending a data payload to the **0x0** address
- Runtime bytecode : This is the code running after the contract creation, it contains the logic of the contract.

**List of instructions needed to return 42 :**

```
OPCODE   MNEMONIC      DETAILS                                                SIZE
----------------------------------------------------------------------------------------
60|2a    PUSH1 0x2a    Value to store, 0x2a equals to 42                      2
					   Second parameter of MSTORE(p,value)

60|50    PUSH1 0x50    Position in memory                                     2
					   First parameter of MSTORE(position,value)

52       MSTORE        Store 0x2a at 0x50 in memory                           1

60|20    PUSH1 0x20    Size in bytes, 0x20 equals 32 bytes, our stack size    2
					   Second parameter of RETURN(p,size)

60|50    PUSH1 0x50    Position in memory, where to find the value            2
                       First parameter of RETURN(position,s)

F3       RETURN        Returns value at 0x50 in memory, of size 0x20 bytes    1
```

So, our final runtime opcode is : ``602a60505260206050f3`` with a size of 10 bytes : `0x0a`

Then we can simply use it within our contract using `assembly{}` structure. Since EVM stack size is 32 bytes, there is a prepadding of `32 bytes - 10 bytes (our opcode) = 22 bytes (0x16)`.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/magicnumber.png)

Once deployed, we set the solver's address to our contract's address : `contract.setSolver("0xA")` and we submit the instance.

#### BONUS : Everything from opcode

We could also generate the **initialization opcode** associated to our runtime opcode, and send the final bytecode directly to the **Ethereum null address** in order to generate the contract. 

```
OPCODE   MNEMONIC      DETAILS                                                SIZE
----------------------------------------------------------------------------------------
60|0a    PUSH1 0x0a    Value to store, 0x0a equals to 10                      2
					   Third parameter of CODECOPY(d,p,size)

60|0c    PUSH1 0x50    Position in memory, runtime is at 12nd byte            2
					   Second parameter of CODECOPY(d,position,s)

60|00    PUSH1 0x00    Destination in memory, let's choose 0                  2
					   First parameter of CODECOPY(destination,p,s)

39       CODECOPY      Copy of code of size 0x0a at 0x50 to 0x00 in memory    1

60|0a    PUSH1 0x0a    Size of our runtime opcode, 0x0a equals to 10          2
                       Second parameter of RETURN(p,size)

60|00    PUSH1 0x00    Position in memory, where to find the value            2
                       First parameter of RETURN(position,s)

F3       RETURN        Returns value at 0x00 in memory, of size 0x0a bytes    1
```

**Final initialization opcode** would be : `600a600c600039600a6000f3`
We concatenate both initialization and runtime opcodes : `600a600c600039600a6000f3 + 602a60505260206050f3 = 600a600c600039600a6000f3602a60505260206050f3`

We can now create the contract by sending a transaction to the **0x0 address** and it will be interpreted as **Contract Creation** by the EVM :
`web3.eth.sendTransaction({from: player, data: "600a600c600039600a6000f3602a60505260206050f3"})`