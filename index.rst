Massa: The Decentralized and Scaled Blockchain
==============================================

Massa is a truly decentralized blockchain controlled by thousands of people. With the breakthrough multithreaded
technology, we’re set for mass adoption.

Introduction
------------

`Massa <https://massa.net>`_ is a new blockchain reaching a high transaction throughput in a decentralized network. Our
research is published in this `technical paper <https://arxiv.org/pdf/1803.09029>`_. It shows that throughput of 10’000
transactions per second is reached even in a fully decentralized network with thousands of nodes.

An easy-to-read blog post introduction with videos is written `here <https://massa.net/blog/post/0/>`_.

General documentation
---------------------

A general overview of the architecture of a Massa node is given :ref:`here <architecture-general-introduction>`.

Massa introduces several new features that enables new applications and adds a layer of security to your decentralized
applications:

- :ref:`Massa's decentralized web <general-decentralized-web>` allows you to truly secure decentralized applications by
  storing your dApps directly on Massa blockchain.
- :ref:`Massa's autonomous Smart Contracts <general-asc>` add a layer of security and reliability to your decentralized
  applications, by allowing your smart contracts to `wake up by themselves and perform arbitrary operations
  <https://massa.net/autonomous-sc>`_.

Web3 developers
---------------

If you want to build on the Massa blockchain, we recommend the following ressources:

- :ref:`Massa's smart-contracts <web3-dev-sc>` will get you through the various steps needed to compile and send
  smart-contracts to the Massa blockchain.
- :ref:`Massa's decentralized web <general-decentralized-web>` will show you how Massa blockchain can be used to host
  decentralized websites.
- :ref:`massa-web3 <web3-massa-web3>` is a collection of useful resources for the frontend development of your
  decentralized application.
- :ref:`Local network generation <local-network-generation>` will get you through all the steps required to launch a
  local Massa network.
- :ref:`Useful resources <web3-useful-resources>` is a collection of useful resources for smart-contract development.
- :ref:`External resources <web3-external-resources>` is a collection of useful resources developed by external
  contributors.

Technical resources
-------------------

:ref:`Here <technical-api>` you’ll find the documentation on the various JSON-RPC API endpoints exposed by a Massa node.

Testnet
-------

As decentralization is our core value, we would like to help you start running a Massa node on our testnet. You’ll find
a full tutorial on how to install and stake with your node on our testnet :ref:`here <testnet-install>`.

Community
---------

If you have any questions regarding the project or wish to discuss technical aspects in more depth, feel free to
reachout to us in our community channels:

- `Telegram <https://t.me/massanetwork>`_
- `Twitter <https://twitter.com/MassaLabs>`_
- `Discord <https://discord.com/invite/massa>`_

Contents
========

.. toctree::
    :maxdepth: 2
    :caption: General documentation

    general-doc/architecture.rst
    general-doc/thyra.rst
    general-doc/autonomous-sc.rst

.. toctree::
    :maxdepth: 2
    :caption: Web3 developers

    web3-dev/smart-contracts.rst
    web3-dev/massa-web3.rst
    web3-dev/dummy-network-generation.rst
    web3-dev/useful-resources.rst
    web3-dev/external-resources.rst

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
    testnet/all-config.rst
    testnet/faq.rst
    testnet/community-resources.rst

.. toctree::
    :maxdepth: 2
    :caption: Technical documentation

    technical-doc/bootstrap.rst
    technical-doc/storage-costs.rst
    technical-doc/api.rst
    technical-doc/glossary.rst

.. toctree::
    :maxdepth: 1
    :caption: Contributing

    contributing.rst

- :ref:`genindex`
- :ref:`modindex`
- :ref:`search`

:ref:`Keyword Index <genindex>`, :ref:`Search Page <search>`
