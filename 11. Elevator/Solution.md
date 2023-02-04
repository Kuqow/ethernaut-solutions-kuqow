# Solution to challenge 11 : Elevator

We will play with interfaces, we notice **Elevator.sol** never implemented the `isLastFloor()` function from the Building interface :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/elevator1.png)

Thus, we can create a malicious contract **Building** that implements this function. Then, if we invoke goTo() from the malicious contract, our version of the `isLastFloor` function will be used in the context of our level’s Elevator.sol instance !

We want our function to :
- Return ``false`` first, to enter in the `if{}`
- Return `true` after, to change top to `true` and validate the level

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/elevator2.png)
