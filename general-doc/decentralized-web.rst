.. index:: web; decentralized, decentralized web

.. _general-decentralize-web:

#########################
Massa's Decentralized web
#########################

Rationale
=========

The "*code is law*" rule is a cornerstone of DeFi.

It states among other things that once successfully audited, a program can remain trusted. This implies that the
program of a successfully audited smart contract may never be unexpectedly changed by an outsider (note that contracts
that modify their own code during runtime cannot be trusted this way in the first place, but this is visible in the code
audit). Popular ETH smart contracts essentially follow that rule.

However, most DeFi web3 apps such as `app.uniswap.org <https://app.uniswap.org/>`_ are typically used through an
otherwise normal website that talks to a browser plugin (typically
`Metamask <https://github.com/MetaMask/metamask-extension>`_) allowing the webpage to interact with the user's wallet
and the blockchain. The website that serves as an entry point to the dApp is neither decentralized nor
immutable-once-audited. This breaks the very foundation of blockchain security. And that’s how you get into situations
of scandalous thefts in the DeFi world, like `this one
<https://www.theverge.com/2021/12/2/22814849/badgerdao-defi-120-million-hack-bitcoin-ethereum>`_.

The goal of Massa's decentralized web is to allow addresses on Massa to store not only a balance, bytecode and a
datastore, but also files, zip archive and websites without using any centralised party in between your client and the
blockchain. Any address, through bytecode execution, can initialize, read and write the "file" part into the datastore.
Files or websites, for instance, are complex objects built on top of native objects (eg: balance, bytecode...) to enable
users to do more while assuring full decentralisation. The reason why we don’t simply reuse the datastore for this,
outside of the risk of key collisions, is for easier auditing: if the code never writes into its own bytecode nor its
datastore after deployment, it is safe to assume that the stored website can’t change anymore. Which means that Massa
can host the files corresponding to the front-end of the decentralized applications. Since the front-end is hosted on
the blockchain, allowing anyone to access it using a Massa node. For that Massa has developed a client that acts as a
gateway to the blockchain preventing you from using any centralised servers but also maximising your security with
immutable and censorship resistant websites.

This client is called θύρα (thyra) which in ancient Greek means door, entrance.

Start a decentralised web3 journey now, and install
`Thyra <https://docs.massa.net/en/latest/web3-dev/decentralized-web.html>`_. It will enable you to store your own
website or to simply navigate Massa web3 content.

That way, Massa allows deploying fully decentralized code-is-law apps, as it was meant to be!
