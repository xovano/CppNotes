*********
Functions
*********

.. contents::

Function declarations and defintions
====================================
C++ doesn't allow nested function defintions but allows nested function
declarations. Declaring a function locally is unusual, but sometimes
it's useful. For example it allow you to call a function without
including a big header file and polluting global namespace.


Argument passing
================


``const`` parameters
~~~~~~~~~~~~~~~~~~~~
The function signatures are the same whether you include top-level
``const`` in front of a parameter or not. For example, ``f(int)`` and
``f(const int)`` are the same function.

Whether or not a parameter is ``const`` in a function definition is
purely an implementation detail.


Parameters of ``main``
~~~~~~~~~~~~~~~~~~~~~~
``argv[0]`` doesn't necessary point to name of the program. In fact it
can point to an empty string.


Variadic functions
~~~~~~~~~~~~~~~~~~
There are 3 choices to write variadic function:

#. Use ``std::initializer_list`` parameter if all "arguments" have the
   same type. Note that all elements of ``initializer_list`` are
   ``const``. And `beware of copies with std::initializer_list
   <https://tristanbrindle.com/posts/beware-copies-initializer-list>`_.
#. Use variadic template.
#. Use ellipsis paramerters. Almost all class objects won't be copied
   correctly. Therefore this should only be used when interfacing with
   C.

Function using ellipsis paramerter is declared like this:

*return_type* *func_name*\ ``(``\ *param_list*\ ``, ...);``

*param_list* can be empty and the comma after that is optional.


Returning from function
=======================

Returning a reference or pointer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Never return a reference or pointer to a local object. This rule seems
easy to follow but you can make mistake due to implicit conversion
creating an local temporary, like this:

.. sourcecode:: cpp

    const std::string& f() {
        std::string ret;
        // do something with ret

        if (!ret.empty())
            return ret;        // Wrong, pretty obvious
        else
            return "Default";  // Wrong, return a reference to local temporary
    }


Automatic return type deduction (C++14)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Return type deduction with ``auto``
-----------------------------------
From C++14, ``auto`` can be used as return type. Compiler determines
actual return type from the expresion used in ``return`` statement, the
same way as when ``auto`` is `used to declare variables`__, except that
you can't return a brace-init list:

.. __: VarsAndBasicTypes.rst#auto-type-specifier

.. sourcecode:: cpp

    int i = 42;
    const auto& f() {
        return i;  // return type is const int&
    }

    auto g() {
        return {1, 2, 3};  // error, can't return brace-init list
    }


If the function's body has no ``return`` statement, return type is
deduced to ``void``:

.. sourcecode:: cpp

    auto  f() {}  // return type is void
    auto* f() {}  // error, auto* can't be deduced to void


If the function's body has multiple ``return`` statements, the
expressions have to have the same type. The type deduced from the first
``return`` can be used to deduce type in the rest of the function. This
allow recursion if the base cases are written first:

.. sourcecode:: cpp

    auto sum(int i) {
        if (i == 1)
            return i;  // return type is int

        return sum(i - 1) + i; // OK, type of sum(i - 1) is already known
    }


Function with return type deduction can be forward declared but can
only be used after it is defined with the definition available in
translation unit. The function can't be re-declared with other return
type deduction methods (e.g. ``decltype(auto)``), or with a return type
which is the same as the deduced one (since we can't overload function
by using different return types).

.. sourcecode:: cpp

    auto f();               // forward declaration
    auto f() { return 1; }  // definition, return an int
    int f();                // error, re-declare using the deduced type
    decltype(auto) f();     // error, re-declare with other return type deduction method
    auto f();               // OK



Automatic return type deduction with ``decltype(auto)``
-------------------------------------------------------
``decltype(auto)`` return type acts like ``auto`` return type but uses
deduction rule of ``decltype`` (same as in `variable declaration`__).
This allow preserving reference-ness of returned expression, which is
useful while writing forwarding functions.

.. __: VarsAndBasicTypes.rst#decltype-type-specifier

For example, let say we have 2 functions:

.. sourcecode:: cpp

    std::string lookup1();
    std::string& lookup2();


and we want 2 functions that authenticate user before calling the above
functions:

.. sourcecode:: cpp

    std::string authAndLookup1();
    std::string& authAndLookup2();


Before C++14 we have to use explicit return type or use ``decltype``
in trailing return type. In C++14, we can just write:

.. sourcecode:: cpp

    decltype(auto) authAndLookup1() {
        authenticateUser();
        return lookup1();
    }

    decltype(auto) authAndLookup2() {
        authenticateUser();
        return lookup2();
    }

The same usefulness (ability to deduced to reference type) can turn
into badness if we end up returning a reference to local object:

