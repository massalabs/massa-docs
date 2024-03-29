Bootstrapping in Massa
======================

Introduction
------------

Nodes that are already part of the network are able to follow the State by observing the blocks passing through the
network, verifying them, and applying the state changes they cause.

However, new nodes joining the network need to get an absolute "current" version of the state, which is called
"bootstrapping". In some blockchains like Bitcoin, full nodes joining the network are recommended to download all blocks
from the beginning (genesis) of the blockchain in order to re-verify the whole state change history.

However, Massa has a triple decentralization/security/performance goal:
    - maximal decentralization requires that node hardware requirements stay consistent with a typical consumer desktop
      computer to lower the entry barrier of becoming a node runner
    - maximal security requires that all nodes verify all blocks and operations
    - maximal performance requires using the node hardware to its fullest (CPU, network, memory, storage)

This means that the State in Massa evolves almost as fast as typical consumer desktop computers can run blocks, which
implies that catching up blocks since genesis goes only slightly faster than new blocks appear in the meantime, and
would take a very long time. Moreover, Massa aims at processing thousands of operations per second, which means that it
produces a lot of block data every second, thus preventing nodes with the target hardware from storing the full block
history and making bootstrapping from genesis impossible since old blocks are forgotten.

Massa nodes joining the network must therefore bootstrap by downloading the absolute current State.

Note that nodes can recover from short-term disconnects by asking for missing data from nodes around them once they come
back in the network. However, since Massa nodes only store a short history of blocks and forget older ones, it is
impossible to recover from long disconnects since the surrounding nodes have forgotten the blocks needed by the
recovering node. In that case, a new State bootstrap is required.

Security model
--------------

The example of Bitcoin
~~~~~~~~~~~~~~~~~~~~~~

To understand the security model of node bootstrap, Bitcoin is a good starting example.

When Bitcoin node runners decide to join the network, they first download the node software from a central source (eg.
bitcoin.org). If that source is compromised, the node might end up on a different network, and/or private key theft
might happen. Bitcoin therefore requires trust in the entity sourcing the node software.

Note that optionally, if the nodes don't wish to download the full block history they resort to trusting a more recent
"checkpoint" state encoded in the node software. If that state is compromised, the node's knowledge of the whole ledger
might be skewed.

Assuming the node software is not corrupted, new nodes joining the network need an initial list of peer nodes to which
they need to connect first in order to discover the network. This list is hardcoded in the node software (see `the
bitcoin docs <https://developer.bitcoin.org/devguide/p2p_network.html#peer-discovery>`_). If all peers pointed by this
initial list are compromised, the node can end on a different (non-bitcoin) network, even if the node software itself is
not compromised. Bitcoin therefore also requires trust in at least one of the initial peers.

Note that ending up on the wrong network can be detected by checking block hashes with an external source. But this
requires trusting yet another source of data.

The case of Massa
~~~~~~~~~~~~~~~~~

The Massa case is very similar to Bitcoin's. Node runners also need to trust the source of the node software they
download, as well as the initial list of peers.

Similarly to Bitcoin checkpoints, bootstrapping Massa nodes must obtain the current State from a trusted source, ideally
the same source as the one they downloaded the node software from, in order to avoid having to trust multiple entities.

Downloading the state from an untrusted source can result in major issues such as coin theft. As such, bootstrapping
from untrusted sources should be discouraged, and bootstrapping other nodes should be opt-in for node runners to avoid
"bootstrap lists" circulating as the default way of bootstrapping from unaware node runners.

Implementation details
----------------------

Procedure from the point of view of the node being bootstrapped
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Massa nodes that bootstrap start by connecting to a randomly chosen node among the ones listed in
`massa-node/base_config/config.toml` (section `Bootstrap/bootstrap_list`).

The bootstrap process uses a separate port and protocol than the normal Massa peer communication.

All communications with the chosen bootstrap node are authenticated using the public key (node ID) of the bootstrap node
(in the `config.toml` file, section `Bootstrap/bootstrap_list`) to prevent man-in-the-middle attacks.

The node being bootstrapped then attempts to download the current State, as well as an initial list of peers from the
bootstrap node.

Once successfully bootstrapped, the node can then connect to peers, discover the rest of the network, and process live
incoming blocks to keep its state up to date.

In Massa, the hash of the state is used as part of the proof-of-stake seed, which is a safety mechanism against
malicious bootstrap nodes sending a compromised State. It ensures that nodes with an altered State eventually end up
isolated from the real network because their proof-of-stake draws differ which causes them to discard incoming honest
blocks. Note however that a PoS seed mismatch can take up to 2 cycles to be detected.

In case of bootstrap failure, the bootstrapping node retries with another randomly chosen bootstrap node after a delay.

Procedure from the point of view of the bootstrap node
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Massa nodes can bootstrap other nodes, with certain limitations because the procedure is heavy for the bootstrap node.

The bootstrap system listens on the address/port defined in `massa-node/base_config/config.toml` (section
`bootstrap/bind`). The node's bootstrap server can be disabled by removing the `bind` entry from the config file.

The Massa State is large (~1 terabyte in the worst case), and takes time to upload to bootstrapping nodes. During that
time, new changes to the state continue to appear, so new changes affecting already-uploaded parts need to be sent
on-the-fly.

By default, Massa nodes only allow a whitelist of IP addresses to bootstrap from them. This list is present in the
`massa-node/base_config/bootstrap_whitelist.json` file. This list is intended to prevent flooding attacks by attackers
pretending to be bootstrapping, and also makes it more difficult for node runners to bootstrap from untrusted sources.
If you wish to disable whitelisting and allow anyone to bootstrap from your node, simply delete the
`bootstrap_whitelist.json` file and restart your node.

A complementary `bootstrap_blacklist.json` (absent by default) can also be created alongside `bootstrap_whitelist.json`
(and following the same syntax) in order to explicitly prevent certain IP addresses from bootstrapping from the node.

Once a node has accepted to bootstrap an incoming node, it adds the incoming node's IP address to a local cache
preventing that IP from bootstrapping again for a time defined in `massa-node/base_config/config.toml` (section
`bootstrap/per_ip_min_interval`) by refusing subsequent connections from that IP during the config-defined delay. The
exclusion delay is not extended if the remote IP attempts new connections during the exclusion delay. The exclusion
delay is however applied if the bootstrap was accepted but failed for any reason. This aims at limiting the load on
individual bootstrap nodes, and spreading the load among bootstrap nodes.

The number of nodes simultaneously bootstrapping from the local node is limited (`massa-node/base_config/config.toml`
section `bootstrap/max_simultaneous_bootstraps`). Excess attempts are refused but do not trigger the exclusion delay
mechanism.

Future optimizations
--------------------

We plan to add the possibility to download bootstrap data from untrusted sources for load-balancing, but then check the
hash of the obtained state from trusted sources, and only fallback to downloading everything from trusted sources if
multiple bootstrap attempts from this hybrid approach fail.
