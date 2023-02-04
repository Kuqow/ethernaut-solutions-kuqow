# Solution to challenge 19 : Alien Codex

Our goal here is to claim ownership of this "Alien contract".

1.  First we can see that this contract has a "contracted", the variable `bool public contact` must be turned to true in order for us to use the contract functions. To do so, we just need to call `make_contract()` function.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/aliencodex1.png)

2. To claim Ownership we need to change the `owner` variable, this variable doesn't appear in the contract because it is being imported from `import '../helpers/Ownable-05.sol';`, we can look at the details of this Ownable contract on OpenZeppelin's Git repository here : [https://github.com/OpenZeppelin/ethernaut/blob/master/contracts/contracts/helpers/Ownable-05.sol](https://github.com/OpenZeppelin/ethernaut/blob/master/contracts/contracts/helpers/Ownable-05.sol)

Now we have to remember how storage works : 
-  Multiple items that need less than 32 bytes are packed into a single storage slot if possible (https://docs.soliditylang.org/en/v0.4.24/miscellaneous.html)
-  For dynamic arrays, if it starts at a slot location ``p``, then the slot ``p`` will contain the total number of elements stored in the array, and the actual array data will be stored at ``keccack256(p)``. Explained here : [https://docs.soliditylang.org/en/v0.8.13/internals/layout_in_storage.html#mappings-and-dynamic-arrays](https://docs.soliditylang.org/en/v0.8.13/internals/layout_in_storage.html#mappings-and-dynamic-arrays)

Let's sum up the organization of our contract's storage :

```
SLOT (32 bytes)       VARIABLES                                                
----------------------------------------------------------------------------------------------

0                     ``bool public contact`` (1 byte)
                      and ``address private _owner`` (20 bytes)
                      They are packed into a single storage slot because less than 32 bytes

1                     Length of the dynamic array `bytes32[] public codex`

...      

keccak256(1)          codex[0] - First element

keccak256(2)          codex[1] - Second element

...

2**256 -1             codex[2**256 - 1 - keccak256(1)] - Last storage slot

0                     codex[2**256 - 1 - keccak256(1) + 1]
	                  Overflow since EVM storage size is exactly ``2**256-1`` slots of 32                
	                  bytes
```

The function ``retract()`` is not checking any int underflow, as a result if we call it, we change codex length from ``0`` to ``2**256 - 1``. EVM storage size is exactly ``2**256-1`` slots of 32 bytes. Thus, we gain the ability to modify any slot of contract storage.

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/aliencodex2.png)

3. Finally in order to complete the exploit we need to `revise()` codex at index `2**256 - 1 - keccak256(1) + 1` with our player address :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/aliencodex3.png)
