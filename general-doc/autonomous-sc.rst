.. index:: smart contracts, autonomous

Massa's Autonomous Smart Contracts
==================================

.. _asc-intro:

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
introduce self wake-up capabilities to smart contracts. In the future, smart contracts could be programmed to perform
arbitrary operations, for example triggering a call when a specific exchange rate target of an LP pool is met.

Such automated capabilities open the door to various applications, from automated liquidation of under-collateralized
positions on lending protocols, to on-chain trading bots, or ever evolving NFTs.
