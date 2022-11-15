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

Here you can find an example dapp for the :ref:`sum smart-contract <sc-example-sum>`: 
`Massa DApp example: Sum <https://github.com/massalabs/massa-dapp-example-sum>`_.

Debugging
#########

This section will give you some tips to help you debug your dapp while building it.

Work in local
-------------

Run :ref:`a local node <local-network-generation>`.

Run Thyra in local: 

.. code-block::

    go run ./cmd/thyra-server/main.go --node-server LOCALHOST

Create a new wallet in `Thyra <http://my.massa/thyra/wallet/index.html>`_.

Modify the file `massa-node/base_config/initial_ledger.json`:

 .. code-block:: javascript

    {
        "ADDR": {
            "balance": "80000000",
            "datastore": {},
            "bytecode": []
        }
    }

to add some Massa tokens to your newly created address.

In your smart-contract projects, use this `.env` file:

.. code-block::

    WALLET_PRIVATE_KEY=""
    JSON_RPC_URL_PUBLIC=http://127.0.0.1:33035
    JSON_RPC_URL_PRIVATE=http://127.0.0.1:33034

Use the private key that the node-client gave you when calling
`wallet_generate_secret_key`. This wallet also needs to have some Massa
tokens, so it must appear in `massa-node/base_config/initial_ledger.json`
before running the node with `cd massa-node && cargo run --features sandbox`
in `massa <https://github.com/massalabs/massa>`_ directory.

Modify the file `massa/massa-client/base_config/config.toml` to the localhost RPC:

.. code-block::

    ip = "127.0.0.1"
    private_port = 33034
    public_port = 33035

Start a client with the command `cargo run` in `massa/massa-client` directory.

You might want to sign transaction to call smart-contract in your frontend
application:

.. code-block:: javascript

    const options = {
      method: "POST",
      url: "https://my.massa/cmd/executeFunction",
      headers: { "Content-Type": "application/json" },
      data: {
        nickname: "wallet",
        name: "hello",
        at: "A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB",
        args: "",
        gaz: { price: 1000, limit: 700000000 },
        coins: 0,
        expiry: 3,
        fee: 0,
        keyId: "default",
      },
    };

    return axios
      .request(options)
      .then(function (response) {
        return response.data;
      })
      .catch(function (error) {
        console.error(error);
      });

This performs an HTTP POST call to Thyra, asking to create a transaction
that will call the function named `hello` of the smart-contract located at
`A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB` with the wallet name
`wallet`.

This opens a password prompt and you will be able to see the response of
the call in the development tools of the browser. The response is the operation
id, like for example `2mLLkdKB4BY4hJQFNwGZ5oQVhky13EVZAwHJXCGQnd3FRHUoHw`.

If the operation failed, the response contains the details.

Now you can now use the commands of the node client to see the details of your
operation:

.. code-block::

    get_operations 2mLLkdKB4BY4hJQFNwGZ5oQVhky13EVZAwHJXCGQnd3FRHUoHw

This will output something like 

.. code-block:: text

    Operation 2XwBkDZrou8CxvAs9aTJsuvuXFQLiTwTHbHEZuWCugdVpSWLQX[ (in pool)]
    In blocks:
      - 2n6JLR7UbQvzUW4joxW3D1TbuJtso7Szp9jvYipVTmtFceVAZv
    Signature: WJVPHZ7QckXiMvFHFFNYt7zzmfFYawWdTuhnv15sNxCcWy92tXfmHrMgt7FSeWjvSUhqNzD5kzbVzr2ni6NQ8aybkMozo
    Creator pubkey: P12fnkn6fMhjZHatWsQ3k1L4B7hb99XdjbWCmVthxCwbisRAMk2P
    Creator address: A12LempfubRfRZoRh7Dr4nWyTUYKAQs7Be1cZ9Gejcx7NXYG4HXD
    Id: 2XwBkDZrou8CxvAs9aTJsuvuXFQLiTwTHbHEZuWCugdVpSWLQX
    Fee: 0
    Expire period: 543
    Operation type: CallSC:
      - target address:A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB
      - target function:hello
      - target parameter:
      - max_gas:700000000
      - gas_price:0.000001
      - coins:0

You can also see the events emitted by your contract:

.. code-block::

    get_filtered_sc_output_event caller_address=A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB

(assuming that your contract is deployed at `A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB`).

.. code-block::

    Context: Slot: (period: 282, thread: 22) at index: 0
    On chain execution
    Block id: hGKNZxZpFZSyzRiUndPTJckV6NDX3s3LDFzQHwZUyT48T8Drw
    Origin operation id: VJDjWebiDWvMizQpDUe1hRnqjFym1QQp3n8L5Z36DBy41W5L3
    Call stack: A12LempfubRfRZoRh7Dr4nWyTUYKAQs7Be1cZ9Gejcx7NXYG4HXD,A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB

    Data: hello

In this example the event message is `hello`.
