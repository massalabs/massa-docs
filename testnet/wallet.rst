.. _wallet:

Creating a Massa wallet
=======================

A Massa wallet is a file that contains a list of your keypairs.

Like other blockchains, Massa uses elliptic curve cryptography for the security of your coins (with `ed25519`).

It means your secret key is your password allowing you to spend coins that were sent to your address (your address is
the hash of your public key).

Here is how to create a Massa wallet.

From the command line interface
-------------------------------

If your client is not running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Go to the client folder:

.. code-block:: bash

    cd massa/massa-client/

Start the interactive client and load a wallet file:

.. code-block:: bash

    cargo run

It will ask your wallet password in order to load `wallet.dat`. If the file does not exist, you will be asked to set a
password and it will be created.

If your client is running
~~~~~~~~~~~~~~~~~~~~~~~~~

Now you can either generate a new keypair (and associated address):

.. code-block::

    wallet_generate_secret_key

**Or, if you already have one from a previous wallet**, you can add manually an existing keypair:

.. code-block::

    wallet_add_secret_keys <SecretKey>

The list of addresses of your wallet can be accessed with:

.. code-block::

    wallet_info

Access public key(s) of addresse(s):

.. code-block::

    wallet_get_public_key <Address1> <Address2>

Access secret key(s) of addresse(s):

.. code-block::

    wallet_get_secret_key <Address1> <Address2>

From the graphical interface
----------------------------

If you don't plan to stake or use the command-line client, you can also create a wallet on the web interface: head to
the `explorer <https://test.massa.net>`_, in the wallet tab.

Click `Generate secret key` then `Add`.

This generates a new random keypair from your computer randomness which stays on your side, it is never transferred on
the network.

Now you can add more addresses or see the list of your addresses with their associated thread and balance.

Also, if you want to save this wallet and be able to restore it later, click `Save wallet`, and next time directly do
`Load wallet`.
