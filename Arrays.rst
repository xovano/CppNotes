******
Arrays
******

.. contents::

Array definition
================
``auto`` can't be used in array definition.

We can value-initialize all element using list-initialization syntax
with an empty list

.. sourcecode:: cpp

    int a[20] = {};  // all elements are initialized to 0


Pointer and array
=================
In most cases, array name is converted to pointer, except when it's
operand of ``&``, ``sizeof``, ``typeid`` and ``decltype``, or when the
array is used to initialize a reference.

.. sourcecode:: cpp

    int a[] = {1, 2, 3};        // int[3]
    decltype(a) b = {3, 2, 1};  // int[3]
    auto p = a;                 // int*
    auto& r = a;                // int (&)[3], a reference to int[3]



Array indexing
==============
Unlike ``string`` and standard containers, array indexing operator
doensn't convert its operand to unsigned type. Negative indices are OK
as long as the accesses are not out-of-bound.

We can take address of the (non-existent) one-past-the-last element of
an array. In fact this is the only valid operation on this element.

.. sourcecode:: cpp

    int a[] = {1, 2, 3};
    int* p = &a[3];  // valid
    a[3] = 4;        // undefined


See also `pointer states`__.

.. __: VarsAndBasicTypes.rst#pointers


Multidimensional array
======================

Multidimensional array initialization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Nested braces in initializer are optional. Example:

.. sourcecode:: cpp

    int a[3][3] = {1, 2, 3, 4, 5, 6, 7};


is equivalent to

.. sourcecode:: cpp

    int a[3][3] = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 0, 0},
    }


If you also omit the first dimension the compiler can determine it
wrong.


Range-based for loop and multidimensional array
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When using range-based for loop on multidimensional array, control
variables for all dimensions but the innermost one must be references.

Example:

.. sourcecode:: cpp

    int arr2d[3][3];
    // fill the array

    for (auto row : arr2d)  // row is an int*
        for (auto elem : row)  // compile error
            std::cout << elem << std::endl;


In the above code, when ``row`` is initialized, the initialization code
converts each sub-array (``int[3]``) to an ``int*``. Remember that in
C++ an array can't be copy-initialized from another array.

The correct way is:

.. sourcecode:: cpp

    for (const auto& row : arr2d)
        for (auto elem : row)
            std::cout << elem << std::endl;
