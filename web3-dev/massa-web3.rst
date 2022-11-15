.. index:: library, massa-web3, TypeScript

.. _web3-massa-web3:

##########
Massa web3
##########

`massa-web3 <https://github.com/massalabs/massa-web3>`_ is a TypeScript library that
allow you to interact with the Massa blockchain through a local or remote Massa node.
In particular the massa-web3 library will allow you to call the
:ref:`JSON-RPC API <technical-api>`, but also to fetch and poll events from smart
contracts on the Massa blockchain, deploy smart contracts and much more.
Documentation for the massa-web3 library is available on the
`Github repository <https://github.com/massalabs/massa-web3>`_. The library is published
on npmjs under `@massalabs/massa-web3 <https://www.npmjs.com/package/@massalabs/massa-web3>`_


`create-react-app-massa <https://github.com/massalabs/create-react-app-massa>`_ is a
minimal React template made for our massa-web3 library.

You'll find examples of frontends using the `massa-web3` library in the
`massa-sc-examples <https://github.com/massalabs/massa-sc-examples>`_ repository:

- `blog <https://github.com/massalabs/massa-sc-examples/tree/main/blog>`_ is an
  example of a decentralized blog platform.

Building Decentralized Application
##################################

Prerequisites:
  * having `Thyra <https://github.com/massalabs/thyra/>`_ installed and running on your computer
  * having a wallet in Thyra
  * the wallet needs to own some Massa
  * having a smart-contract :ref:`deployed <sending-sc-sum>`

**Network**: as a blockchain is a network of nodes, there could be several
networks. The main network is called the mainnet. There is also a testnet,
run by Massa team, where you can build application without paying real Massa
tokens. You can also run your own network, with only one node
(see :ref:`local-network-generation`).

A decentralized application is an application running on a blockchain. Even the
frontend can be hosted on the blockchain with
:ref:`web-on-chain <web3-decentralized-web>`.

To build such application you will use some libraries:
  * `massa toolkit <https://github.com/massalabs/massa-sc-toolkit/>`_
  * `massa assemblyscript sdk <https://github.com/massalabs/massa-as-sdk/>`_
  * `massa javascript web3 <https://github.com/massalabs/massa-web3/>`_

Then you will be able to build a frontend application that communicate with
Thyra's API to sign transactions and submit it to the blockchain. 

Then, use Thyra to deploy your frontend application.

..
  comment: TODO: add link to the page explaining how to deploy a website
