.. _sc-playground:

Smart-contract Playground
=========================

`Smart-contract Playground <https://massa.net/sc-playground>`_ is a simple interface for writing, unit-testing, compiling, and sharing the smart-contract code written in Assembly Script. It runs in your browser’s local storage, and allows you to work on one smart-contract file. 

This is the first release of the Playground, and as such it comes with a few limitations. The main ones are that you can’t deploy or interact with the smart-contract in it yet. 

At the moment, Playground is also limited in terms of imported smart-contract methods.

The available methods are:

* getOf & setOf to to interact with data (read and write) 
* generateEvent to log blockchain content easily
* unsafeRandom to generate a random Number between 0 and the max Safe integer

The available classes are:

* Address

Despite these limitations, you can explore writing, testing and compiling following types of contracts:

* Fungible tokens
* Non-fungible tokens
* Lottery
* Video games
* Price oracles

Here’s an `example of a Fungible token contract and its unit-test <https://massa.net/sc-playground/?ext_code_url=https://raw.githubusercontent.com/massalabs/massa-sc-playground/main/examples/erc20.ts&ext_unit_test_url=https://raw.githubusercontent.com/massalabs/massa-sc-playground/main/examples/testERC20.ts>`_ you can explore directly in the Playground’s workspace. 

If you need more functionality and methods, or if you wish to request additional features, please submit an issue in `the Smart-contract Playground Github repository <https://github.com/massalabs/massa-sc-playground>`_. We will do our best to provide you with everything you need to explore the power and potential of Massa’s smart contracts.

