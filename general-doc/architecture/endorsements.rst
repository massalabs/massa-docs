Endorsements
============

Intro
-----

Massa uses the Proof-of-Stake selection mechanism with Nakamoto consensus. In that context, when there are multiple
cliques in close competition, we want all nodes to converge towards a single clique as fast as possible to minimize
finality time and maximize the quality of the consensus. To achieve this, we draw inspiration from Tezos and introduce
the concept of Endorsement.

Basic principle
---------------

Each block header has `E` ordered endorsement slots: each one can include an endorsement or not. Each endorsement
contains:

    - The slot `S` in which it is meant to be included. The endorsement can only be included in blocks of slot `S`.
    - The hash of the endorsed block. This is the hash of the latest blockclique block of thread `S.thread` according to
      the endorsement creator at the moment the endorsement was created.
    - The index of the endorsement slot within the header from `0` (included) to `E-1` (included). The endorsement can
      only be included at that endorsement slot index within the block eader.
    - The public key of the creator of the endorsement
    - The signature of all the previous fields with the private key of the creator of the endorsement

At every slot `S`, we use the existing Proof-of-Stake selection mechanism to not only draw the block creator for that
slot, but also `E` other stakers indexed from `0` (included) to `E-1` (included) that can create Endorsements meant to
be included in block headers of slot `S` at their respective endorsement slot index.

Conceptually, each endorsement meant to be included at a slot `S` can be seen as a single vote endorsing the parent in
thread `S.thread` that the endorsement creator would have chosen if they had to create a block at slot `S`.

The likelihood of the attacker getting lucky and being selected for `N` consecutive PoS draws to attack/censor the
system decays exponentially with `N`. With endorsements, we don't have to wait for `N` blocks to account for `N`
proof-of-stake draws to happen as `E+1` draws happen at every slot (1 for the block creator and `E` for endorsement
creators). In the consensus algorithm, we choose the clique of highest fitness as the blockclique. A block including `e`
endorsements out of the maximum `E` contributes a fitness `e + 1` to the cliques it belongs to. The fitness of a block
is therefore reflected by the number of PoS draws that were involved in creating it.

The net effect of this mechanism is to increase safety and convergence speed by allowing block producers to quickly
choose the best clique to extend according to the "votes" provided by the endorsements.

Structure of an endorsement
---------------------------

.. code-block:: rust

    pub struct Endorsement {
      /// slot in which the endorsement can be included
      pub slot: Slot,
      /// endorsement index inside the including block
      pub index: u32,
      /// Hash of endorsed block.
      /// This is the parent in thread `self.slot.thread` of the block in which the endorsement is included
      pub endorsed_block: BlockId,
    }

Note that the `WrappedEndorsement` structure includes the underlying `Endorsement` as well as the signature, and the
public key of the endorsement producer.

Within a block, endorsements are fully included inside the header.

A header is invalidated if:

- it contains strictly more than `E` endorsements
- at least one of the endorsements fails deserialization or signature verification
- at least one of the endorsements endorses a block different than the parent of the including block within its own
  thread
- any of the endorsements should not have been produced at that `(endorsement.slot, endorsement.index)` according to the
  selector
- there is strictly more than one endorsement with a given `endorsement.index`

Lifecycle of an endorsement
---------------------------

To produce endorsements for slot `S`, the Endorsement Factory wakes up at `timestamp(S) - t0/2` so that the previous
block of thread `S.thread` (the endorsed block) had the time to propagate, and so that the endorsement itself has the
time to propagate to be included in blocks of slot `S`. It then checks the endorsement producer draws for slot `S`. At
every slot, there are `E` endorsement producer draws, one for each endorsement index from `0` (included) to `E-1`
(included). The factory will attempt to create all the endorsements that need to be produced by keypairs its wallet
manages. To choose the block to endorse, the factory asks Consensus for the ID of latest blockclique (or final) block
`B` in thread `S.thread` that has a strictly lower period than `S.period`. Every created endorsement is then sent to the
Endorsement Pool for future inclusion in blocks, and to Protocol for propagation to other nodes.

In Protocol, endorsements can be received from other modules, in which case they are propagated. They can also be
received from other nodes, in which case they added to the Endorsement Pool and propagated. Endorsements are propagated
only to nodes that don't already know about them (including inside block headers).

