.. index:: api, gRPC,Postman

.. _technical-grpc:

Massa GRPC API
==============

Through the API documentation, you can find out how to make calls and handle responses.

- **gRPC Public API**: used for blockchain interactions. Default port: 33037 e.g. grpc://localhost:33037

Find the complete Massa `gRPC <https://grpc.io/>`_ specification `here
<https://github.com/massalabs/massa-proto/blob/main/proto/apis/massa/api/v1/api.proto>`_.

.. note::

   We provide the generated `API
   <https://htmlpreview.github.io/?https://github.com/massalabs/massa-proto/blob/main/doc/api.html>`_ and
   `Commons <https://htmlpreview.github.io/?https://github.com/massalabs/massa-proto/blob/main/doc/commons.html>`_ documentation
   which documents every available message and method in gRPC Massa services.

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

- **Postman**: is a tool for software developers to create, test and debug API requests.
  More details can be found in
  `Postman learning center <https://learning.postman.com/docs/getting-started/introduction/>`_.
  Find all maintained Massa Postman collections in our official `workspace <https://www.postman.com/massalabs>`_.

- **Massa proto rs**: is a Rust library which contains generated code from Massa protobuf files.
  See `massa-proto-rs <https://github.com/massalabs/massa-proto-rs>`_.

Code generation
---------------

**Step 1: Clone `massa-proto` repository**

.. code-block:: shell

    git clone https://github.com/massalabs/massa-proto.git

.. note::
    If you are using an older version of Massa node, please use the corresponding commit hash to your node version.

**Step 2: Install Buf CLI**

1. Download the latest version of Buf CLI from the `official website <https://docs.buf.build/installation>`_.
2. Extract the downloaded file to a folder on your computer.
3. Add the buf binary to your system PATH environment variable.

**Step 3: Set up Buf**

1. Add the following content to ``buf.work.yml``:

.. code-block:: yaml

    version: v1
    directories:
        - proto/apis
        - proto/commons
        - proto/third_party

By specifying the directories in the configuration file, Buf knows which `.proto` files to include in the build process.

1. Create a file called ``buf.gen.yml`` and add the following content:

.. code-block:: yaml

    version: v1
    managed:
      enabled: true
    plugins:
      - remote: buf.build/community/timostamm-protobuf-ts:v2.9.0
        out: gen/ts

The configuration above tells Buf to use the official
`timostamm-protobuf-ts <https://buf.build/community/timostamm-protobuf-ts>`_
plugin to generate gRPC client and classes in TypeScript.

.. note::
    The complete list of official Buf `plugins <https://buf.build/plugins>`_.
    For each language, there are multiple plugins available. Please refer to the documentation of each plugin if it's not supported.

**Step 4: Generate gRPC client/classes in TypeScript**

1. Launch the code generation:

.. code-block:: yaml

    buf generate

2. Install the required dependencies for the generated code to your project:

.. code-block:: shell

    npm install @protobuf-ts/runtime@^2.9.0 @protobuf-ts/runtime-rpc@^2.9.0
