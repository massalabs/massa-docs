.. index:: smart contracts, assemblyscript

.. _deploy-from-local-node:

Deploy a smart contract on a local node
========================================

In this section we will execute a smart contract by running your own node.

To execute the smart contract you will need:

- A client configured with an address having coins.
- A smart contract compiled in WebAssembly. We will use the `hello world` contract from the :ref:`getting-started <sc-getting-started>` step.

.. note::
   * If you don't have any wallet configured yet, :ref:`create a new one <wallet>`.
   * If you're using a brand new wallet, add some coins by sending your address to
     `testnet-faucet discord channel <https://discord.com/channels/828270821042159636/866190913030193172>`_.
   * In any case, keep the `Address` and `Secret key` of your wallet, you will use it later.

Let's go!

Configure the client
~~~~~~~~~~~~~~~~~~~~

Make sure that you have the latest version of the Massa node. If not,
:ref:`install it <testnet-install>` and :ref:`run it <testnet-running>`.

.. note::

   You can also execute your smart contract on a local sandbox node.
   To learn more about the sandbox node, follow this tutorial:
   :ref:`Local network generation <local-network-generation>`.

Execute the smart contract on the node
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Everything is in place, now we can execute the `hello world` smart contract on your
local node with the following command inside the **client cli**:

.. code-block:: shell

   send_smart_contract <address> <path to wasm file> 100000 0 0

.. note::

   We are executing the send_smart_contract command with 6 parameters:

   - <address>: the address of your wallet kept during previous step;
   - <path to wasm file>: the full path (from the root directory to the file extension .wasm)
     of the hello smart contract, generated in the previous chapter.
   - 100000: the maximum amount of gas that the execution of your smart contract is allowed to use.
   - Two 0 parameters that can be safely ignored by now. If you want more info on them, use the command
     `help send_smart_contract`.

.. note::

   To go inside the **client cli**, open a terminal in `massa/massa-client` directory and run `cargo run`.

If everything went fine, the following message should be prompted:

.. code-block:: shell

   Sent operation IDs:
   <id with numbers and letters>

In that case, you should be able to retrieve the event with the `Hello world` emitted.
Use the following command inside the **client cli**:

.. code-block:: shell

   get_filtered_sc_output_event operation_id=<id with numbers and letters>

If everything went well you should see a message similar to this one:

.. code-block:: shell

    Context: Slot: (period: 627, thread: 22) at index: 0
    On chain execution
    Block id: VaY6zeec2am5i1eKKPzuyvhbzxVU8mts7ykSDj5usHyobJee8
    Origin operation id: wHGoVbp8QSwWxEMzM5nK9CpKL3SpNmxzUF3E4pHgn8fVkJmR5
    Call stack: A12Lkz8mEZ4uXPrzW9WDo5HKWRoYgeYjiQZMrwbjE6cPeRxuSfAG

    Data: Hello world!

Congratulations! You have just executed your first smart contract!
