===============
Getting started
===============

Welcome to the main technical documentation for the Hackathon.
You will find everything you need to develop this weekend.

Massa client
============

First of all you need to have a client to interact with the network.
We recommend to use the pre-built binaries available
`here <https://github.com/massalabs/massa/releases>`__.

The zip contains two folders, one called `massa-node` and the other `massa-client`.
For this hackathon you will only need the client.
In the folder `massa-client` you will find an executable
`massa-client` that will be your client for the whole Hackathon.

In the `massa-client/config` folder, create a `config.toml` file
with the following content:

.. code-block::

    [default_node]
    ip = "141.94.218.103"

This will allow your client to connect to the remote node run by Massa team
so that you don't have to run a node by yourself.

You can now run the client and generate a secret key using the
following command in the client:

.. code-block::

    wallet_generate_secret_key

Now that you have created a wallet you can check the address,
public key, balance, etc... with this command:

.. code-block::

    wallet_info

To develop and send smart contracts to the Massa blockchain you will need some tokens.
You can get some by sending your address to the faucet bot in the "testnet-faucet"
channel of our `Discord <https://discord.com/invite/massa>`_, or if you don't have
Discord make sure to tell us and we'll happily send you some.

If you check your wallet again, you should see coins on your address.
Now that you have an address with coins you can create and deploy your
first smart contract. 

Smart contracts
===============

Here you'll find all relevant links to resources for smart-contracts development.

Setting up your working environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The :ref:`Getting started tutorial <sc-getting-started>` will get you through
all the steps to set up your environment for smart-contract development,
and show you how to execute a simple "Hello world" smart contract.

Write your first smart contract
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:ref:`This example <sc-example-sum>` showcases a more involved example
of a Massa smart contract.

Testing your smart contract
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The `massa-sc-tester <https://github.com/massalabs/massa-sc-tester>`_
allows you to test your smart contracts locally.

Create your frontend
^^^^^^^^^^^^^^^^^^^^

To interact with smart contracts from your decentralized application we recommend
to use our `massa-web3 <https://github.com/massalabs/massa-web3>`_ library.

You'll find in the `create-react-app-massa <https://github.com/massalabs/create-react-app-massa>`_
repo a React template that is compatible with our massa-web3 library.

You'll find several examples of frontends developed
using the massa-web3 library or in pure JS in our
`massa-sc-examples <https://github.com/massalabs/massa-sc-examples>`_
repository (blog, games).

Other resources
^^^^^^^^^^^^^^^

- `massa-sc-examples <https://github.com/massalabs/massa-sc-examples>`_ is a
  collection of examples of Massa smart contracts
- :ref:`External resources <web3-external-resources>` is a collection of
  useful resources developed by external contributors.

**You should be all set to start building on-top of Massa now. Good luck!**


Thyra
=====

Thyra is a client developed by Massa that plays the role of a gateway to the Massa blockchain.


What is does and how to use it
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To date, Thyra will enable you to store content, like a website, on the blockchain and assure its immutability as well as facilitate all the process. 

To start using it, follow the steps provided `here <https://docs.massa.net/en/latest/web3-dev/decentralized-web.html>`__.

===============
Troubleshooting
===============

There are some limitations and workarounds that we have listed, have a look `here <https://github.com/massalabs/thyra/wiki/Hackathon-Nov-22---Troubleshooting>`__

