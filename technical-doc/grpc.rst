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

To enable gRPC support in your Massa node, edit file ``massa-node/config/config.toml`` (or create it if absent) with the
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

Code generation
---------------

**Step 1: Download the `massa-proto` folder from GitHub**

1. Go to the GitHub repository for `massa-proto <https://github.com/massalabs/massa-proto>`_.
2. Click the green ``Code`` button and select ``Download ZIP`` to download the entire repository as a ZIP file.
3. Extract the ZIP file to a folder on your computer.
4. Navigate to the ``massa-proto`` folder, which contains the ``.proto`` files we'll be using to generate gRPC clients.


**Step 2: Install Buf CLI**

1. Download the latest version of Buf CLI from the `official website <https://docs.buf.build/installation>`_.
2. Extract the downloaded file to a folder on your computer.
3. Add the buf binary to your system PATH environment variable.

**Step 3: Set up Buf**

1. Create a new file called ``buf.work.yml``
2. Add the following content:

.. code-block:: yaml

    version: v1
    directories:
        - proto/massa/api/v1
        - proto/third-party

By specifying the directories in the configuration file, Buf knows which `.proto` files to include in the build process.

1. Create another file called buf.gen.yml in the massa-proto folder.
2. Add the following content to ``buf.gen.yml``:

.. code-block:: yaml

    version: v1
    managed:
      enabled: true
    plugins:
      - remote: buf.build/stephenh/plugins/ts-proto
        out: gen/ts
        opt:
          - outputServices=...
          - useExactTypes=...

This tells Buf to use the official ``ts-proto`` plugin to generate gRPC client in TypeScript.

.. note::
    The complete list of official Buf `plugins <https://buf.build/plugins>`_.

**Step 4: Generate gRPC client in TypeScript**

1. Open a command prompt or terminal window and navigate to the ``massa-proto`` folder.
2. Run the following command to generate the gRPC clients:

.. code-block:: yaml

    buf generate

This will generate the gRPC client in a new ``gen/ts`` folder in the ``massa-proto`` folder.

.. note::

    More information about gRPC in developer `documentation <https://github.com/massalabs/massa/blob/main/massa-grpc/README.md>`_.
