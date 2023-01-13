.. index:: smart contracts, assemblyscript

.. _sc-getting-started:

Getting started
===============

In this section you will learn how to set up your smart-contract development environment, how to deploy your
first Massa smart contract, and how to call that smart contract.

Setting up a new project
------------------------

Make sure you have a recent version of `Node.js <https://nodejs.org/>`_  and `npm <https://www.npmjs.com/>`_.
Update or `install <https://docs.npmjs.com/downloading-and-installing-node-js-and-npm>`_ them if needed.

`@massalabs/sc-project-initializer <https://github.com/massalabs/massa-sc-toolkit/tree/main/packages/sc-project-initializer>`_ is a tool that creates a boilerplate
smart-contract project. To create a smart-contract project, invoke the initializer by running:

.. code-block:: shell

  npx @massalabs/sc-project-initializer init my-sc && cd my-sc

You now have a npm project, created in `my-sc` folder. It contains all tools that will be used to compile AssemblyScript to Wasm bytecode, as well as deploying and running your smart contracts.

Congratulations! Now you have a fully set up project and you are ready to add some code.

.. note::
   A few words on project folders:

   * `assembly` is where the AssemblyScript source code goes.
   * `build` will be created during compilation and will contain compiled smart contracts.
   * `src` contains Typescript code used to interact with the network.


Create your first smart contract
--------------------------------

Since the beginning of mankind, humans explain how to use a program, a new language,
a service by implementing a *Hello world!*. Your first smart contract will be no exception!

Open the `assembly/contracts/main.ts` file and replace its content by the following one:

.. code-block:: typescript

   // The entry file of your WebAssembly module.
   import { Args } from '@massalabs/as-types';
   import { generateEvent } from '@massalabs/massa-as-sdk';

   export function sayHello(args: StaticArray<u8>): void {
      const name = new Args(args).nextString().expect('name argument is invalid');
      const message = `Hello world! Greetings from ${name} 👋`;
      generateEvent(message);
   }

.. note::
   Massa smart contract module (`@massalabs/massa-as-sdk`) contains the API you need
   to use to interact with the external world of the smart contract (the node, the ledger...).

Don't forget to save the file. Before starting compilation, just a few words to describe what is used here:

* line 3: `sayHello` function is exported. This means that the `sayHello` function can be called
  from outside of the smart contract. For instance by another smart contract or through the API (see :ref:`massa-web3 <web3-massa-web3>`).
* line 5: `Args` class is used to deserialize arguments' bytecode (see :ref:`massa sc types <sc-types>`).
* line 7: `generateEvent` function will generate an event with the message string given as argument. Events can be recovered later from the Massa client or through the API.

Now that everything is in place, we can start the compilation step by running the following command:

.. code-block:: shell

   npm run build

Congratulations! You have generated your first smart contract: the `main.wasm` file in `build` directory.

.. note::

   If due to bad luck you have an error at compilation time:

   * check that you properly followed all the steps,
   * do a couple a internet research,
   * look for any similar issue (open or closed) in `massa-as-sdk <https://github.com/massalabs/massa-as-sdk/>`_.

   If you find nothing, feel free to contact us on `Discord <https://discord.gg/massa>`_ or
   directly open an `issue <https://github.com/massalabs/massa-as-sdk/issues>`_.

Deploy your smart contract
--------------------------

Your smart contract is now ready to be deployed and later executed on the Massa network.

Uploading a smart contract on the Massa blockchains requires coins. On top of the usual gas
costs, Massa has also :ref:`storage costs <storage-cost>`. In order to deploy your smart contract,
you thus need to own a Massa wallet and have some MAS coins on it.

.. note::
   * If you don't have any wallet configured yet, :ref:`create a new one <wallet>`.
   * If you're using a brand new wallet, add some coins by sending your address to
     `testnet-faucet discord channel <https://discord.com/channels/828270821042159636/866190913030193172>`_.
   * In any case, keep the `Address` and `Secret key` of your wallet, you will use it later.