The Endorsement Pool stores a finite number of endorsements that can potentially be included in future blocks created by
the node. Consensus notifies the Endorsement pool of newly finalized blocks, which allows the pool to eliminate
endorsements that can only be included in already-finalized slots and are therefore not useful anymore.

When the Block Factory produces a block and needs to fill its header with endorsements, it asks the Endorsement Pool for
the endorsements that can be included in the block's slot and that endorse the block's parent in its own thread.

Incentives and penalties
------------------------

There needs to be an incentive in:

- creating blocks that can be endorsed, and also avoid publishing them too late so that endorsers have the time to
  endorse them
- creating and propagating endorsements, also doing so not too early in order to endorse the most recent block, and not
  too late for subsequent blocks to be able to include the endorsement
- including endorsements in blocks being created, and also not publishing them too early to include as many endorsements
  as possible

To achieve this, we note `R` the total amount of coin revenue generated by the block: the sum of the per-block monetary
creation, and all operation fees. We then split `R` into `1+E` equal parts called `r = R/(1+E)`.

- `r` is given to the block creator to motivate block creation even if there are no endorsements available
- for each successfully included endorsement:

  - `r/3` is given to the block creator to motivate endorsement inclusion
  - `r/3` is given to the endorsement creator to motivate endorsement creation
  - `r/3` is given to the creator of the endorsed block to motivate the timely emission of endorsable blocks

Note that this split also massively increases the frequency at which stakers receive coins, which reduces the incentive
to create staking pools.

Choosing the value of `E`
-------------------------

The value of `E` has implication both in the safety and in the performance of the protocol. In terms of performance, the
greater the value of `E` is, the more ressources (bandwith, memory, computing power) is needed to generate, broadcast
and include endorsements, which could induce latency. One the other hand, the value of `E` has implications in the
safety of the protocol. The threat we are looking at here is the finality fork attack.

Finality fork attacks
~~~~~~~~~~~~~~~~~~~~~

A finality fork attack is when an attacker that controls a portion `beta` of the total stake, decides not to broadcast
the blocks he has been selected to produce, in order to construct an alternative and undisclosed clique. The attacker's
goal is to produce an attack clique that has a fitness greater than the honnest clique. If he manages to do so, the
attacker can wait until the finalization time of a block `b` belonging to the honest clique, to broadcast its attack
clique. Because the fitness of the attack clique is greater than the honest one, a portion of the network will consider
the attacker's clique to be the good version of the chain, while some other portion will have finalized block `b`. This
results in a network partion, where two or more portion of the network do not agree on the state of the ledger.

Security level
~~~~~~~~~~~~~~

The factors that influence the success probability of a finality fork attack are the number of endorsments per block
`E`, the portion of the total stake controlled by the attacker `beta` and a finality parameter `F`. The finality
parameter `F` respresents a number of descendant a block must have, before being finalized. The portion of the stake
`beta` is positively correlated with the success probability of an attack, while `F` and `E` are inversly correlated. We
do not have control over `beta`, thus we define our security level as a portion `beta` and a maximum success probability
of an attack. We chose `beta = 1/3` because it is the maximum proportion of Byzantine participants BFT based consensus
protocols assume. We chose `p_success <= 1e-11` because it corresponds to a period of time of around 10 000 years (given
one block every 0.5s). We must chose the minimal parameters `F` and `E` that match this security level.

Simulation results
~~~~~~~~~~~~~~~~~~

Our simulations results show that with `F = 64`, `E = 16` and given 32 thread and a portion `beta = 1/3` of the stake
controlled by the attacker, the probability of success of an attack is in the order of `1e-11`. You can find more
details in the [technical paper](https://arxiv.org/abs/1803.09029).

.. image:: simulation_result.png

Future features
---------------

There is still optimizations that we can perform on the endorsements and their workflow :

- Add more verifications when receiving an endorsement from an other node of the network to avoid storing/propagating
  invalid endorsements. `Issue <https://github.com/massalabs/massa/issues/3020>`__
- Use stored endorsements to choose the best parents. `Issue <https://github.com/massalabs/massa/issues/2976>`__
- Split blocks and endorsements productions to an other binary so that they can be run on different machines and be more
  customized. `Discussion <https://github.com/massalabs/massa/discussions/2895>`__
- To punish network and CPU overload attacks, a denunciations system will be implemented to point out the stakers that
  produces two different endorsements for the same `(slot, index)`. `Issue
  <https://github.com/massalabs/massa/issues/3022>`__
