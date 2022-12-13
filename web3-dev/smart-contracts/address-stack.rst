Stack system in Massa
=====================

  * ExecuteSC
      When an account A send an ExecuteSC operation, the stack at the beginning
      of that execution is:
      `bottom [ A ] top`.

  * CallSC
      When an account A sends a CallSC operation to call a function in a smart
      contract B, the stack at the beginning of the execution of that function
      is:
      `bottom [ A, B ] top`.
      Note: A and B can be the same

  * Call from one smart contract to another
      When a function F from smart contract C is being executed with the stack
      [A, B, C] and calls a function on a smart contract D, the stack at the
      beginning of the execution of D's function becomes:
      `bottom [A, B, C, D] top`.
      When D's function finishes, the stack becomes
      `bottom [A, B, C] top`
      and the execution of F resumes

  * Autonomous SC:
      A message sent at a moment when the stack was [A, B, C] and calling a
      target function F of a smart contrtact D will yield the following stack
      at the beginning of the execution of the target function:
      `bottom [C, D] top`.
      Note: C and D can be the same

  * Local execution (not yet implemented, see https://github.com/massalabs/massa-sc-runtime/issues/170)
      Local executions don't change the stack: they allow executing foreign code
      in the current context

ABIs
----

In the `massa assemblyscript sdk <https://github.com/massalabs/massa-as-sdk/>`_
there is a Object called `Context`. You can import it in your smart contract code this way:

.. code-block::

        import { Context } from "@massalabs/massa-as-sdk";

It exposes some useful functions like:

  * `addressStack()` returns the full call stack as a list, bottom to top
  * `caller()` returns the stack element just below the top
  * `callee()` returns the stack element at the top of the stack
  * `transactionCreator()` returns the stack element at the bottom of the stack