.. sourcecode:: cpp

    decltype(auto) authAndLookup1() {
        authenticateUser();
        auto str = lookup1();
        return (str);  // error, return a reference to local variable
    }


Note that ``decltype(auto)`` can't be combined with other type modifier
or qualifier. For example ``const decltype(auto)&`` is illegal.


Function overloading
====================
There are scenarios in which a set of different function names is better
than overloading. For example:

.. sourcecode:: cpp

    Screen& moveCursorHome();
    Screen& moveCursorAbsolute(int row, int col);
    Screen& moveCursorRelative(int rowOffset, int colOffset, Direction);


is better than

.. sourcecode:: cpp

    Screen& moveCursor();
    Screen& moveCursor(int row, int col);
    Screen& moveCursor(int rowOffset, int colOffset, Direction);


because the calling code is easier to understand:

.. sourcecode:: cpp

    textScreen.moveCursorHome();  // unambigious
    textScreen.moveCursor();      // huh? move cursor to where?


``const_cast`` and overloading
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
``const_cast`` is usually used when creating different overloads base
on ``const``-ness of arguments:

.. sourcecode:: cpp

    const Date& earlierDate(const Date& d1, const Date& d2) {
        // determine earlier date and return it
    }

    Date& earlierDate(Date& d1, Date& d2) {
        auto& ret = earlierDate(const_cast<const Date&>(d1),
                                const_cast<const Date&>(d2));
        return const_cast<Date&>(ret);
    }


Overloading and scope
~~~~~~~~~~~~~~~~~~~~~
As other name declaration, function declared in inner scope hides
function declared in outer scope. Furthermore, name lookup happens
before type checking. Functions do not overloads across scopes.

.. sourcecode:: cpp

    void f(const std::string&);
    void f(double);

    int main() {
        void f(int);  // this is hiding, not overloading

        f("42");  // error, no implicit conversion from string literal to int
        f(3.14);  // no error, BUT still calls f(int), not f(double)
    }


Default arguments
=================
As usual, name of parameters with default arguments can be ommited in
function declaration

.. sourcecode:: cpp

    double f(int, double = 3.14);  // OK


Subsequent declaration (in the same scope) can add a default only for a
parameter that has not previously had a default. The previous default
arguments don't have to be repeated. For example, we can add a default
for the first parameter of the above function like this:

.. sourcecode:: cpp

    double f(int = 42, double);

Names used in default argument expression are resolved in the scope of
the function declaration, and their value are evaluated at the time of
the call:

.. sourcecode:: cpp

    int X = 0;
    int Y = 0;

    int getCurrentZ();
    double distance(int x = X, int y = Y, int z = getCurrentZ());

    double g() {
        X = 42;
        int Y = 24;
        return distance();  // call distance(42, 0, getCurrentZ())
    }


Hàm ``inline`` và ``constexpr``
===============================
Kiểu trả về và kiểu của các tham số của hàm ``constexpr`` phải là `kiểu
literal`_

.. _kiểu literal: VarsAndBasicTypes.rst#kieu-literal

Trong C++11, thân hàm ``constexpr`` phải chứa duy nhất một lệnh ``return`` và
có thể chứa thêm các lệnh không yêu cầu hành động ở runtime bao gồm
``static_assert``, lệnh rỗng, khai báo tên khác cho kiểu (mà không định nghĩa
class hay kiểu liệt kê), khai báo và chỉ thị ``using``.

C++14 cho phép thân hàm ``constexpr`` dùng tất cả các cấu trúc của hàm thông
thường, ngoại trừ:

- Inline assembly (định nghĩa ``asm``).
- Lệnh ``goto``.
- Khối ``try``.
- Định nghĩa biến không có kiểu literal, biến ``static``, biến
  ``thread_local`` hoặc biến không khởi tạo.

Ví dụ: hai hàm sau không hợp lệ trong C++11 nhưng hợp lệ trong C++14

.. sourcecode:: cpp

    constexpr int f(int x) {
        return --x;
    }

    constexpr int g(int x, int n) {
        int r = 1;
        while (--n > 0) r *= x;
        return r;
    }


Hàm ``constexpr`` được ngầm định là ``inline``.

Trình dịch cần thấy được thân hàm ``inline`` và ``constexpr`` để khai triển
code cũng như thực hiện tính toán trong lúc dịch. Khác với các hàm khác, hàm
``inline`` (và do đó kéo theo hàm ``constexpr``) có thể được định nghĩa nhiều
lần, tuy nhiên các định nghĩa này phải khớp nhau. Vì vậy hàm ``inline`` và
``constexpr`` nên được định nghĩa trong header.


So khớp hàm
===========
Để xác định hàm nào được gọi, trình dịch cần thực hiện so khớp hàm, bao gồm
các bước:

