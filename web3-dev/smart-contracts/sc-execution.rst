========================
Smart-contract execution
========================

Introduction
============

In Massa, you can execute arbitrary smart contracts. You are not limited to the classic way of deploying and then
calling that contract. There are different possibilities how to execute smart contracts depending on the context. This
documentation will explain you different possibilities and how to use them. You can choose the one that most matches
your use-case.

You can find examples for the different ways to execute smart contracts in the smart contract 
sdk `massa-web3 <https://github.com/massalabs/massa-web3>`_.

Different ways to execute smart contracts
=========================================

"Execute Smart Contract" operation
----------------------------------

This operation allows to send a smart contract that will be executed on the blockchain (not stored), that can perform
all the state changes as a normal smart contract.
It's often the best way to deploy an other smart contract using the operation datastore of the execute operation.
Our `deployer <https://github.com/massalabs/massa-sc-toolkit/blob/main/packages/sc-deployer/src/index.ts>`_
uses this operation to deploy smart contracts.

"Call Smart Contract" operation
-------------------------------

This operation allow you to call the function of a smart contract that exists
on the blockchain with specific parameters and coins.

An `example <https://github.com/massalabs/massa-web3#smart-contract-read-and-write-calls>`_ in massa-web3
shows you how to make the call smart-contract operation in JS/TS/NodeJS.

Read-only Execute and Call Smart Contract
-----------------------------------------

You can also execute or call smart contracts in read-only mode.
Read-only execute will execute a smart contract, as in any normal execution, but not store it in a block. Therefore, all
state changes and events will be returned to you, instead of being applied (stored) in the blockchain.
This can help you estimate the cost of a future operation, or the state changes it will imply.
For example, it can provide information about a smart contract, as well as its storage content.

An `example <https://github.com/massalabs/massa-web3#smart-contract-read-and-write-calls>`_ using massa-web3
will show you how to make read-only calls and read-only execute of a smart contract in JS/TS/NodeJS.

Call in Smart-contract SDK
--------------------------

Calling one smart contract from another is a feature provided by the smart-contract Assembly Script SDK. 

`this example <https://github.com/massalabs/massa-sc-examples/blob/main/games/tictactoe/smart-contract/assembly/main.ts>` describes the use of the `call` function to send coins from the call source to the call destination.

Local Call in Smart Contract SDK
--------------------------------

As the call function, you can also call a smart contract from the code of an other
one, but execute it in your current context.

For example if the called smart contract uses the SDK function `Storage.set(key, value)`, the value will be set in the storage of the smart contract that made the call. The storage of the called smart contract won't be affected. 
