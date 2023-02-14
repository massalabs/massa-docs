.. role:: raw-html(raw)
    :format: html

Introduction
============

We will describe in this document the global architecture of a Massa Node, from the ground up, and introduce relevant
definitions and concepts.

The goal of the Massa network is to build a consensus between **nodes** to gather and order **blocks** that contain
ordered lists of **operations**.
An operation ultimate purpose once executed is to act as transitions for the global network state,
called the **ledger**.

Operations are produced by external clients and sent to the Massa network via a node.
Some operations are containing code to be run as **smart contracts**, enabling complex programmatic modifications
of the ledger.
Nodes will gather all the pending operations and group them to produce blocks.
Each block contains a finite set of operations, limited by the fact that each block has a limited amount of space
available to store operations.
Traditional blockchains will then typically link blocks one after the other (including a hash of the previous block
in the block header), to materialize their temporal ordering. However, unlike traditional blockchains, Massa blocks
are not simply chained one after the other, but organized into a more complex spatio-temporal structure, which allows
for parallelization and increased performances.

Instead of one chain, there are several threads (T=32) of chains running in parallel, with blocks equally spread on
each thread over time, and stored inside **slots** that are spaced at fixed time intervals:

.. image:: structure.drawio.svg

The time between two slots located on the same thread is called a **period** and lasts 16s (conventionally called
:math:`t_0`).
Corresponding slots in threads are slightly shifted in time relative to one another, by one period divided by the
number of threads, which is 16s/32 = 0.5s, so that a period contains exactly 32 slots equally spaced over the 32
threads.
A **cycle** is defined as the succession of 128 periods and so lasts a bit more than 34min.
Periods are numbered by increments of one, so can be used together with a thread number to uniquely identify a block
slot.
Period 0 is the genesis and contains genesis blocks with no parents.

The job of the Massa nodes network is to essentially collectively fill up slots with valid blocks.
To do so, at each interval of 0.5s, a specific node in the network is elected to be allowed to create a block
(more about the selection process below, and the proof of stake sybil resistance mechanism),
and will be rewarded if it creates a valid block in time. It is also possible that a node misses its opportunity to
create the block, in which case the slot will remain empty (this is called a **block miss**).

In traditional blockchains, blocks are simply referencing their unique parent, forming a chain.
In the case of Massa, each block is referencing one parent block in each thread (so, 32 parents).
Here is an example illustrated with one particular block:

.. image:: block_parents.drawio.svg

Let's introduce some relevant definitions and concepts generally necessary to understand how the Massa network
operates.
We will then explain the node architecture and how the whole system works.

Address
*******

Each account in Massa has a public and private key associated with it. This is how messages can be signed
and identity enforced.

The address of an account is simply the hash of its public key.

Ledger
******

The ledger is a map that stores a global mapping between addresses and information related to these addresses.
It is replicated in each node and the consensus building mechanism ensures that agreement on what operations have been
finalized (and in what order) will be reached over the whole network. The ledger is the state of the Massa network,
and fundamentally operations (see below) are requests to modify the ledger.

The information stored in the ledger with each address is the following:

===============================  =========================================================
**Ledger information associated with each address**
------------------------------------------------------------------------------------------
``balance``                      The amount of Massa coins owned by the address
``bytecode``                     When the address references a smart contract, this is the compiled code
                                 :raw-html:`<br/>` corresponding to the smart contract (typically contains several
                                 functions that act as :raw-html:`<br/>` API entry points for the smart contract)
``datastore``                    A key/value map that can store any persistent data related to a smart
                                 :raw-html:`<br/>` contract, its variables, etc
===============================  =========================================================


Smart Contract
**************

Smart contracts are a piece of code that can be run inside the Massa virtual machine and which can modify the ledger,
accept incoming requests through a public interface (via smart contract operations).
One particularity of Massa smart contracts compared to other blockchain smart contracts
is their ability to wake up by themselves independently of an exterior request on their interface.
This allows more autonomy and less dependency on external centralized services.

Smart contracts are currently written in assemblyscript, a stricter derivation from typescript, which is itself a
type-safe version of javascript.
AssemblyScript compiles to web assembly bytecode (wasm). Massa nodes Execution Module runs such bytecode.
Smart contracts have access to their own datastore, so they can modify the ledger.

Operation
*********

Fundamentally, the point of the Massa network is to gather, order and execute operations, recorded inside blocks that
are located in slots.
There are three types of operations: transactions, roll operations, and smart contract code execution.
The general structure of an operation is the following, and the different types of operations differ by their payload:

