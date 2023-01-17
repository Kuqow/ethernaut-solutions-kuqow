Our goal is to retrieve 0.001 eth from a lost contract. To do that we need to :

1.  Find the contract address, from our generator contract address and using etherscan https://goerli.etherscan.io/ we can find the **ContractCreation** call :

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/recovery1.png)

2. Then, looking at the contract code, we notice that there is a `destroy()` function callable from anyone

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/recovery2.png)

3. We call it and it's done

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/recovery3.png)