To pay for the operation cost, you need to configure your project with your wallet's secret key.
This is done using the `.env` file. The initializer comes with a template `.env` file that you can use:

.. code-block::

    cp .env.example .env

Now fill the `WALLET_PRIVATE_KEY` variable with your wallet secret key.

You are now ready to deploy you smart contract with the following command:

.. code-block:: shell

   npm run deploy

If everythings goes as expected, this should produce the following output:

.. code-block:: shell

   > my-massa-sc@0.0.1 deploy
   > npm run build && ts-node src/deploy.ts


   > my-massa-sc@0.0.1 build
   > npx massa-as-compile

   2 files to compile

   Wallet balance:  474.15525
   Operation submitted with id: O12aescJDj7gps3rxmXzh2NYoehSDYGtLBJYJEZpidAjjMJtJRD7
   Waiting for events...
   Deployment success with events:
   Contract deployed at address: A1u6xTYnRBM5dDJPiXV5CpV4FXRiwDTeHmgUv3zLmdBr2J7aaKu

To facilitate the work of the developer, the `npm run deploy` command will also build your contracts underthehood so you don't have to run `npm run build` yourself.

Wait for a few seconds... The last line of the output is the deployed smart contract address.
Save it somewhere, it will be used in the next step.

Calling your smart contract
------------------------

Open the `assembly/contracts/run.ts` file and replace its content by the following one.
Then replace `<your contract address>` by the address of the deployed contract that you obtained in the previous step.

.. code-block:: typescript

   import { Address, call } from '@massalabs/massa-as-sdk';
   import { Args } from '@massalabs/as-types';

   export function constructor(args: StaticArray<u8>): StaticArray<u8> {
      callHelloContract(args);
      return [];
   }

   function callHelloContract(args: StaticArray<u8>): void {
      const address = new Address(
         '<your contract address>',
      );
      call(address, 'sayHello', new Args(args), 0);
      return;
   }

* line 4: `constructor` is a special function that is called when the run smart contract is deployed.
* line 9: `callHelloContract` function initialises an Address object using the address of the deployed smart contract and then calls the `sayHello` function of the smart contract.
* line 13: `call` function calls the given function of the smart contract, deployed at the given address.

Now that everything is ready, we have to build our new contract:

.. note::
   * The `npm run build` command will build every smart contract in the folder `assembly/contracts`.

.. code-block:: shell

   npm run build

Now let's have a look at the deployer script `src/deployer.ts` and stop at the `deploySC` instruction.

.. code-block:: typescript

  ...
  await deploySC(
    publicApi,
    deployerAccount,
    [
      {
        data: readFileSync(path.join(__dirname, 'build', 'main.wasm')),
        coins: 0,
        args: new Args().addString('test'),
      } as ISCData,
    ],
    0,
    4_200_000_000,
    true,
  );
  ...

This function lets us set the smart contract to be deployed when running `npm run deploy`.
It also allows us to pass arguments to the call to the `constructor` function.
Let's modify the code in order to deploy our run smart contract by replacing `main.wasm` by `run.wasm`.
Let's also replace the `test` string used as an argument by our name so that it will be passed to the `sayHello` function of our smart contract!

We are now ready to deploy our run smart contract:

.. code-block:: shell

   npm run deploy

The output should looks like the following:

.. code-block:: shell

   > my-massa-sc@0.0.1 deploy
   > npm run build && ts-node src/deploy.ts


   > my-massa-sc@0.0.1 build
   > npx massa-as-compile

   2 files to compile


   Wallet balance:  469.81775
   Operation submitted with id: O12U6qa379CFeyYVJhkr5FTAzzgepwFabanNxCffyuis3jcJVMxP
   Waiting for events...
   Deployment success with events:
   Hello world! Greetings from Bob 👋
   Contract deployed at address: A12TosPSoPoQoSLrEnsmbJMCLRbRgbxGSpz8q4dsnFHE9Psr4NBU

That's it! After a few seconds you should see the "Hello world! Greetings from <Name> 👋`" message coming from the contract's event.

