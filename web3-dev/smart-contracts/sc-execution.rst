========================
Smart contract execution
========================

Introduction
============

In Massa you can execute smart contracts not only deploy and call.
There is different possibilities to execute smart contracts in different contexts.
This documentation will explain you the different possibilities and how to use them.
You can choose the one that match the most your use-case.

All of this way to execute smart contracts are available 
in the smart contract sdk `massa-web3 <https://github.com/massalabs/massa-web3>`_.

Different ways to execute smart contracts
=========================================

Operation execute smart contract
--------------------------------

This operation allow you to send a smart contract 
that will be executed on the blockchain (not stored) and can perform all the state changes as a normal smart contract.
It's often the best way to deploy an other smart contract using the operation datastore of the execute operation.
Our `deployer <https://github.com/massalabs/massa-sc-toolkit/blob/main/packages/sc-deployer/src/index.ts>`_ 
use this operation to deploy smart contracts.

Operation call smart contact
----------------------------

This operation allow you to call the function of a smart contract that exists 
on the blockchain with specific parameters and coins.

An `example <https://github.com/massalabs/massa-web3#smart-contract-read-and-write-calls>`_ using massa-web3 will 
show you how to make call smart contract in JS/TS/NodeJS.

Read only execute and call smart contract
-----------------------------------------

You can also execute or call smart contracts in read only mode.
Read only execute will execute the smart contract as a normal execution but not in a block and so all the 
state changes and events will be returned to you instead of being applied in the blockchain.
This can help you estimate the cost of a future operation or the state changes it will imply.

Read only calls will execute the smart contract function and return the result without any state changes also.
It can allow to get some information or values from a smart contract and/or his storage.

An `example <https://github.com/massalabs/massa-web3#smart-contract-read-and-write-calls>`_ using massa-web3 
will show you how to make read-only calls and execute of smart contract in JS/TS/NodeJS.

Call in smart contract sdk
--------------------------

You can call a smart contract from the code of an other one. You can pass arbitrary parameters and 
coins to the called smart contract.

This can be done using the `call` function of the smart contract Assembly script sdk. You can find an example 
`here <https://github.com/massalabs/massa-sc-examples/blob/main/games/tictactoe/smart-contract/assembly/main.ts>`_

Local call in smart contract sdk
--------------------------------

As the call function, you can also call a smart contract from the code of an other 
one but execute it in your current context.

For example if the smart contract called use the function 
from the sdk `Storage.set(key, value)` the value will be set in the storage of the smart contract 
that call the other one and not in the storage of the called smart contract.
