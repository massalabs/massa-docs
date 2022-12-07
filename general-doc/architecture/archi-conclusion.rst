Conclusion
==========

There are many more details and specific mechanisms that are not described in this short introduction, but it gives a
good overview of the architecture and should help to get inside the code of the Massa Node.

Topics that were not handled here include:

* operations fees (each operation provides a fee and block factories will tend to favor operations with the highest
  fees for inclusion in blocks first), and how they are shared between block producers and endorsers
* ledger size limitation and the cost of storage
* slashing and node banning
* execution stack within smart contracts and what permissions smart contracts have in terms of ledger read/write access,
  based on their address
* details about the opcodes of the Massa WASM virtual machine
* analysis of potential attacks, like multistaking (when a block producer produces several blocks in the same slot)

For further references and technical details, you can find more information in the
`whitepaper <https://arxiv.org/pdf/1803.09029.pdf>`_.