- Xác định các hàm ứng cử viện: đó là các hàm có tên trùng với hàm được gọi
  và có khai báo thấy được tại điểm gọi hàm.

- Xác định các hàm khả thi (viable): là các hàm ứng cử viên có thể được gọi
  với số lượng đối số được cung cấp và kiểu của các đối số phải khớp hoặc
  chuyển đổi được sang kiểu của tham số.

  Nếu không có hàm nào khả thi, trình dịch sẽ báo lỗi vì không có hàm nào
  khớp với lời gọi.

- Xác định hàm khớp tốt nhất: hàm trong tập khả thi là khớp tốt nhất nếu

  + Mức độ khớp của mỗi đối số là không tồi hơn so với các hàm khả thi còn lại.
  + Ít nhất một đối số khớp tốt hơn so với các hàm khả thi còn lại.

  Nếu không xác định được hàm khớp tốt nhất (và duy nhất), trình dịch sẽ báo
  lỗi vì lời gọi là không rõ ràng.


Mức độ khớp của các đối số được xác định theo chiều giảm dần như sau:

#. Khớp chính xác:

   * Kiểu đối số trùng với kiểu tham số.
   * Đối số được chuyển đổi từ kiểu mảng hoặc hàm sang kiểu con trỏ tương ứng.
   * ``const`` cấp cao nhất được thêm vào hoặc bỏ đi từ đối số.

#. Khớp qua chuyển đổi ``const`` (chẳng hạn từ ``int&`` sang ``const int&``).
#. Khớp qua `nâng kiểu`_.
#. Khớp qua `chuyển đổi giữa các kiểu số học`__ hoặc chuyển đổi giữa các kiểu
   con trỏ (gồm cả chuyển đổi từ ``0`` hay ``nullptr``).
#. Khớp qua chuyển đổi định nghĩa bởi class.

.. _nâng kiểu: Expressions.rst#nang-kieu-nguyen
.. __: VarsAndBasicTypes.rst#chuyen-doi-kieu

Các quy tắc phức tạp trên cùng với nâng kiểu nguyên và chuyển đổi số học có
thể gây ra những kết quả bất ngờ không mong đợi. Xét hai hàm ``f(int)`` và
``f(short)``, ``f(short)`` có thể không được gọi ngay cả khi có vẻ nó khớp
tốt hơn với các đối số giá trị nhỏ.

.. sourcecode:: cpp

    void f(int);
    void f(short);

    f(10);   // gọi f(int), literal 10 có kiểu int
    f('a');  // gọi f(int), 'a' từ kiểu char được nâng lên int

    short n = 3;
    f(n);    // gọi f(short), khớp chính xác với kiểu của đối số


Nếu các tham số của các hàm overload không có liên hệ gần với nhau, ta thường
không cần quan tâm đến các quy tắc này vì có thể dễ dàng chỉ ra hàm nào được
gọi. Việc nhầm lẫn hàm được gọi hoặc phải ép kiểu đối số để chọn đúng hàm là
dâu hiệu gợi ý rằng chương trình được thiết kế không tốt.


Con trỏ hàm
===========
Hàm được chuyển đổi tự động sang con trỏ hàm khi *tên* hàm được sử dụng *như
một giá trị*. Và tương tự như mảng, khi ta khai báo tham số của hàm là một
hàm, nó được hiểu là con trỏ tới hàm.

.. sourcecode:: cpp

    int f(char);
    auto pf = f;  // pf có kiểu con trỏ hàm int (*)(char)

    void g(int a, int b, int f(int a, int b));  // g có kiểu void(int, int, int (*)(int, int))


Ngoài các trường hợp trên, hàm và con trỏ hàm là khác nhau.

.. sourcecode:: cpp

    using F = int(int, int);  // F là kiểu hàm, không phải kiểu con trỏ hàm
    using PF = int (*)(int, int);  // PF là kiểu con trỏ hàm

    F  f1(int funcNumber);  // lỗi, không thể trả về hàm
    F* f2(int funcNumber);  // OK
    PF f3(int funcNumber);  // OK


``decltype`` khi áp dụng lên tên hàm cũng cho kiểu hàm chứ không phải là con
trỏ hàm.

.. sourcecode:: cpp

    int foo(int);
    delctype(foo)  getFunc1(int funcNum);  // lỗi, decltype(foo) cho kiểu hàm int(int)
    decltype(foo)* getFunc2(int funcNum);  // OK


Không có phép chuyển đổi nào giữa các kiểu con trỏ hàm khác nhau, nhưng ta có
thể gán ``nullptr`` hoặc một `biểu thức hằng`_ nguyên có giá trị 0 vào con trỏ
hàm.

.. _biểu thức hằng: VarsAndBasicTypes.rst#constexpr-va-bieu-thuc-hang
