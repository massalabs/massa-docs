.. _web3-asc:

Autonomous Smart Contract
=========================

Massa smart contracts have the unique feature of being able to autonomously define when they will be active, without
external actors. By the end of this page, you will have a basic understanding of:

- How to use the sendMessage(...) ABI in order to make your smart contracts autonomous
- Provided autonomous smart contracts examples

Use sendMessage to automate your smart contracts
------------------------------------------------

As we've just seen, the core principal enabling the autonomous smart contracts feature, is the ability to emit a special
message which has the effect of executing a desired function call at a specified window in time, and (optionally) when
other specified conditions are met.

The ABI method `sendMessage(...)` is the key to emitting such a message, and the provided parameters define its
configuration:

- `at`: Address of the smart-contract that you want to wakeup(can be yourself)
- `functionName`: Function of the smart-contract to call
- `validityStartPeriod` and `validityStartThread`: First slot where your message could be executed
- `validityEndPeriod` and `validityEndThread`: Last slot where you message can be executed
- `maxGas`: Maximum amount of gas for the execution of the function referenced in the message
- `rawFee`: A fee to gain prority in the async pool
- `coins`: Coins that will be passed to the smart contract called in the message
- `data`: Parameter of the function called in the message
- `filterAddress`: An optional address that you can define if you want your function to be executed in your validity
  period BUT only after a modification in the data of the address (balance, bytecode, datastore)
- `filterKey`: An optional datastore key that will execute the message only if there is changes on this datastore key of
  the `filterAddress`

Examples
--------

You can find an example of autonomous smart contracts in `this ticker example
<https://github.com/massalabs/massa-sc-examples/tree/main/ticker>`_, where `sendMessage(...)` is used in
`this function <https://github.com/massalabs/massa-sc-examples/blob/main/ticker/assembly/contracts/oracle.ts#L25>`_.
