# Solution to challenge 20 : Denial

Our goal here is to prevent the owner from withdrawing funds when they call the  `withdraw()` function

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/denial1.png)

1. Within this function we notice that there is a `call()` just before the transfer to the owner, this `call()` is not checking return and is not using any limited amount of gas. 

2. Thus we can create an **infinite loop** that will drain all the gas and prevent the contract from continuing its execution, **blocking the transfer to the owner** :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/denial2.png)
