**********
Statements
**********

.. contents::

``switch`` statement
====================
It's illegal to jump from a place where a variable *with initializer*
is out of scope to a place where that variable is in scope.


``goto`` statement
==================
Similar to ``switch``, ``goto`` can't jump from a point where an
*initialized* variable is out of scope to a point where that variable
is in scope.

Jump backward over an already executed definition is fine. In this case
the variable is destroyed and reconstructed again.
