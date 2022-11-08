.. _sc-example-sum:

Massa's smart-contracts example
===============================

.. note::

    This tutorial doesn't assume any existing knowledge of the Massa protocol.

In this tutorial, we will go through all the steps required to create a smart-contract on Massa.

You can find the complete project on this `Github repository <https://github.com/massalabs/massa-sc-example-sum>`_.

Prerequisites
-------------

Smart-contracts are written in `Assembly Script <https://www.assemblyscript.org/>`_, and so we’ll assume that you have some familiarity with it, but you should be able to follow along even if you’re coming from a different programming language. We’ll also assume that you’re familiar with programming concepts like functions, objects, arrays, and to a lesser extent, classes.

Writing your smart-contract
---------------------------

Smart-contract in Massa are written in `Assembly Script <https://www.assemblyscript.org/>`_ and then compiled to `WebAssembly <https://webassembly.org/>`_ (WASM). We chose WebAssembly as it is efficient and can be compiled from several languages, including Assembly Script.

Setup
~~~~~

You need `node`, `npm` and `git` to initialize the project!

.. code-block:: shell

    npx @massalabs/sc-toolkit init massa-sc-example-sum

.. _writing-sc-sum:

Writing the smart-contract
~~~~~~~~~~~~~~~~~~~~~~~~~~

Smart-contracts are in the `assembly` directory.
The `index.ts` will be our smart contract.
The `main.ts` contains a function that will deploy our smart contract on the Massa blockchain. It may be confusing right now, but we'll go through all these steps in the following.

For this tutorial, we will create a very simple smart contract that calculate the sum of two integers.

Let's start with the sum smart-contract.

.. code-block:: typescript

    import { generateEvent } from "@massalabs/massa-as-sdk";

    // This is my SC

    function add(a: i32, b: i32): i32 {
        return a + b;
    }

    export function sum(args: string): string {
      const byteArray = fromByteString(args);
      const a = toInt32(byteArray);
      const b = toInt32(byteArray, 4);
      const result = add(a, b);
      generateEvent(`Sum (${a.toString()}, ${b.toString()}) = ${result.toString()}`);
      return result.toString();
    }

    function fromByteString(byteString: string): Uint8Array {
      const byteArray = new Uint8Array(byteString.length);
      for (let i = 0; i < byteArray.length; i++) {
        byteArray[i] = u8(byteString.charCodeAt(i));
      }
      return byteArray;
    }

    function toInt32(byteArray: Uint8Array, offset: u8 = 0): i32 {
      // i32 is 4 bytes long
      if ((byteArray.length - offset) < 4) {
        return <i32>NaN;
      }

      let x: i32 = 0;
      x = (x | byteArray[offset + 3]) << 8;
      x = (x | byteArray[offset + 2]) << 8;
      x = (x | byteArray[offset + 1]) << 8;
      x = x | byteArray[offset + 0];
      return x;
    }

Calling function of a smart-contract that is stored in the blockchain with some arguments will start an assemblyscript runtime (wasmer).
This is why each function that you want to be able to call in your smart-contract must be exported with the `export` keyword and must take one string argument and return a value of type string.

Here, we are exporting the sum function. In this function, we deserialize the argument into two integers, with the help of `fromByteString` and `toInt32`.

.. note::
    Massalabs team is working on a better way to serialize and deserialize the function arguments.

Then, here is the `main.ts` file that will deploy our smart contract.

.. code-block:: typescript

    import { createSC, generateEvent, fileToBase64 } from '@massalabs/massa-as-sdk';

    // This is a SC that will deploy my SC (index.ts)

    export function main(_args: string): i32 {
      const bytes = fileToBase64('./build/index.wasm');
      const websiteDeployer = createSC(bytes);
      generateEvent(`Contract deploy at : ${websiteDeployer._value}`);
      return 0;
    }

Compiling your smart-contract
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Smart-contract can be compiled using the command:

.. code-block::

    npm run build

.. _sending-sc-sum:

Putting your smart-contract on the blockchain
---------------------------------------------

We'll now turn to the process of putting the smart-contract on the Massa blockchain.

For the deployment, you will need a wallet with some coins. 

.. code-block::

    cp .env.example .env

This command will create a `.env` file. Now fill it with your wallet keys and address.

Deploying a smart-contract on Massa blockchain is done by calling a temporary smart-contract that will store 
our sum smart-contract onto the ledger.

Sending the smart-contract to the Massa blockchain is done with the command `npm run deploy`.

.. code-block::

    npm run deploy build/main.wasm

This command will execute `main.ts` smart-contract, and this smart-contract will store the `sum.ts` smart-contract onto the ledger.

You will see an output like this:

.. code-block::

    > my-massa-sc@1.0.0 deploy
    > ts-node --esm deployer/deployment_script.ts build/main.wasm

    Smartcontract file path : build/main.wasm

    Deployment has begun...

    Deployment successfully ended with operation id CyZxiMa53WyLYjKUYrfEYCyaEvXU5EdUamUhNyGmATrd7M9Tx

    Retrieving first event...

    Contract deploy at: A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9

Interaction with the smart-contract
-----------------------------------

We will now interact with our sum smart-contract.

To interact with a smart-contract, we can write another smart-contract that will be executed, or use the `CallSC` function.
In our example, we will create the file `run.ts` in the `assembly` directory.

.. code-block:: typescript

    import { Address, call } from "@massalabs/massa-as-sdk";
    import { ByteArray } from "@massalabs/as/assembly/byteArray";

    export function main(): i32 {
        const address = new Address(
            "A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9"
        );
        call(
            address,
            "sum",
            ByteArray.fromI32(10 as i32)
                .toByteString()
                .concat(ByteArray.fromI32(13 as i32).toByteString()),
            0
        );
        return 0;
    }

Note that we use the address where the contract has been deployed: A1PjpgXyXSBeiG1rbXCP4ybhVccYzpysDKYmkymXWd81idutaD9.

As always, we need first to compile the smart-contract:

.. code-block::

    npx asc assembly/run.ts --target release --exportRuntime -o build/run.wasm

Then execute it:

.. code-block::

    npm run deploy build/run.wasm

Remember that our sum smart-contract compute the sum and emit an event with the result.

You will see this output:

.. code-block::

    > my-massa-sc@1.0.0 deploy
    > ts-node --esm deployer/deployment_script.ts build/run.wasm

    Smartcontract file path : build/run.wasm

    Deployment has begun...

    Deployment successfully ended with operation id 24zP8RFvj5wPEvu242WKZmCMRtxdK6gVMGkg1a2WM3YannqrMY

    Retrieving first event...

    Sum (10, 13) = 23

You can call the JSON RPC API function `get_filtered_sc_output_event` to get the event with; 

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
