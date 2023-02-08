.. _testnet-running:

==============
Running a node
==============

From binaries (simple installation)
===============================

Simply run the binaries you downloaded in the previous step:
Open the `massa-node` folder and run the `massa-node` executable
Open the `massa-client` folder and run the `massa-client` executable

On Ubuntu / MacOS
-----------------

**Configure the node**

Defaut configuration is available `here <https://github.com/massalabs/massa/blob/main/massa-node/base_config/config.toml>`_.

You can override the defaut configuration via `massa-node/config/config.toml` file.

**Start the node**

On a first window:

.. code-block:: bash

    cd massa/massa-node/

Launch the node, on Ubuntu:

.. code-block:: bash

    ./massa-node -p <PASSWORD> |& tee logs.txt

Replace <PASSWORD> with a password that you will need to keep to restart your node
You should leave the window opened.

**Start the client**

On a second window:

.. code-block:: bash

    cd massa/massa-client/

Then:

.. code-block:: bash

    ./massa-client -p <PASSWORD>

Replace <PASSWORD> with a password that you will need to keep to restart your client

From source code (for advanced users)
=====================================

On Ubuntu / MacOS
-----------------

**Start the node**

On a first window:

.. code-block:: bash

    cd massa/massa-node/

Launch the node, on Ubuntu:

.. code-block:: bash

    RUST_BACKTRACE=full cargo run --release -- -p <PASSWORD> |& tee logs.txt

Replace <PASSWORD> with a password that you will need to keep to restart your node

**Or,** on macOS:

.. code-block:: bash

    RUST_BACKTRACE=full cargo run --release -- -p <PASSWORD> > logs.txt 2>&1

Replace <PASSWORD> with a password that you will need to keep to restart your node
You should leave the window opened.

**Start the client**

On a second window:

.. code-block:: bash

    cd massa/massa-client/

Then:

.. code-block:: bash

    cargo run --release -- -p <PASSWORD>

Replace <PASSWORD> with a password that you will need to keep to restart your client
Please wait until the directories are built before moving to the next step.

On Windows
----------

**Start the Node**

- Open Windows Power Shell or Command Prompt on a first window
    - Type: :code:`cd massa`
    - Type: :code:`cd massa-node`
    - Type: :code:`cargo run --release -- -p <PASSWORD>`

Replace <PASSWORD> with a password that you will need to keep to restart your node
You should leave the window opened.

**Start the Client**

- Open Windows Power Shell or Command Prompt on a second window
    - Type: :code:`cd massa`
    - Type: :code:`cd massa-client`
    - Type: :code:`cargo run --release -- -p <PASSWORD>`

Replace <PASSWORD> with a password that you will need to keep to restart your client
Please wait until the directories are built before moving to the next step.

.. warning::
    In case of crash of the rust compiler or at runtime, please do not report bugs to the rustlang/rust repository, but open an issue on massa instead.
    We will triage the issues and open them on the rust side if they are valid. This avoids polluting the main rust repository with many reports of the same error.