===============================  =========================================================
**Operation header**
------------------------------------------------------------------------------------------
``creator_public_key``           The public key of the operation creator (32 bytes)
``expiration_period``            Period after which the operation is expired (u64 varint)
``fee``                          The amount of fees the creator is willing to pay (u64 varint)
``type``                         The type of operation (from 0 to 4: transaction, rollbuy,
                                 :raw-html:`<br/>` rollsell, executesc, callsc) (u64 varint)
``payload``                      The content of the operation (see below)
``signature``                    signature of all the above with the private key of
                                 :raw-html:`<br/>` the operation creator (64 bytes)
===============================  =========================================================

Transactions operations
^^^^^^^^^^^^^^^^^^^^^^^

Transactions are operations that move native Massa coins between addresses. Here is the corresponding payload:

===============================  =========================================================
**Transaction payload**
------------------------------------------------------------------------------------------
``amount``                       The amount of coins to transfer (u64 varint)
``destination_address``          The address of the recipient (32 bytes)
===============================  =========================================================

Buy/Sell Rolls operations
^^^^^^^^^^^^^^^^^^^^^^^^^

Rolls are staking tokens that participants can buy or sell with native coins (more about staking below).
This is done via special operations, with a simple payload:

===============================  =========================================================
**Roll buy/sell payload**
------------------------------------------------------------------------------------------
``nb_of_rolls``                  The number of rolls to buy or to sell (u64 varint)
===============================  =========================================================


Smart Contract operations
^^^^^^^^^^^^^^^^^^^^^^^^^

Smart Contracts are pieces of code that can be run inside the Massa virtual machine. There are two ways of calling for
the execution of code:

1. Direct execution of bytecode

In this case, the code is provided in the operation payload and executed directly:

===============================  =========================================================
**Execute SC payload**
------------------------------------------------------------------------------------------
``max_gas``                      The maximum gas spendable for this operation (u64 varint)
``bytecode_len``                 The length of the bytecode field (u64 varint)
``bytecode``                     The bytecode to run (in the context of the caller address)
``datastore_len``                The number of the datastore keys (u64 varint), each record
                                 :raw-html:`<br/>` is stored then one after the other after
list of datastore records        Concatenation of ``key_len`` (u8), ``key``,
                                 :raw-html:`<br/>` ``value_len`` (u64 varint), ``value``
===============================  =========================================================

1. Smart Contract function call

Here, the code is indirectly called via the call to an existing smart contract function, together with the required
parameters:

===============================  =========================================================
**Call SC**
------------------------------------------------------------------------------------------
``max_gas``                      The maximum gas spendable for this operation (u64 varint)
``coins``                        The coins transferred in the call (u64 varint)
``target_address``               The address of the targeted smart contract (32 bytes)
``function_name_length``         The length of the name of the function that is called (u8)
``function_name``                The name of the function that is called (utf8)
``param_len``                    The number of parameters of the function call (u64 varint)
``params``                       The parameters of the function call
===============================  =========================================================

Block
*****

A block is a data structure built by nodes and its function it to aggregate several operations.
As explained above, for each new slot that becomes active, a particular node in the network is elected in a
deterministic way with the task of creating the block that will be stored in that slot (more about this in the
description of the Selector Module below).
A block from a given thread can only contain operations originating from a `creator_public_key` whose hash's five first
bits designate the corresponding thread, thus implicitly avoiding collisions in operations integrated into parallel
threads.

The content of a block is as follows:

===============================  =========================================================
**Block header**
------------------------------------------------------------------------------------------
``slot``                         A description of the block slot, defined by a couple (period, thread) that
                                 :raw-html:`<br/>` uniquely identify it
``creator_public_key``           The public key of the block creator (32 bytes)
``parents``                      A list of the 32 parents of the block, one parent per thread (parent blocks are
                                 :raw-html:`<br/>` identified by the block hash)
``endorsements``                 A list of the 16 endorsements for the block (more about endorsements below)
``operations_hash``              A hash of all the operations included in the block (=hash of the block body below)
``signature``                    signature of all the above with the private key of
                                 the block creator
===============================  =========================================================

===============================  =========================================================
**Block body**
------------------------------------------------------------------------------------------
``operations``                   The list of all operations included in the block
===============================  =========================================================

Endorsements are optional inclusion in the block, but their inclusion is incentivized for block creators.
They are validations of the fact that the parent block on the thread of the block is the best parent that could have
been chosen, done by other nodes that have also been deterministically selected via the proof of stake probability
distribution (see below).
A comprehensive description of endorsements can be found
`here <https://docs.massa.net/en/latest/general-doc/architecture/endorsements.html>`_,
so we will not go further into details in the context of this introduction.
