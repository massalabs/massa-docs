==============
Update a node
==============

If you use the binaries, simply download the latest binaries, and make sure you use the latest nightly version of rust.

Download the nightly version we use:

.. code-block:: bash

    rustup install nightly-2023-01-30

Use the right version:

.. code-block:: bash

    rustup default nightly-2023-01-30

Otherwise:

Make sure you you have the right git repository (especially since the change from GitLab to GitHub):

.. code-block:: bash

    cd massa/
    git stash
    git remote set-url origin https://github.com/massalabs/massa.git

Update Massa:

.. code-block:: bash

    git checkout testnet
    git pull

After updating, enter the command :code:`node_get_staking_addresses` in your client and make sure that it returns an address that has rolls according to :code:`wallet_info`.

-   If :code:`wallet_info` does not return any address, it means that you haven't backed up your wallet.dat correctly. Close the client, overwrite wallet.dat with your backup, launch the client again and try again. You can also create a new address by calling :code:`wallet_generate_secret_key`.

-   If you can't find an address in :code:`wallet_info` that has non-zero candidate, non-zero final and non-zero active rolls, please refer to the `staking tutorial <https://massa.readthedocs.io/en/latest/testnet/staking.html>`_ on getting rolls.

-   If :code:`node_get_staking_addresses` does not return any address or does not return an address that has active_rolls according to `wallet_info`, it means you haven't backed up staking_keys.json properly. Try stopping the node, overwriting staking_keys.json with your backup, and starting the node again to try again. You can also manually add a staking key by calling :code:`add_staking_keys` with the KEY matching the address that has active rolls in :code:`wallet_info` (warning: do not type the address or public key, only the SECRET KEY).
