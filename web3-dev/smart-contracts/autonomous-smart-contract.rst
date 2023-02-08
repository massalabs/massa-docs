.. _autonomous-smart-contract:

Autonomous Smart Contract
=========================

In smart-contract world of Massa, you can create smart-contracts that can be called in the future.

This system works by sending messages in the future specifying the address and the function you want to trigger.

You need to use the ABI `sendMessage(...)` in your smart contract if you want to send a message to wakeup a function in the future.
When the message is sent it will debit you the gas directly in this operation and store this message in a separate pool
than operation one called async pool.
