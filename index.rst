Massa: The Decentralized and Scaled Blockchain
==============================================

Massa is a truly decentralized blockchain controlled by thousands of
people. With the breakthrough multithreaded technology, we're set for
mass adoption.

Introduction
------------

`Massa <https://massa.net>`_ is a new blockchain reaching a high
transaction throughput in a decentralized network. Our research is
published in this `technical paper <https://arxiv.org/pdf/1803.09029>`_.
It shows that throughput of 10'000 transactions per second is reached
even in a fully decentralized network with thousands of nodes.

An easy-to-read blog post introduction with videos is written
`here <https://massa.net/blog/post/0/>`_.

General documentation
---------------------

A general overview of the global architecture of a Massa Node is given
`here <general-introduction>`.

Massa introduce several new features that allows you to add a layer of security
to your decentralized applications:

- :ref:`Massa's decentralized web <decentralized-web>` allow to truly secure decentralized application by storing your dApps directly on the Massa blockchain.
- :ref:`Massa's autonomous Smart Contracts <asc-intro>` add a layer of security and reliability to your decentralized applications by allowing your smart contracts to wake up by themselves and perform arbitry operations.

Testnet
-------

As decentralization is our core value, we would like to help you start
and run a Massa node on our testnet. You'll find a full tutorial on how
to install and stake with your node on our testnet :ref:`here <testnet-install>`.

Web3 developers
---------------

If you want to build on the Massa blockchain we recommend the following ressources:

- :ref:`Massa's smart-contracts <sc-introduction>`, will get you through the various steps needed to compile and send smart-contracts to the Massa blockchain.
- :ref:`Massa's decentralized web <decentralized-web>` will show you how the Massa blockchain can be used to host decentralized websites.
- :ref:`Types <sc-types>` is a collection of useful types for smart-contracts.

Technical ressources
--------------------

You'll find :ref:`here <technical-api>` the documentation of the various JSON-RPC API endpoints exposed by a Massa node.

Community
---------

If you have any question regarding the project or technical questions, feel
free to reach us on our community channels:

- `Telegram <https://t.me/massanetwork>`_
- `Twitter <https://twitter.com/MassaLabs>`_
- `Discord <https://discord.com/invite/massa>`_

Contents
========

.. toctree::
   :maxdepth: 2
   :caption: General documentation

   general-doc/architecture.rst
   general-doc/decentralized-web.rst
   general-doc/autonomous-sc.rst

.. toctree::
   :maxdepth: 2
   :caption: Testnet documentation

   testnet/install.rst
   testnet/running.rst
   testnet/update.rst
   testnet/wallet.rst
   testnet/staking.rst
   testnet/routability.rst
   testnet/rewards.rst
   testnet/faq.rst
   testnet/community-resources.rst

.. toctree::
   :maxdepth: 2
   :caption: Web3 developers

   web3-dev/smart-contracts.rst
   web3-dev/decentralized-web.rst
   web3-dev/types.rst
   web3-dev/dummy-network-generation.rst
   web3-dev/external-resources.rst

.. toctree::
   :maxdepth: 2
   :caption: Technical documentation

   technical-doc/storage-costs.rst
   technical-doc/api.rst
   technical-doc/glossary.rst
   technical-doc/concurrency.rst
   technical-doc/Endorsements.rst
   technical-doc/vm-ledger-interaction.rst

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
:ref:`Keyword Index <genindex>`, :ref:`Search Page <search>`
