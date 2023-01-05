========================
Smart-contract execution
========================

Introduction
============

Massa introduces more options when it comes to meeting different requirements for execution contexts.
In particular, you are not limited to the standard way of deploying smart contract, and then
calling that contract using a transaction.

This documentation details the different possibilities regarding smart contract execution,
and how to use them so that you can choose the one that most matches your use-case.

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

This operation allows you to call the function of a smart contract that exists
on the blockchain with specific parameters and coins.

An `example <https://github.com/massalabs/massa-web3#smart-contract-read-and-write-calls>`_ in massa-web3
shows you how to make the call smart-contract operation in JS/TS/NodeJS.

Read-only Execute and Call Smart Contract
-----------------------------------------

Read-only execute will "dry-run" a smart contract, i.e. execute as per normal, emitting its effects which can be read, but without propagating them into a block. 

Useful use-cases can include: 

- "Dry-running" future operations to estimate what their costs will be, and the state changes they imply
- Obtaining information on a smart contract, including its current state and stored data

A concrete example using massa-web3 library compatible with JS/TS/NodeJS can be found `here <https://github.com/massalabs/massa-web3#smart-contract-read-and-write-calls>`_ 

Call in Smart-contract SDK
--------------------------

Calling one smart contract from another is a feature provided by the smart-contract Assembly Script SDK. 

`this example <https://github.com/massalabs/massa-sc-examples/blob/main/games/tictactoe/smart-contract/assembly/main.ts>` describes the use of the `call` function to send coins from the call source to the call destination.

Local Call in Smart Contract SDK
--------------------------------

As the call function, you can also call a smart contract from the code of an other
one, but execute it in your current context.

For example if the called smart contract uses the SDK function `Storage.set(key, value)`, the value will be set in the storage of the smart contract that made the call. The storage of the called smart contract won't be affected. 
