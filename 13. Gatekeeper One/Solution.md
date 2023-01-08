To register as an entrant and succeed, we have to bypass 3 gates :

### Gate 1

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/gatekeeperone1.png)

This one is easy, we just need to call the function through a contract, thus we will have : `msg.origin = contract addr` and `tx.origin = user addr`. 

**Reminder :** 
- With `msg.sender` the owner can be a contract.
- With `tx.origin` the owner can never be a contract. Externally Owned Accounts (EOA) only.

### Gate 2

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/gatekeeperone2.png)

To pass this gate, we need to send a precise amount of gas : `gasleft() % 8191 == 0`.
Every EVM operand costs gas, but we can't really guess what operations will happen and how much gas will it cost before arriving at this exact operation. 

Thus, we decide to brute force using a `for` loop :

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/gatekeeperone3.png)

We could also send one tx and **debug it** (using Remix IDE) to see how much gas has been spent that point.

In our example we see the following gas used : `24829 gas` to validate the tx

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/gatekeeperone4.png)

### Gate 3 

Reminder : 

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/gatekeeperone5.png)

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/gatekeeperone6.png)

For this one, we need to find the perfect `bytes8 _gateKey` to send.

**8 bytes = 64 bits = uint64 so we have :**

1. uint32(\_gateKey) =  uint16(\_gateKey) 
2. uint32(\_gateKey) != \_gateKey
3. uint32(\_gateKey) = uint16(uint160(tx.origin))
*casting from uint64 to uint160, we don't lose any information so it doesn't matter*

**Let's represent our key (8 bytes) with B1, B2, B3... :**

1. x x x x B5 B6 B7 B8 = x x x x x x B7 B8 
**the 5th and 6th bytes must be equal to 0**

2. x x x x B5 B6 B7 B8 != B1 B2 B3 B4 B5 B6 B7 B8 
**the first 4 bytes must be different from 0**

3. x x x x B5 B6 B7 B8 = x x x x x x TXog TXog 
**the last 2 bytes must belong to the tx.origin**

To calculate our key, let's apply a mask on our **tx.origin** address :
`bytes8 exploitKEY = bytes8(uint64(uint160(tx.origin)) & 0xFFFFFFFF0000FFFF);`

**Extra :**
For whatever reason, using `target.enter{gas:X}(key)` ended on a 'gas etimation error'. Using the `target.call{gas:X}()` was working fine.
