.. index:: web; decentralized, decentralized web

#########################
Massa's decentralized web
#########################

.. _decentralized-web:

Hosting your website on Massa's decentralized web
=================================================

Massa's decentralized web allows you to store websites directly on the blockchain. This feature provides another layer of security for your dApps.

This documentation go through all the steps to host your website on the blockchain and register
it on Massa's DNS service in order to access it using Massa's plugin.

To upload a website to Massa's decentralized web you can use the following smart-contract.
If you want latter want to be able to access to your website through the Massa plugin it's important to use the entry `massa_web` for your website.

.. code-block:: typescript

    import { include_base64, print, Storage } from "massa-sc-std";

    function createWebsite(): void {
        const bytes = include_base64('./site.zip');
        Storage.set_data("massa_web", bytes);
    }

    export function main(_args: string): i32 {
        createWebsite();
        print("Uploaded site");
        return 0;
    }

This smart-contract and the dependencies are available `here <https://github.com/massalabs/massa-sc-examples/tree/main/website>`_.

Setting the DNS
===============

To claim a DNS address for you smart-contract you can use the following smart-contract:

.. code-block:: typescript

    import { call } from "massa-sc-std";
    import { JSON } from "json-as";

    @json
    export class SetResolverArgs {
        name: string = "";
        address: string = "";
    }

    export function main(_args: string): i32 {
        const sc_address = "2cVNfo79K173ddPwNezMi8WzvapMFojP7H7V4reCU2dk6QTeA"
        call(sc_address, "setResolver", JSON.stringify<SetResolverArgs>({name: "flappy", address: "Gr2aeZt7ZRb9S5SKgAEV1tZ6ERLHGhBCZsAp2sdB6i3rDK9M7"}), 0);
        return 0;
    }

For now DNS addresses can only be claimed using the following address: `9mvJfA4761u1qT8QwSWcJ4gTDaFP5iSgjQzKMaqTbrWCFo1QM` (and its associated secret key).

Once you've done this step, you should be able to access to your website using
`Massa's browser plugin <https://github.com/massalabs/massa-wallet>`_ at `https://flappy.massa`.

.. index:: decentralized web

.. _web-plugin:

Massa's browser plugin
======================

In order to access decentralized websites we've developed a `browser plugin <https://github.com/massalabs/massa-wallet>`_.

The Massa browser plugin function like Metamask (wallet, interaction with the blockchain) but also provide extra functions:

* when an URL of the form `xxxxxxxxxxxx.massa` is typed in the address bar, the plugin will check if `xxxxxxxxxxxx` is an address
* if it's an address, the plugin will try to load the `website.zip` file from the filestore of that address, unzip it,
  and display its index.html (or whatever other page is requested).
* if it's not and address but something like a domain name, the plugin will interrogate a "Massa Name Service"
  smart contract through a readonly call to try to match the domain name to an address.
  This is inspired by how the Ethereum Name Service works. Then it will load the address's website as defined above.
* the website will typically contain html, css and javascript to remain lightweight.
  The javascript can dynamically talk to the Massa plugin to interact with the blockchain and wallet (just like Metamask)
* if the website needs heavy assets (videos, large images...) that cannot be stored on the blockchain,
  the plugin allows looking up optional external data on IPFS or on the normal web,
  but plugin settings allow users to disable the fetching of off-chain assets for maximum security.

Installation
------------

For now the Massa browser plugin is only compatible with Chrome and Firefox.

To install it, you can follow this installation procedure detailed on the
`Github repository <https://github.com/massalabs/massa-wallet>`_.
