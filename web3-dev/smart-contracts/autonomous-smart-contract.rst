.. _autonomous-smart-contract:

Autonomous Smart Contract
=========================

In smart-contract world of Massa, you can create smart-contracts that can be triggered in the future.

This system works by sending messages in the future specifying the address and the function you want to trigger.

You need to use the ABI `sendMessage(...)` in your smart contract if you want to send a message to wakeup a function in the future.

When the message is sent it will debit you the gas directly in this operation and store this message in a separate pool
than the operation one called async pool.

This pool is totally deterministic because it's filled by information that come from operations included in blocks
and processed by the whole network.

The pool has a finite size and sort the messages with the same behavior as the operation pool: there is a priority with the fees
and the messages get removed after a certain number of slots. 

The number of messages to execute each slot are limited by a maximum amount of gas like normal operations 
and so it's possible that your message isn't executed at the slot that you want (like operations that could be not included in the first block after you sent it)
but some slots later when there is enough space to include your message. If you want to have your message to be included as soon as possible
you have to put more fees than the others (just like the operations).

The formula to orders the messages is : `(rev(Ratio(msg.fee, max(msg.max_gas,1))), emission_slot, emission_index)` where `emission_index` is an index that differentiate
multiple messages created in the same slot.

All the information are given by the smart-contract calling the ABI
in ABI parameters. Let's investigate the parameters in the prototype of the ABI 
`sendMessage(at, functionName, validityStartPeriod, validityStartThread, validityEndPeriod, validityEndThread, maxGas, rawFee, coins, msg, filterAddress, filterKey)` :

- `at`: Address of the smart-contract that you want to wakeup(can be yourself)
- `functionName`: Function of the smart-contract to call
- `validityStartPeriod` and `validityStartThread`: First slot where your message could be executed
- `validityEndPeriod` and `validityEndThread`: Last slot where you message can be executed
- `maxGas`: Maximum amount of gas for the execution of the function referenced in the message
- `rawFee`: A fee to gain prority in the async pool
- `coins`: Coins that will be passed to the smart contract called in the message
- `data`: Parameter of the function called in the message
- `filterAddress`: An optional address that you can define if you want your message to be executed in your validity period
but only after a modification in the data of the address (balance, bytecode, datastore)
- `filterKey`: An optional datastore key that will execute the message only if there is changes on this datastore key of the `filterAddress`

You can find an example of an oracle using autonomous smart contracts `here <https://github.com/massalabs/massa-sc-examples/tree/oracle_example/oracle>`__