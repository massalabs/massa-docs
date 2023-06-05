Update a node
=============

**IF COME HERE TO UPDATE FROM TESTNET 22 TO TESTNET 23
PLEASE DO NOT FOLLOW THIS AND START FROM A FRESH INSTALL**

If you use the binaries, simply download the latest binaries.
Otherwise if you have built from source, follow the instructions below.

Make sure you have all the packages installed on your system. Maybe new one could have been added since you
last release refer to
`our documentation <https://docs.massa.net/en/latest/testnet/install.html#from-source-code-advanced-installation>`__
for the updated list.

Make sure you you have the right git repository:

.. code-block:: bash

    cd massa/
    git stash
    git remote set-url origin https://github.com/massalabs/massa.git

Update Massa:

.. code-block:: bash

    git checkout testnet
    git pull

After updating, enter the command ``node_get_staking_addresses`` in your client and make sure that it returns an address
that has rolls according to ``wallet_info``.

- If ``wallet_info`` does not return any address, it means that you haven't backed up your wallet.dat correctly. Close
  the client, overwrite wallet.dat with your backup, launch the client again and try again. You can also create a new
  address by calling ``wallet_generate_secret_key``.
- If you can't find an address in ``wallet_info`` that has non-zero candidate, non-zero final and non-zero active rolls,
  please refer to the `staking tutorial <https://docs.massa.net/en/latest/testnet/staking.html>`_ on getting rolls.
- If ``node_get_staking_addresses`` does not return any address or does not return an address that has active_rolls
  according to `wallet_info`, it means you haven't backed up staking_keys.json properly. Try stopping the node,
  overwriting staking_keys.json with your backup, and starting the node again to try again. You can also manually add a
  staking key by calling ``add_staking_keys`` with the KEY matching the address that has active rolls in ``wallet_info``
  (warning: do not type the address or public key, only the SECRET KEY).
