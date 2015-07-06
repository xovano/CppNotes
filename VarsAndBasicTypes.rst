*************************
Variables and basic types
*************************

.. contents::

Built-in types
==============
Built-in types include arithmetic types and ``void``.


Arithmetic types
~~~~~~~~~~~~~~~~
Arithmetic types include integral types and floating-point types.

Note that ``bool`` is an integral type.

=============== =====================
Type            *Minimum* size
=============== =====================
``bool``        Not available
``char``        8 bits
``wchar_t``     16 bits
``char16_t``    16 bits
``char32_t``    32 bits
``short``       16 bits
``int``         16 bits
``long``        32 bits
``long long``   64 bits
``float``       6 significant digits
``double``      10 significant digits
``long double`` 10 significant digits
=============== =====================

``char`` is big enough to hold a numerical values corresponding to the
characters in the machine's basic character set.

``wchar_t`` is big enough to hold any character in the machine's
largest extended character set.

Standard only specify minimum number of significant digit for
floating-point type. Most compiler provides higher precision: 7
significant digits for 32-bit ``float`` and 16 significant digits for
64-bit ``double``. ``long double`` often use special-purpose
floating-point hardware.


Signess
-------
Except for ``bool`` and extended character types, integral types can be
signed or unsigned.

Unlike other integral types, ``char``, ``signed char`` and ``unsigned
char`` are 3 distinct types. But there are only two different
representations: signed and unsigned. The representation used by
``char`` is implementation-defined.

All bits in unsigned types are used to represent the value.

Standard doesn't specify how signed types represented, but does specify
that the range should be evenly divided between positive and negative
values. For example, 8-bit ``signed char`` can represent value from
*-127* to 127, but there's no guarantee the it can represent -128.


Type conversions
~~~~~~~~~~~~~~~~

* When a floating-point value is assigned to an integer object,
  fractional part is truncated. The behavior is undefined if the part
  before decimal point can't be represented in target type.

* When an out-of-range integral value is assigned to an unsigned object,
  the result is the remainder of the value modulo the number of values
  that the target type can hold.

* When an out-of-range value is assigned to an signed object, the
  behavior is undefined.

Some weird consequences:

.. sourcecode:: cpp

    // Defined, value after truncation (0) fits in unsigned
    unsigned u1 = -0.5;

    // But this's undefined since value after truncation (-3) can't be
    // represent in unsigned
    unsigned u2 = -3.14;

    // Still undefined
    unsigned u3 = static_cast<unsigned>(-3.14);

    // But this's defined. First truncate to -3,
    // then convert to unsigned using modulo rule
    unsigned u4 = static_cast<int>(-3.14);

The initialization of ``u3`` uses casting so compiler might not warn
about undefined behavior (GCC emits warning for ``u2``). This
illustrate how casting can be bad. MISRA-C++ requires that all
conversions which cause lost of precision must use casting, which is
unfortunate since it just *hide* the problem in this case. The above
undefined behaviors can be detected with Clang's UBSan.

**Don't mix signed and unsigned types, especially when the signed value
is negative**. `Signed values may be converted to unsigned type
automatically`__ and can yield surprising result.

.. __: Expressions.rst#type-conversion-in-arithmetic-expression


Literals
~~~~~~~~

Integer literal
---------------

Decimal literal has the smallest type of ``int``, ``long`` and ``long
long`` (or corresponding unsigned types if the literal has ``u`` or
``U`` suffix) in which its value fits.

Literal in base 2 (C++14), 8 and 16 has smallest type of ``int``,
``unsigned``, ``long``, ``unsigned long``, ``long long`` and ``unsigned
long long`` in which its value fits (skip singed type if the literal
has ``u`` or ``U`` suffix).

If the value of the integer literal is too big to fit in any of the
types above and the compiler supports extended integer types (such as
``__int128``) the literal may be given the extended integer type.
Otherwise the program is ill-formed.


Digit separator (C++14)
-----------------------
Separator, if used, must be inserted between two digits.
``1.602'176'5e-9`` and ``0b1111'1111'1111`` are valid, but ``1.'293'1``
and ``0b'1111'1111'`` aren't.


Escape sequences
----------------
General form: ``\x`` followed by a hexadecimal number, or ``\``
followed by one, two or three octal digits.

Note that if ``\`` is followed by more than 3 octal digits, only first
3 digits are part of the escape sequence.


Specifying type of a literal
----------------------------

======  ============================  ============
Character types
--------------------------------------------------
Prefix             Meaning                Type
======  ============================  ============
``u``   Unicode 16 character          ``char16_t``
``U``   Unicode 32 character          ``char32_t``
``L``   Wide characters               ``wchar_t``
``u8``  UTF-8 (string literals only)  ``char``
======  ============================  ============


================  ==============
Integer types
--------------------------------
      Suffix      *Minimum* type
================  ==============
``u`` or ``U``    ``unsigned``
``l`` or ``L``    ``long``
``ll`` or ``LL``  ``long long``
================  ==============


==============  ===============
Floating-point types
-------------------------------
    Suffix            Type
==============  ===============
``f`` or ``F``  ``float``
``l`` or ``L``  ``long double``
==============  ===============


Variables
=========

Variable initialization
~~~~~~~~~~~~~~~~~~~~~~~
Variables of built-in type defined outside function body are
initialized to 0.


