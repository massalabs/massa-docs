.. _autonomous-smart-contract:

Autonomous Smart Contract
=========================

Massa smart contracts have the unique feature of being able to autonomously define when they will be active, without external actors. By the end of this page, you will have a basic understanding of

- The mechanisms behind autonomous smart contracts
- How to use the sendMessage(...) ABI in order to make your smart contracts autonomous
- Provided autonomous smart contracts examples

Mechanism in Massa network
--------------------------

This system works by emitting messages which will schedule an execution. To do so, the following must be specified:

- The address of the SC to be executed
- The function to be executed
- The desired execution window
- (Optional) The activation triggers

Normally, when you submit an operation, the operation is sent in the operation pool and in the future included in a block to be executed: you pay the gas at execution time.
With autonomous SCs, the message is emitted inside of the operation and and you pay the gas for the message execution upfront. The message is then sent to another pool called the async pool.

This pool is deterministic by nature, as it's filled with information that comes from operations included in blocks which is processed by every node of the network.

The pool has a finite size, and sorts its messages with a similar behaviour as the operation pool: there is a priority, which is computed from the message emission fees, maximum execution gas, and length of the execution window. The messages get removed after a certain number of slots if they were not executed.

The number of messages that can be executed by each slot are limited by a maximum amount of gas like normal operations 
and so it's possible that your message isn't executed at the slot that you want (such as operations that could be not included in the first block after you sent it),
but in a later slot, when there is enough space to include your message. If you want to have your message to be included as soon as possible, 
you have to put more fees than the others (just like the operations).

The order of the messages is determined by the formula: 

`(Reverse(Ratio(msg.fee, max(msg.max_gas,1))), emission_slot, emission_index)`

where `emission_index` is an index that differentiate multiple messages created in the same slot.

Use sendMessage to add autonomy in your smart contracts
-------------------------------------------------------

As we've just seen, the core principal enabling the autonomous smart contracts feature, is the ability to emit a special message which has the effect of executing a desired function call at a specified window in time, and (optionally) when other specified conditions are met. 

The ABI method `sendMessage(...)` is the key to emitting such a message, and the provided parameters define its configuration: 

- `at`: Address of the smart-contract that you want to wakeup(can be yourself)
- `functionName`: Function of the smart-contract to call
- `validityStartPeriod` and `validityStartThread`: First slot where your message could be executed
- `validityEndPeriod` and `validityEndThread`: Last slot where you message can be executed
- `maxGas`: Maximum amount of gas for the execution of the function referenced in the message
- `rawFee`: A fee to gain prority in the async pool
- `coins`: Coins that will be passed to the smart contract called in the message
- `data`: Parameter of the function called in the message
- `filterAddress`: An optional address that you can define if you want your function to be executed in your validity period BUT only after a modification in the data of the address (balance, bytecode, datastore)
- `filterKey`: An optional datastore key that will execute the message only if there is changes on this datastore key of the `filterAddress`

Examples
--------

You can find an example of autonomous smart contracts in `this ticker example <https://github.com/massalabs/massa-sc-examples/tree/main/ticker>`__, where `sendMessage(...)` is used `here <https://github.com/massalabs/massa-sc-examples/blob/a69dd88079caf0932e638689a518627b15a4d407/ticker/assembly/contracts/oracle.ts#L25>`__