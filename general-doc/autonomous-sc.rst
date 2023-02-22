.. index:: smart contracts, autonomous

.. _general-asc:

Autonomous Smart Contracts
==========================

Massa smart contracts have the unique feature of being able to autonomously define when they will be active, without
external actors. By the end of this page, you will have a basic understanding of:

- The limitations of smart contracts as they exist in today's blockchains
- How Massa solve limitations current smart contracts by allowing to pre-program execution
- The mechanisms behind autonomous smart contracts

Introduction
------------

Issues with current smart contracts
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Automating IT processes are at the heart of pretty much every industry we can think of, and if we narrow it down to
modern finance, nowadays most actions are initiated by automated mechanisms. When we take a closer look at decentralized
finance, only certain actions of lending and arbitration are done automatically, however they are executed by bots
operating off-chain. This is because without external calls, smart contracts as they exist in all public blockchains
cannot perform automated operations.

Many decentralized protocols rely on recurrent triggers of certain functions in order to work as planned. In case of
decentralized lending protocols, borrowers lock crypto assets (cryptocurrencies or fungible tokens) in order to take out
loans. When the price of the collateralized asset decreases below a threshold, the borrower’s position becomes
under-collateralized if he doesn’t promptly react to the decrease in value of that collateral. To ensure that the
protocol behaves correctly, such positions must be liquidated. Liquidations are currently performed by organizations or
individuals running bots, usually on some centralized cloud services.

The need for a reliable automation mechanism
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are countless applications that rely on such recurrent triggers. As a result, a lot of time and energy has been
spent trying to develop more reliable networks of bots to guarantee that transactions are executed when needed. However,
as those solutions are inherently off-chain, there’s no guarantee that the execution will be effectively triggered. And
when those bots `fail to execute such transactions <https://insights.glassnode.com/what-really-happened-to-makerdao/>`_,
those protocols are at risk (and the applications built on top of them).

Autonomous Smart Contracts
--------------------------

Massa’s Autonomous Smart Contracts solve the issues of lacking reliability, sophistication and centralization around
dApps that want to offer automated smart contract executions on behalf of their users. Autonomous Smart Contracts
introduce self wake-up capabilities to smart contracts. Smart contracts can be programmed to perform arbitrary
operations, for example triggering a call when a specific exchange rate target of an LP pool is met.

Such automated capabilities open the door to various applications, from automated liquidation of under-collateralized
positions on lending protocols, to on-chain trading bots, or ever evolving NFTs.

Mechanism in Massa network
~~~~~~~~~~~~~~~~~~~~~~~~~~

This system works by emitting messages which will schedule an execution. To do so, the following must be specified:

- The address of the SC to be executed
- The function to be executed
- The desired execution window
- (Optional) The activation triggers

Normally, when you submit an operation, the operation is sent in the operation pool and in the future included in a
block to be executed: you pay the gas at execution time. With autonomous SCs, the message is emitted inside of the
operation and and you pay the gas for the message execution upfront. The message is then sent to another pool called the
async pool.

This pool is deterministic by nature, as it's filled with information that comes from operations included in blocks
which is processed by every node of the network.

The pool has a finite size, and sorts its messages with a similar behaviour as the operation pool: there is a priority,
which is computed from the message emission fees, maximum execution gas, and length of the execution window. The
messages get removed after a certain number of slots if they were not executed.

The number of messages that can be executed by each slot are limited by a maximum amount of gas like normal operations
and so it's possible that your message isn't executed at the slot that you want (such as operations that could be not
included in the first block after you sent it),
but in a later slot, when there is enough space to include your message. If you want to have your message to be included
as soon as possible,  you have to put more fees than the others (just like the operations).

The order of the messages is determined by the formula:

`(Reverse(Ratio(msg.fee, max(msg.max_gas,1))), emission_slot, emission_index),`

where `emission_index` is an index that differentiate multiple messages created in the same slot.

Going further
-------------

If you want to go further and start coding your own autonomous smart contract, you can read more about the ABI that
allows you to automate your smart contracts and find examples of autonomous smart contracts :ref:`here <web3-asc>`.
