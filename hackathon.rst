=============================================
Documentation for the GOTTA GO HACK Hackathon
=============================================

Welcome to the main technical documentation for the Hackathon.
You will find everything you need to develop this weekend.

Massa client
============

First of all you need to have a client to interact with the network.
We recommend to use the pre-built binairies available here:

https://github.com/massalabs/massa/releases

The zip will contains two folders one called `massa-node` and the other `massa-client`.
For this hackathon you will only need the client. In the folder `massa-client` you will
find an executable `massa-client` that will be your client for the whole Hackathon.

In the `massa-client/config` folder create a `config.toml` file with the following content:

.. code-block::
    [default_node]
    ip = "141.94.218.103"

This will allow your client to connect to a remote node run by the Massa team
so that you don't have to run a node yourself.

You can now run the client and generate a private key using the following commang in the client:

.. code-block::

    wallet_generate_secret_key

Now that you have created a wallet you can check the address, public key, balance, etc... with this command:

.. code-block::

    wallet_info

To develop and send smart-contracts to the blockchain you will need some tokens.
You can get some by sending your address to the faucet bot in the "testnet-faucet"
channel of our `Discord <https://discord.com/invite/massa>`_, or if you don't have
Discord ask us and we'll happily send you some.

If you check your wallet again, you should see coins on your address.
Now that you have an address with coins you can create and deploy your first smart contract. 

Smart contracts
===============

You'll find here relevant links to ressources for smart-contract development.

Setting up your working environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You'll find in the :ref:`Getting started tutorial <sc-getting-started>` all the informations
to setup your environment for smart-contract development.

Write your first smart contract
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You'll find a more involved example on :ref:`this page <sc-example-sum>`.

Testing your smart-contract
^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can test your smart-contract locally by using the `massa-sc-tester <https://github.com/massalabs/massa-sc-tester>`__.

Create your frontend
^^^^^^^^^^^^^^^^^^^^

As you saw in the tec-tac-toe example, you can create a website to interact with
the smart-contract using our `massa-web3 <https://github.com/massalabs/massa-web3>`_ library.

We have two example for websites:

- In JS you have the example of the `game of life <https://github.com/massalabs/massa-sc-examples/tree/main/games/game-of-life>`_
- In React you have the template `create-react-app-massa <https://github.com/massalabs/create-react-app-massa>`_
