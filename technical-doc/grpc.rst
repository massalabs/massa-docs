.. index:: api, gRPC,Postman

.. _technical-grpc:

Massa GRPC API
==============

Through the API documentation, you can find out how to make calls and handle responses.

- **gRPC API**: used for blockchain interactions. Default port: 33037 e.g. grpc://localhost:33037

Find the complete Massa `gRPC <https://grpc.io/>`_ specification `here
<https://github.com/massalabs/massa/blob/main/massa-proto/proto/massa/api/v1/api.proto>`_.

.. note::

   We provide the generated `API documentation
   <https://htmlpreview.github.io/?https://github.com/massalabs/massa/blob/main/massa-proto/doc/index.html>`_
   which documents every available methods in gRPC Massa service.

.. warning::

    Massa gRPC support is experimental. This API is not yet stable and may change in the future.

To enable WebSocket support in Massa node, edit file ``massa-node/config/config.toml`` (create it if absent) with the
following contents:

.. code-block:: toml

    [api]
       # whether to broadcast for blocks, endorsement and operations
       enable_broadcast = true
    [grpc]
        # whether to enable gRPC
        enabled = true

The complete gRPC configuration is available in `node configuration
<https://docs.massa.net/en/latest/testnet/all-config.html#node-configuration>`_ .

Integrations
------------

**Postman**: is a tool for software developers to create, test and debug API requests. More details can be found in
`Postman learning center <https://learning.postman.com/docs/getting-started/introduction/>`_.
Find all maintained Massa Postman collections in our official `workspace <https://www.postman.com/massalabs>`_.

**Code generation**: See developer `documentation <https://github.com/massalabs/massa/blob/main/massa-grpc/README.md>`_.

Error codes
-----------

When a call to Massa grpc API fails, it **MUST** return a valid gRPC `status
<https://grpc.github.io/grpc/core/md_doc_statuscodes.html>`_ .