Declarations and definitions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A declaration makes a name known to the program. A definintion creates
entity associated with that name.

.. sourcecode:: cpp

    extern int i;       // declares but not define i
    extern int k = 10;  // declares and defines k


It's illegal to provide initializer on an ``extern`` inside a function.

A variable can be declared many times but can be defined only once.


Pointers
========
Pointer can be in one of four states:

1. Point to an object (valid)
2. Point to a location immediately past the end of an object (valid)
3. Null pointer (valid)
4. Invalid

Assigning an integer *variable* to a pointer is illegal, even if the
variable's value is 0.

.. sourcecode:: cpp

    int* p1 = 0;  // legal, null pointer

    int a = 0;
    int* p2 = a;  // illegal


We can add and subtract null pointer with a constant expression which
equals to 0. We can subtract to null pointers and get 0 as result.

Distance between two pointers is represented in signed type
``ptrdiff_t`` (defined in ``cstddef`` header).


``const`` and ``constexpr`` variables
=====================================

``constexpr`` variables
~~~~~~~~~~~~~~~~~~~~~~~
``constexpr`` on variables implies top-level ``const``.

.. sourcecode:: cpp

    constexpr int* p = nullptr;  // const pointer, NOT pointer to const


Literal types
~~~~~~~~~~~~~
Types that can be used in ``constexpr`` are called literal types. They
are simple enough to have literal values.

``constexpr`` pointer can be initialized with ``nullptr`` or ``0``
literals, or bound to and objects with fixed address (global and
``static`` local objects).


Sharing ``const`` across translation units
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When a ``const`` is initialized by a constant expression, compiler
usually replace all usages of the variable with its value. Therefore
compiler must have access to the initializer in every TU (by putting
the ``const`` definition in header). To avoid violating one-definition
rule, by default ``const`` variables are local to the TU.

In case we need to share the same copy of a ``const`` across multiple
TUs (e.g. the ``const`` is initialized in only one TU from a
non-constant expression), we have to put ``extern`` on its definintion
and on all of its declarations.

.. sourcecode:: cpp

    // file_A.cpp defines the const and shares it with others
    extern const int timeout = getTimeoutFromConfig();

    // file_X.h, included in all cpp files that need bufferSize.
    // Should be included in file_A.cpp too for error checking
    extern const int timeout;


Different ways to declare variable type
=======================================

Type aliases
~~~~~~~~~~~~
Declaration that use type alias for a compound type and ``const`` can
yield surprising result. The code snippet:

.. sourcecode:: cpp

    typedef char* pstr;
    const pstr p1 = nullptr;


can be mis-interpreted by programmer as:

.. sourcecode:: cpp

    const char* p1 = nullptr;  // p1 points to const char


and it's WRONG.

Base type in the above declaration is ``const pstr``. ``const`` in base
type qualifies the given type. ``pstr`` is "pointer to ``char``", so
``const pstr`` is "``const`` pointer to ``char``", not "pointer to
``const char``. In the wrong interpretion, base type is ``const char``,
``*`` is just a part of declarator ``* p1``.


``auto`` type specifier
~~~~~~~~~~~~~~~~~~~~~~~
``auto`` and ``auto*`` can be used interchangeably to define pointer,
except when cv-qualifier is involved:

.. sourcecode:: cpp

    int i = 42;
    const auto  p1 = &i;  // int* const
    const auto* p2 = &i;  // const int*


``decltype`` type specifier
~~~~~~~~~~~~~~~~~~~~~~~~~~~
``decltype`` does NOT evaluate its argument, similar to ``sizeof``.

``decltype`` doesn't drop top-level ``const`` and reference like
``auto``.

When we apply ``decltype`` to a lvalue_ that's not an identifier or a
member access expression, the return type is reference type.

.. _lvalue: Expressions.rst#lvalue_and_rvalue

.. sourcecode:: cpp

    int i = 42;
    int* p = &i;

    decltype(*p)  a;  // error, int& must be initialized

    decltype(i)   b;  // OK, int
    decltype((i)) c;  // error, int& must be initialized

    struct S {
        int x = 0;
    };

    S s;
    const S* pcs = &s;
    decltype(s.x) d;       // OK, int
    decltype(pcs->x) e;    // OK, int
    decltype((pcs->x)) f;  // error, const int& must be initialized


Note that ``decltype((variable))`` is always reference type, and
``decltype(variable)`` is reference type if and only if ``variable``
is a reference.


``decltype(auto)`` (C++14)
~~~~~~~~~~~~~~~~~~~~~~~~~~
``decltype(auto)`` can be used in place of ``auto`` in variable
declaration to use ``decltype``'s deduction rule instead of ``auto``'s.
But note that ``decltype(auto)`` can't be combined with type modifiers
and qualifers like ``*``, ``&``, ``const`` and ``volatile``.

.. sourcecode:: cpp

    const int ci  = 24;
    decltype(auto) a = ci;    // const int
    decltype(auto) b = (ci);  // const int&


Using ``decltype(auto)`` for variable declaration is NOT recommended,
since variable's reference-ness and top-level ``const``-ness should not
depend on initializer. ``decltype(auto)`` is mainly used to write
`forwarding function`__.

.. __: Functions.rst#automatic-return-type-deduction-with-decltype-auto
