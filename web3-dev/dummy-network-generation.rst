.. _local-network-generation:

Local network generation
========================

Here, you will learn how to launch a local network with custom settings, initial coins and rolls distribution.

Clone massa:

.. code-block:: bash

    git clone git@github.com:massalabs/massa.git

Compile it with the sandbox feature enabled:

.. code-block:: bash

    cd massa && cargo build --release --features sandbox

Create a keypair in massa-client:

.. code-block:: bash

    cd massa-client && cargo run
    wallet_generate_secret_key

For the rest of the tutorial we will use theses abbreviations:

- `SECRETK`: The secret you just generated
- `PUBK`: The public key corresponding to SECRETK
- `ADDR`: The address corresponding to PUBK

Setup your node to use the secret you just generated as its public key and staking key:
    - modify or create the file `massa-node/config/node_privkey.key`:

    .. code-block:: bash

        {"secret_key":"SECRETK","public_key":"PUBK"}

    - modify the file `massa-node/base_config/initial_ledger.json`:

    .. code-block:: javascript

        {
            "ADDR": {
                "balance": "80000000",
                "datastore": {},
                "bytecode": []
            }
        }

    - CLEAR and modify the file `massa-node/base_config/initial_rolls.json`:

    .. code-block:: javascript

        {
            "ADDR": 100
        }

    - CLEAR content of `massa-node/base_config/initial_vesting.json`:

    .. code-block:: javascript

        {}

You can now launch your node:

    .. code-block:: bash

        cd massa-node && cargo run --features sandbox

On your client run the following command to add your secret key as staking key:

.. code-block:: bash

    cd massa-client && cargo run node_start_staking ADDR

The local network and your node will start after 10 seconds. Once it is started, you can interact with it using the CLI
client as you would with a testnet node.
