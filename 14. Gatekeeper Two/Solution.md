# Solution to challenge 14 : Gatekeeper two

[https://docs.soliditylang.org/en/v0.4.23/assembly.html](https://docs.soliditylang.org/en/v0.4.23/assembly.html)

[https://ethereum.stackexchange.com/questions/45095/how-could-msg-sender-tx-origin-and-extcodesizecaller-0-be-true](https://ethereum.stackexchange.com/questions/45095/how-could-msg-sender-tx-origin-and-extcodesizecaller-0-be-true)

To register as an entrant and succeed, we have to bypass 3 gates (again) :

### Gate 1

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/gatekeepertwo1.png)

Same as previous challenge, call the function from a contract.

### Gate 2

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/gatekeepertwo2.png)

Here we have one assembly code call : ``extcodesize(x)`` returns the size of the code at address x. In our case, we want to have the code size of our contract equals to zero, how is it possible ? We need to call it within the **constructor** ! 

At this point, it will have a (theoretical) address but no code yet in the chain. It will only get code size after the transaction completes and the block is accepted.

### Gate 3

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/gatekeepertwo3.png)

To pass this gate we need to calculate a precise `_gateKey` using XOR operations :

`(uint64(bytes8(keccak256(abi.encodePacked(msg.sender))))` simply equals to ``msg.sender``

So we have :

``msg.sender ^ _gateKey == type(uint64).max``

And the key equals to :

`_gateKey == msg.sender ^ type(uint64).max`

We wrap all the calculations and call in the constructor to pass the challenge.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/gatekeepertwo4.png)
