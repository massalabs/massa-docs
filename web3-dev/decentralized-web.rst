.. index:: decentralized web, thyra

.. _web3-decentralized-web:

Massa's decentralized web
=========================

Massa’s decentralized web allows you to store websites directly on the blockchain. This feature enables a lot of
applications and provides :ref:`another layer of security to your dApps <general-decentralize-web>`.

Thyra
-----

In order to facilitate the process of hosting websites on the Massa blockchain, we have developed a client, *Thyra*,
that enables you to easily store websites on the Massa blockchain and browse them. This client acts as a proxy that
intercepts request to `.massa` websites, perfom the domain name resolution on chain and fetch the website from the Massa
blockchain, allowing you to seamlessly browse decentralized websites from your favorite browser.

This section introduces all steps to host your website on the blockchain and register it on Massa’s DNS service, in
order to access it using any traditional browser.

*Why this name: Thyra*
~~~~~~~~~~~~~~~~~~~~~~

As explained Thyra plays the role of a gateway to the Massa blockain. Massa being a coin minted in the city of `Massalia
<https://en.wikipedia.org/wiki/Massalia>`_, the ancient Marseille founded by the Greek, quite naturaly the name `θύρα
<https://en.wiktionary.org/wiki/%CE%B8%CF%8D%CF%81%CE%B1>`_ (entrance, front door in ancient greek) imposed itself. In
order to simplify and standardize its writing, we transformed it into Thyra, but its pronunciation `\tʰý.raː\
<https://www.youtube.com/watch?v=_0BQ7sSJMTw>`_ remained.

.. _general-introduction:

Install Thyra
~~~~~~~~~~~~~

Automatically
+++++++++++++

- `MacOS installation script
  <https://github.com/massalabs/thyra/blob/255c6136c6910d17cbb94ce06b1e908d8516713a/scripts/macos_install.sh>`_ or
  simply use this cmd on your terminal:

.. code-block:: bash

    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/massalabs/thyra/main/scripts/macos_install.sh)"

- `Windows installation script
  <https://github.com/massalabs/thyra/blob/255c6136c6910d17cbb94ce06b1e908d8516713a/scripts/thyra-installer.bat>`_

.. note::

    If you install Thyra with this script using an Ethernet connection, there is high level of chances that you will
    need to re-install it if you move to wifi. Sorry about that, we’re working on it!*

.. code-block:: bash

    curl -fsSL https://raw.githubusercontent.com/massalabs/thyra/main/scripts/thyra-installer.bat >> thyra-installer.bat
    thyra-installer.bat

- `Linux Ubuntu installation script <https://github.com/massalabs/thyra/blob/main/scripts/linux_install.sh>`_

.. code-block:: bash

    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/massalabs/thyra/main/scripts/linux_install.sh)"

Once the installation is complete you should be able to browse `.massa` websites directly from your browser.

Manually
++++++++

To install Thyra manually, follow the step-by-step process found `here
<https://github.com/massalabs/thyra/blob/main/INSTALLATION.md>`__.

Upload your website and setup an on-chain domain for your website
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Thyra web-interface allows you to add a new domain to our DNS smart-contract, import a website, deploy it to the
blockchain and link it to your domain in just a few clicks.

Go to this page and follow instructions: http://my.massa/thyra/websiteCreator/index.html

.. warning::

    Due to :ref:`storage costs <storage-cost>`, storing websites on the Massa blockchain requires coins. Make sure that
    the wallet you use to upload your website has enough coin (about 100 coins per MB).

Browse Massa's decentralized web
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To browse and explore websites and other applications stored on Massa blockchain, you now simply have to run Thyra using
the cmd below and access any `.massa` website.

.. code-block:: bash

    thyra-server

Or you can also search for one you like `here <http://my.massa/thyra/registry/index.html>`__.
