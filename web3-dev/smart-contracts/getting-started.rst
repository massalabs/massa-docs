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

`@massalabs/sc-project-initializer <https://github.com/massalabs/massa-sc-toolkit/tree/main/packages/sc-project-initializer>`_
is a tool that creates a boilerplate smart-contract project.
To create a smart-contract project, invoke the initializer by running:

.. code-block:: shell

  npx @massalabs/sc-project-initializer init my-sc && cd my-sc

You now have a npm project, created in `my-sc` folder. It contains all tools that will be used to compile AssemblyScript
to Wasm bytecode, as well as deploying and running your smart contracts.

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
   :linenos:
   :emphasize-lines: 5,6,10

   // The entry file of your WebAssembly module.
   import { generateEvent } from '@massalabs/massa-as-sdk';
   import { Args } from '@massalabs/as-types';

   export function sayHello(binaryArgs: StaticArray<u8>): StaticArray<u8> {
     const name = new Args(binaryArgs)
       .nextString()
       .expect('Name argument is invalid');
     const message = `Hello world! Greetings from ${name} 👋`;
     generateEvent(message);
     return [];
   }

.. note::
   Massa smart contract module (`@massalabs/massa-as-sdk`) contains the API you need
   to use to interact with the external world of the smart contract (the node, the ledger...).

Don't forget to save the file. Before starting compilation, just a few words to describe what is used here:

* line 5: `sayHello` function is exported. This means that the `sayHello` function can be called
  from outside of the smart contract. For instance by another smart contract or through the API
  (see :ref:`massa-web3 <web3-massa-web3>`).
* line 6: `Args` class is used to deserialize arguments' bytecode (see :ref:`massa sc types <sc-types>`).
* line 10: `generateEvent` function will generate an event with the message string given as argument. Events can be
  recovered later from the Massa client or through the API.


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
This is done using the `.env` file. The initializer comes with a template `.env.example` file that you can use:

.. code-block::

    cp .env.example .env

Now fill the `WALLET_PRIVATE_KEY` variable with your wallet secret key.

You are now ready to deploy you smart contract with the following command:

.. code-block:: shell

   npm run deploy

If everything goes as expected, this should produce the following output:

.. code-block:: shell

   > my-massa-sc@0.0.1 deploy
   > npm run build && ts-node src/deploy.ts


   > my-massa-sc@0.0.1 build
   > npx massa-as-compile

   2 files to compile
   assembly/contracts/main.ts

   assembly/contracts/run.ts

   Wallet balance:  999999997.721
   Operation submitted with id: O12BqB9CK5JVU7bz6ApUSvDZJUpUSEz3BxLRUR2SCXzGy2eEwZmj
   Waiting for events...
   Deployment success with events:
   Contract deployed at address: A12V8LigWFd2vFgR34Vch5tqBWC6QMmNLhgBeFNh1pW5hpUGDT75

To facilitate the work of the developer, the `npm run deploy` command will also build your contracts under the hood so
you don't have to run `npm run build` yourself.

Wait for a few seconds... The last line of the output is the deployed smart contract address.

Calling your smart contract
---------------------------

Open the `assembly/contracts/run.ts` file and replace its content by the following one.

.. code-block:: typescript
   :linenos:
   :emphasize-lines: 7,10,21,26

   import { Address, call, callerHasWriteAccess } from '@massalabs/massa-as-sdk';
   import { Args } from '@massalabs/as-types';

   /**
    * This function is meant to be called only one time: when the contract is deployed.
    */
   export function constructor(binaryArgs: StaticArray<u8>): StaticArray<u8> {
     // This line is important. It ensure that this function can't be called in the future.
     // If you remove this check someone could call your constructor function and reset your SC.
     if (!callerHasWriteAccess()) {
       return [];
     }
     callHelloContract(binaryArgs);
     return [];
   }

   /**
    * @param binaryArgs - The address of the sum contract encoded with `Args`
    * @returns empty array
    */
   function callHelloContract(binaryArgs: StaticArray<u8>): StaticArray<u8> {
     const args = new Args(binaryArgs);
     const address = new Address(
       args.nextString().expect('Address argument is missing or invalid'),
     );
     call(
       address,
       'sayHello',
       new Args().add(args.nextString().expect('Name argument is missing')),
       0,
     );
     return [];
   }


* line 7: `constructor` is a special function that is called when the run smart contract is deployed.
* line 10: we ensure that the caller of this function has the right access on the smart contract. Only the deployer has
  it. This way, we are sure that the constructor can only be called at the deployment.
* line 21: `callHelloContract` function the `sayHello` function of the smart contract.
* line 26: `call` function calls the given function of the smart contract, deployed at the given address.

The constructor of this contract expect 1 argument in binary format. This argument is the encoded version of 2 values:

* the address of the `main.ts` smart contract,
* the name to pass to the `sayHello` function of that smart contract.

Now let's have a look at the deployer script `src/deployer.ts` and stop at the `deploySC` instruction.

Modify the line 26:

.. code-block:: typescript

  let deployedInfo = await deploySC(

Add the import of IEvent at the beginning of the file.

.. code-block:: typescript

   import { Args, IEvent } from '@massalabs/massa-web3';

Add this code snippet after the call to the `deploySC` function:

.. code-block:: typescript
   :lineno-start: 41
   :emphasize-lines: 10

   const data = (deployedInfo.events?.find((e) => e.data) as IEvent).data;
   const address = data.split('Contract deployed at address:')[1].trim();
   deployedInfo = await deploySC(
     publicApi,
     deployerAccount,
     [
       {
         data: readFileSync(path.join(__dirname, 'build', 'run.wasm')),
         coins: 0,
         args: new Args().addString(address).addString('Bob'),
       } as ISCData,
     ],
     0,
     4_200_000_000,
     true,
   );

The function `deploySC` lets us set the smart contract to be deployed when running `npm run deploy`.

* line 50: we specify the arguments to pass to the `constructor` function. Let's also `Bob` used as an argument by your
  name so that it will be passed to the `sayHello` function of our smart contract!

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
   assembly/contracts/run.ts

   assembly/contracts/main.ts

   Wallet balance:  999999995.5035
   Operation submitted with id: O127k8e478tXupVfi83uxc44uChBCEmpsQjT55ZmV5WSnPSk2MWL
   Waiting for events...
   Deployment success with events:
   Contract deployed at address: A12uDz7zKPi9ZriaSJ57NG6jTyBLdowwgsxnuxivC36v5aWDHQdn
   Wallet balance:  999999993.286
   Operation submitted with id: O12pydU3p27HiGuhq17xvYQxX3hRQkwVoUB6UrtxyEP1teiVGMVE
   Waiting for events...
   Deployment success with events:
   Hello world! Greetings from Bob 👋
   Contract deployed at address: A124p6rAcHrwX41qFANRGUbWpCzt4fa27axKqPmxy5kjZQEZxhid
