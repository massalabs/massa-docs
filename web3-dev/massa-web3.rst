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

For the decentralized website part, we'll assume that you have some familiarity
with HTML and JavaScript. If you want to have more details, you can follow
`this great tutorial from React <https://reactjs.org/tutorial/tutorial.html>`_
from which the dApp is inspired from.

Here you can find an example dApp for the :ref:`sum smart-contract <sc-example-sum>`: 
`Massa DApp example: Sum <https://github.com/massalabs/massa-sc-examples/tree/main/sum/frontend>`_.

Prerequisites:
  * having `Thyra <https://github.com/massalabs/thyra/>`_ installed and running on your computer
  * having a wallet in Thyra
  * the wallet needs to own some Massa coins
  * having a smart contract :ref:`deployed <sending-sc-sum>`

**Network**: as any blockchain is a network of nodes, there could be practically several
networks of different nodes. The main Massa network is called the MainNet. There is also a TestNet which is 
ran by Massa Labs team, where you can build applications without paying real Massa
coins. You can also run your own network, with only one node
(see :ref:`local-network-generation`).

A decentralized application is an application running on a blockchain. Even the
frontend can be hosted on the blockchain with
:ref:`web-on-chain <web3-decentralized-web>`.

To build such application you will need to use some libraries:
  * `massa toolkit <https://github.com/massalabs/massa-sc-toolkit/>`_
  * `massa assemblyscript sdk <https://github.com/massalabs/massa-as-sdk/>`_
  * `massa javascript web3 <https://github.com/massalabs/massa-web3/>`_

Then you will be able to build a frontend application that communicates with
Thyra's API to sign transactions and submit it to the blockchain. 

Then, use Thyra to deploy your frontend application.

..
  comment: TODO: add link to the page explaining how to deploy a website

Debugging
#########

This section will give you some tips on how to debug your dapp while building it.

Work in a local development environment
---------------------------------------

Run :ref:`a local node <local-network-generation>`.

Start Thyra and bind it to your local node: 

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
coins, so it must appear in `massa-node/base_config/initial_ledger.json`
before running the node with `cd massa-node && cargo run --features sandbox`
in `massa <https://github.com/massalabs/massa>`_ directory.

Modify the file `massa/massa-client/base_config/config.toml` to the localhost RPC:

.. code-block::

    ip = "127.0.0.1"
    private_port = 33034
    public_port = 33035

Start a client with the command `cargo run` in `massa/massa-client` directory.

You might want to sign transactions to call smart contracts in your frontend
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
that will call the function named `hello` of the smart contract located at
`A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB` with the wallet name
`wallet`.

This opens a password prompt and you will be able to see the response of
the call in the development tools of the browser. The response is the operation
id, for example `2mLLkdKB4BY4hJQFNwGZ5oQVhky13EVZAwHJXCGQnd3FRHUoHw`.

If the operation failed, the response will contain the details.

Now you can now use commands of the node client to see the details of your
operation:

.. code-block::

    get_operations 2mLLkdKB4BY4hJQFNwGZ5oQVhky13EVZAwHJXCGQnd3FRHUoHw

This will output something like 

.. code-block:: text

    Operation 2oCTeeYMUt6hQuGAzCWbGBajXPPhz48VfHaEMWd6MJkVywa7Sa[ (in pool)]
    In blocks:
      - 2Fvqyzd7jfYjPxS9t9UCEGoHhhuvD8HHAmD45CNzEnFp4bbFT5
    Signature: ELrvQeuso9pjk5ErcG4WjESDpV8iFdweB45jic2UErSugSLLinbqx6JeKamxfXRZtBQoZi9ZcbvsGK858yx2FPsUoHrJJ
    Creator pubkey: P12fnkn6fMhjZHatWsQ3k1L4B7hb99XdjbWCmVthxCwbisRAMk2P
    Creator address: A12LempfubRfRZoRh7Dr4nWyTUYKAQs7Be1cZ9Gejcx7NXYG4HXD
    Id: 2oCTeeYMUt6hQuGAzCWbGBajXPPhz48VfHaEMWd6MJkVywa7Sa
    Fee: 0
    Expire period: 2314
    Operation type: CallSC:
      - target address:A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB
      - target function:sum
      - target parameter:
      - max_gas:700000000
      - coins:0

.. note::
    `target parameter` is empty because parameters are encoded into bytes so it is
    unlikely to have printable characters. 

You can also see events emitted by your contract (assuming that your contract is
deployed at `A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB`):

.. code-block::

    get_filtered_sc_output_event caller_address=A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB

.. code-block::

    Context: Slot: (period: 2238, thread: 22) at index: 0
    On chain execution
    Block id: 7ALxLxsJfLMMTYRLcGA42bXGbDKSiTPYWyoGWupKMJxXYNHmb
    Origin operation id: 24kaBAdcCHUsgcfCUBxUXNBquJyz91Whon1xwbWmux1j29MEC
    Call stack: A12LempfubRfRZoRh7Dr4nWyTUYKAQs7Be1cZ9Gejcx7NXYG4HXD,A1nyzu9rJKnf2zz8F7mkM5d6ZoThnMuAtUdij9WmcwXWMWJFmEB

    Data: Sum (2, 3) = 5

In this example the event message is `Sum (2, 3) = 5`.
