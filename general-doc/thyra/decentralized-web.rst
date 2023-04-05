
Decentralized web
=================

Rationale
---------

The "*code is law*" rule is a cornerstone of DeFi. It states among other things that once successfully audited, a
program can remain trusted. This implies that the program of a successfully audited smart contract may never be
unexpectedly changed by an outsider (note that contracts that modify their own code during runtime cannot be trusted
this way in the first place, but this is visible in the code audit). Popular ETH smart contracts essentially follow that
rule.

However, most DeFi web3 apps such as `app.uniswap.org <https://app.uniswap.org/>`_ are typically used through an
otherwise normal website that talks to a browser plugin (typically `Metamask
<https://github.com/MetaMask/metamask-extension>`_) allowing the webpage to interact with the user's wallet and the
blockchain. The website that serves as an entry point to the dApp is neither decentralized nor immutable-once-audited.
This breaks the very foundation of blockchain security.

This attack vector has been well identified by exploiters and, as smart contracts become more robust exploiters are
increasingly targeting front ends. For instance, in 2022, one of the major DeFi protocol, `curve.finance
<https://curve.fi/>`_, was victim of a `DNS hijacking <https://rekt.news/curve-finance-rekt/>`_, with hackers being able
to stole as much as 575$ from users. As long as decentralized applications rely on web2 infrastuctures, these attacks
are bound to happen `regularly <https://twitter.com/LefterisJP/status/1540306236087877635>`_.

The goal of Massa's decentralized web is to allow users to store websites without using any centralised party in between
your client and the blockchain. Since the front-end is hosted on the blockchain, anyone can access it using a Massa
node. For that Massa has developed a client that acts as a gateway to the blockchain preventing you from using any
centralised servers effectively maximising your security with immutable and censorship resistant websites.

This client is called θύρα (thyra) which in ancient Greek means door, entrance.

Start your decentralised web3 journey now, and install `Thyra
<https://docs.massa.net/en/latest/web3-dev/decentralized-web.html>`_. It will enable you to store your own website or to
simply navigate Massa web3 content.

That way, Massa allows deploying fully decentralized code-is-law apps, as it was meant to be!