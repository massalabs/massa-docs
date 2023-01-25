.. _sc-example-sum:

Massa's smart-contract by example
=================================

.. note::

    This tutorial doesn't assume any existing knowledge of the Massa protocol.

In this tutorial, we will go through all the steps required to create a smart contract on Massa.

You can find the complete project on this `Github repository
<https://github.com/massalabs/massa-sc-examples/tree/main/sum/contracts>`_.

Prerequisites
-------------

Smart contracts are written in `Assembly Script <https://www.assemblyscript.org/>`_, and so we’ll assume that you have
some familiarity with it, but you should be able to follow along even if you’re coming from a different programming
language. We’ll also assume that you’re familiar with programming concepts like functions, objects, arrays, and to a
lesser extent, classes.

Writing your smart contract
---------------------------

Smart contracts on Massa blockchain are written in `Assembly Script <https://www.assemblyscript.org/>`_ and then
compiled to `WebAssembly <https://webassembly.org/>`_ (WASM). We chose WebAssembly as it is efficient and can be
compiled from several languages, including Assembly Script.

Setup
~~~~~

Let's start by cloning the sum example repository. You need `node`, `npm` and `git` to initialize the project.

.. code-block:: shell

    git clone https://github.com/massalabs/massa-sc-examples && cd massa-sc-examples/sum/contracts && npm install

.. _writing-sc-sum:

Writing the smart contract
~~~~~~~~~~~~~~~~~~~~~~~~~~

Smart contracts are in the `assembly` directory. The `main.ts` will be our smart-contract file.

For this tutorial, we will create a very simple smart contract which calculates the sum of two integers.

You can find it here `assembly/main.ts`.

.. code-block:: typescript

    import { generateEvent, Args } from "@massalabs/massa-as-sdk";

    function add(a: i32, b: i32): i32 {
        return a + b;
    }

    export function sum(serializedArgs: StaticArray<u8>): StaticArray<u8> {
        const args = new Args(serializedArgs);
        const a = args.nextI32();
        const b = args.nextI32();
        const result = add(a, b);
        generateEvent(
            `Sum (${a.toString()}, ${b.toString()}) = ${result.toString()}`
        );
        return result.toString();
    }

Calling function of a smart contract that is stored in the blockchain with some arguments will start an assemblyscript
runtime (wasmer). This is why each function that you want to be able to call in your smart contract must be exported
with the `export` keyword and must take one `StaticArray<u8>` argument and return a value of type `StaticArray<u8>`.

Here, we are exporting the sum function. In this function, we deserialize the argument into two integers, with the help
of `fromByteString` and `toInt32`.

Compiling your smart contract
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Your smart contract can be compiled using the command:

.. code-block::

    npm run build

Note that a `build/deployer.wasm` file has also been generated. It will be used to deploy your contract on Massa
network.

.. _sending-sc-sum:

Deploy your smart contract on the blockchain
--------------------------------------------

We'll now turn to the process of putting the smart contract on the Massa blockchain.

For the deployment, you will need a wallet with some coins.

To send transaction on the network, you need to provide your wallet secret key. This is done using environment variable
in `.env` file.

.. code-block::

    cp .env.example .env

This command will create a `.env` file. Now fill it with your wallet's secret key (also called a "secret key" by Massa
client).

Contract deployment is done by calling a deployer smart contract which can be found here: `deployer/deployer.as.ts`. It
will store our sum smart contract onto the ledger. The deployer contract already includes your compiled `main.ts`
contract and has been itself compiled at the `npm run build` step.

We will send the deployer smart contract to the Massa blockchain with:

.. code-block::

    npm run deploy

This command will execute the compiled deployer `deployer.wasm`, and this smart contract will store the `main.ts` smart
contract onto the ledger.

You will see an output like this:

.. code-block::

    > sc-example-sum@0.0.1 deploy
    > ts-node deployer/deploy-contract.ts

    Deploying smartcontract: build/deployer.wasm

    Operation submitted successfully to the network. Operation id: <operation id string>

    Waiting for the state of operation to be Final... this may take few seconds

    Deployment success with event: Contract deployed at address: A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9

Interaction with the smart contract
-----------------------------------

We will now interact with our sum smart contract.

To interact with a smart contract, we can write another smart contract that will be executed, or use the `CallSC`
function. In our example, we will use the file `caller.ts` in the `assembly` directory.

.. code-block:: typescript

    import { Address, Args, call } from "@massalabs/massa-as-sdk";

    export function main(): i32 {
        const address = new Address(
            "A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9"
        );
        call(
            address,
            "sum",
            new Args()
                .add(21 as i32)
                .add(20 as i32),
            0
        );
        return 0;
    }

Note that we use the address where the contract has been deployed: A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9.

First we need to compile the `caller.ts` smart contract. For the covenience of this example we have added an npm script
`npm run build:caller` which will compile `caller.ts` and write the generated wasm in `build/caller.wasm`

.. code-block::

    npm run build:caller

Then deploy the caller smart contract:

.. code-block::

    npm run deploy build/caller.wasm

Remember that our sum smart contract computes the sum and emits an event with the result.

You will see this output:

.. code-block::

    > sc-example-sum@0.0.1 deploy

    > ts-node deployer/deploy-contract.ts build/caller.wasm

    Deploying smartcontract: build/caller.wasm

    Operation submitted successfully to the network. Operation id: <operation id string>

    Waiting for the state of operation to be Final... this may take few seconds

    Deployment success with event: Sum (10, 13) = 23

You can call the JSON RPC API function `get_filtered_sc_output_event` to get the event with:

.. code-block::

    curl --location --request POST 'https://test.massa.net/api/v2' \
    --header 'Content-Type: application/json' \
    --data-raw '{
      "jsonrpc": "2.0",
      "method": "get_filtered_sc_output_event",
      "params": [
        {
          "start": null,
          "end": null,
          "emitter_address": null,
          "original_caller_address": null,
          "original_operation_id": "24zP8RFvj5wPEvu242WKZmCMRtxdK6gVMGkg1a2WM3YannqrMY"
        }
      ],
      "id": 0
    }'

Do not forget to set the right operation id function params.

Here is an example of what you can find:

.. code-block:: json

    {
      "jsonrpc": "2.0",
      "result": [
        {
          "context": {
            "block": "qrMVKELonoVrPGE741NVLfELcbSXP3Lk7XHcimeyTi1GGVP5v",
            "call_stack": [
              "A12h7cTMMimawZ4o2yoc7hSJP5EuvrfZKePuPUjL94fNE3phvgo2",
              "A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9"
            ],
            "index_in_slot": 6,
            "is_final": true,
            "origin_operation_id": "24zP8RFvj5wPEvu242WKZmCMRtxdK6gVMGkg1a2WM3YannqrMY",
            "read_only": false,
            "slot": {
              "period": 96370,
              "thread": 27
            }
          },
          "data": "Sum (10, 13) = 23"
        }
      ],
      "id": 0
    }
