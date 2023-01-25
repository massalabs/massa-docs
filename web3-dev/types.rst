.. index:: library, types

.. _sc-types:

Types
=====

Massa AS-types package (`@massalabs/as-types`) contains assemblyscript objects designed to facilitate the writing of
smart contracts.

.. note::

    You know a nugget that could be added to this list or you have a specific need for a new type? `Open an issue
    <https://github.com/massalabs/as/issues>`_ to discuss about it!

.. _currency:

Currency
--------

A representation of a monetary unit used to express a value.

Usage
~~~~~

.. code-block:: typescript

    import { Currency } from '@massalabs/as-types';

    const euro = new Currency("Euro", 2);
    const yen = new Currency("Japanese yen", 0);
    const isSame = euro.sameAs(yen); // False

More info in the `Currency documentation <https://as-types.docs.massa.net/classes/Currency.html>`_.

.. _amount:

Amount
------

A representation of a value in a :ref:`Currency`.

Usage
~~~~~

.. code-block:: typescript

    import { Amount, Currency } from '@massalabs/as-types';

    const euro = new Currency("Euro", 2);

    const price = new Amount(500, euro);
    const accountBalance = new Amount(100, euro);

    cont isEnough = price.lessThan(accountBalance); // False
    const isValidAmount = accountBalance.substract(price).isValid(); // False

More info in the `Amount documentation <https://as-types.docs.massa.net/classes/Amount.html>`_.

.. _args:

Args
----

A class used to serialize and deserialize arguments (of functions, data to/from storage...).

Usage
~~~~~

Serialization
~~~~~~~~~~~~~

.. code-block:: typescript

    import { Address, call } from '@massalabs/massa-as-sdk';

    const address = new Address(
        'A17vYbpxBxiwNgYM4WgnLyMwNzj8vp5t13QMJvZMbhGyJyysxs6',
    );
    const stringArgument = "myStringArgument!"
    call(address, 'mySCFunction', new Args().add(stringArgument), 0);
    ...

Deserialization
~~~~~~~~~~~~~~~

.. code-block:: typescript

    import { Args } from '@massalabs/as-types';

    export function sayHello(args: StaticArray<u8>): void {
    const stringArgument = new Args(args).nextString().unwrap();
    ...

See all Args methods and supported types for serialization/deserialization in the `Args documentation
<https://as-types.docs.massa.net/classes/Args.html>`_.
