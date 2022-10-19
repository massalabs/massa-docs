.. index:: smart contracts, autonomous

##################################
Massa's Autonomous Smart Contracts
##################################

.. _asc-intro:

Introduction
============

Issues with current smart contracts
-----------------------------------

The ability to perform autonomous tasks is widespread in any modern IT infrastructure.
However it is totally absent from decentralized applications that run on public blockchains
such as Ethereum: without external calls, smart contracts as they exist in all public blockchains
cannot perform operations.

Many decentralized protocol rely on recurrent trigger of certain fonctions to function properly.
This is the case for instance for the MakerDAO protocol. In this protocol, users can lock ETH as
a collateral to take out loans. If the price of the ETH decreases below a threshold, then
the position can become under-collateralized. To ensure that the protocol behaves correctly
and that funds from lenders cannot be lost, such positions must be liquidated.
Such liquidations are currently performed by organizations or individuals running bots, usualy on
some centralized cloud services.

The need for autonomous smart contracts
---------------------------------------

There are countless of applications that rely on recurrent calls to function properly.
As a result, a lot of time and energy has been spent trying to develop more reliable networks
of bots to guarantee that transactions are always executed when needed. However, as those
solutions are inherently off-chain, there's no guarantee that the execution will
be effectively triggered. And when those bots
`fail to execute such transactions <https://insights.glassnode.com/what-really-happened-to-makerdao/>`_,
those protocols are at risk (and the applications built on top of them).

Massa's **Autonomous Smart Contracts** solve the issues of lacking reliability,
sophistication and centralization around dapps that want to offer automated smart contract
executions on behalf of their users. Autonomous Smart Contracts introduces self wakeup capabilities
to smart contracts. In practice, smart contracts can be programmed to perform arbitrary operations
in the future, recurrently.

Such a capability opens the door to multiple applications, from automated liquidation of under-collateralized
positions on lending protocols to on-chain trading bots or ever evolving NFTs.
