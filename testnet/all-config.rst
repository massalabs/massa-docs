.. index:: node, client, configuration

.. _testnet-all-config:

Node and client configuration
=============================

.. warning::

    Use with caution, overriding some configurations could lead to node instability and/or a complete desynchronization
    from Massa blockchain.

Node configuration
------------------

You can override the default configuration via `massa-node/config/config.toml` file.

.. literalinclude:: ../external/node/config.toml
    :language: TOML

Node bootstrap whitelist configuration
--------------------------------------

You can override the default configuration via `massa-node/config/bootstrap_whitelist.json` file.

.. literalinclude:: ../external/node/bootstrap_whitelist.json
    :language: JSON

Client configuration
--------------------

You can override the default configuration via `massa-client/config/config.toml` file.

.. literalinclude:: ../external/client/config.toml
    :language: TOML
