Whoever sends it an amount of ether that is larger than the current prize becomes the new king. On such an event, the overthrown king gets paid the new prize. 

We want to prevent the owner from reclaiming the kingship, we will make his **transaction fail** when he will try to `king.transfer(msg.value);`

We will then a contract able to :
- ``Send funds`` to our target
- ``Reject incoming transfers`` to make the transfer fail : to do so, we add a minimum value to our ``receive()`` function :

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/king1.png)

The main contract will then **fail to reclaim the kingship** when doing this :

![](https://github.com/xWhiteOuroboros/ethernaut-solutions-xwhiteouroboros/blob/main/Pictures/king2.png)
