***
Hàm
***

.. contents:: Mục lục

Khai báo và định nghĩa hàm
==========================
Mặc dù không cho phép định nghĩa hàm cục bộ (hàm bên trong hàm), C++ cho phép
khai báo hàm cục bộ (khai báo nguyên mẫu hàm bên trong một block). Nhưng đây
thường là một ý tưởng tồi.

Khai báo lại một hàm nhiều lần là thao tác hợp lệ.


Truyền đối số
=============
Không có sự đảm bảo nào về thứ tự tính toán giữa các đối số.


Tham số ``const``
~~~~~~~~~~~~~~~~~
Giống như mọi khởi tạo khác, khi đối số được sao chép để khởi tạo cho tham
số, ``const`` cấp cao nhất trong danh sách tham số của hàm bị bỏ qua.

Ta có thể bỏ ``const`` cấp cao nhất ra khỏi danh sách tham số trong khai báo
hàm nhưng có ghi nó trong định nghĩa hàm. Nguyên mẫu của hàm vẫn giữ nguyên
bất kể có ``const`` ở trước tham số truyền theo giá trị hay không. Việc có
``const`` cấp cao nhất hay không ở định nghĩa hàm thuần tuý là một chi tiết
implementation, và do đó không nên xuất hiện ở interface (khai báo hàm).


Tham số của ``main``
~~~~~~~~~~~~~~~~~~~~
Phần tử đầu tiên của "mảng" ``argv`` có thể trỏ tới một xâu rỗng.


Hàm với các tham số thay đổi
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Có 3 lựa chọn để viết hàm với danh sách tham số thay đổi:

#. Truyền vào ``std::initializer_list`` nếu các phần tử có cùng kiểu (từ C++11).
   Chú ý rằng các phần tử trong ``std::initializer_list`` đều là ``const``.
#. Dùng template với số ngôi biến đổi (variadic template - từ C++11).
#. Dùng tham số bỏ lửng (ellipsis parameters). Hầu hết đối tượng của các lớp
   không sao chép đúng khi truyền vào tham số bỏ lửng nên chỉ dùng lựa chọn
   này nếu cần interface với mã C.

Hàm với tham số bỏ lửng có dạng:

*kiểu_trả_về* *tên_hàm*\ ``(``\ *danh_sách_tham_số*\ ``, ...);``

trong đó *danh_sách_tham_số* có thể rỗng và dấu phảy theo sau nó là tuỳ chọn.
Các đối số ứng với tham số bỏ lửng không được kiểm tra kiểu.


Trả về từ hàm
=============

Cách giá trị được trả về
~~~~~~~~~~~~~~~~~~~~~~~~
Giá trị trả về được dùng để khởi tạo một đối tượng tạm thời tại vị trí hàm
được gọi, và đó là kết quả của hàm.


Tự động suy luận kiểu trả về (C++14)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Suy luận kiểu trả về với ``auto``
---------------------------------
Kể từ C++14, ta có thể dùng ``auto`` trong kiểu trả về của hàm mà không cần
chỉ rõ kiểu trả về ở đuôi để trình dịch tự động suy luận kiểu trả về. Kiểu
này được xác định từ biểu thức của lệnh ``return``, theo quy tắc suy luận
kiểu của ``auto`` `như đối khai báo biến`__, ngoại trừ việc không
thể trả về brace-init list:

.. __: VarsAndBasicTypes.rst#chi-dinh-kieu-auto

.. sourcecode:: cpp

    int i = 293;
    const auto& f() {
        return i;  // kiểu trả về là const int&
    }

    auto g() {
        return {1, 2, 3};  // LỖI, không thể trả về brace-init list
    }


Nếu thân hàm không có lệnh ``return`` nào, kiểu trả của hàm là ``void``.

.. sourcecode:: cpp

    auto  f() {}  // kiểu trả về là void
    auto* f() {}  // LỖI, auto* không khớp với void


Nếu thân hàm có nhiều lệnh ``return``, chúng phải cùng suy ra một kiểu. Kiểu
suy luận được từ lệnh ``return`` đầu tiên có thể được sử dụng trong phần còn
lại của hàm. Điều này cho phép gọi đệ quy nếu trước đó có ít nhất một lệnh
``return`` cho phép xác định kiểu trả về:

.. sourcecode:: cpp

    auto sum(int i) {
        if (i == 1)
            return i;  // kiểu trả về là int
        else
            return sum(i - 1) + i; // OK, đã biết kiểu trả về của lời gọi sum(i - 1)
    }


Hàm sử dụng suy luận kiểu trả về có thể được forward declare nhưng chỉ có
thể dùng được sau khi được định nghĩa và định nghĩa đó phải có mặt trong đơn
vị dịch sử dụng hàm. Không thể khai báo lại hàm đó với cách suy luận kiểu
khác (như ``decltype(auto)``, xem bên dưới), hoặc với kiểu trả về đã suy
luận được (hiển nhiên khai báo lại hàm với kiểu trả về khác kiểu đã suy luận
được là bất hợp lệ do không thể overload dựa trên kiểu trả về).

.. sourcecode:: cpp

    auto f();
    auto f() { return 1; }  // định nghĩa, kiểu trả về là int
    int f();                // LỖI, không thể khai báo lại với kiểu trả về đã suy luận được
    decltype(auto) f();     // LỖI, dùng cách suy luận kiểu khác
    auto f();               // OK, khai báo lại



Suy luận kiểu trả về với ``decltype(auto)``
-------------------------------------------
Khai báo ``decltype(auto)`` cho kiểu trả về hoạt động giống như ``auto`` cho
kiểu trả về nhưng sử dụng quy tắc suy luận kiểu của ``decltype`` (như `trong
khai báo biến`__). Điều này cho phép bảo toàn tính chất tham chiếu của biểu
thức trả về, và là hữu ích để viết các hàm chuyển tiếp, khi mà chúng ta muốn
kiểu trả về *theo chính xác* kiểu của biểu thức trả về.

.. __: VarsAndBasicTypes.rst#chi-dinh-kieu-decltype

Chẳng hạn chúng ta có hai hàm:

.. sourcecode:: cpp

    std::string lookup1();
    std::string& lookup2();


và cần viết các hàm chuyển tiếp xác thực người dùng rồi gọi các hàm
``lookup`` thích hợp:

.. sourcecode:: cpp

    std::string authAndLookup1();
    std::string& authAndLookup2();


Trong C++11 trở về trước, ta cần chỉ rõ kiểu trả về hoặc sử dụng
``decltype`` trong phần kiểu ở đuôi. Với C++14, ta có thể viết ngắn gọn như
sau:

.. sourcecode:: cpp

    decltype(auto) authAndLookup1() {
        authenticateUser();
        return lookup1();
    }

    decltype(auto) authAndLookup2() {
        authenticateUser();
        return lookup2();
    }


Chú ý rằng kiểu trả về ``decltype(auto)`` chỉ có thể đứng riêng mình nó chứ
không thể sử dụng cùng các type modifier hay qualifier, chẳng hạn ``const
decltype(auto)&`` là bất hợp lệ.

Vì ``decltype(auto)`` sử dụng quy tắc suy luận kiểu của ``decltype``, cách
viết sau trả về tham chiếu và đó là lỗi lập trình (trả về tham chiếu tới
biến cục bộ):

.. sourcecode:: cpp

    decltype(auto) authAndLookup1() {
        authenticateUser();
        auto str = lookup1();
        return (str);
    }



Trả về con trỏ hoặc tham chiếu
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Đừng bao giờ trả về con trỏ hoặc tham chiếu tới biến cục bộ. Đôi khi điều này
xảy ra nếu ta không chú ý, như trong tình huống sai thứ hai trong đoạn mã
dưới đây:

.. sourcecode:: cpp

    const std::string& f() {
        std::string ret;
        // làm gì đó với ret

        if (!ret.empty())
            return ret;      // SAI, trả về tham chiếu tới biến cục bộ ret
        else
            return "Empty";  // SAI, trả về tham chiếu tới đối tượng string tạm thời cục bộ
    }


Tình huống sai thứ nhất khá rõ ràng.

Trong tình huống sai thứ hai, mặc dù thời gian sống của literal xâu
``"Empty"`` vẫn tiếp tục sau khi hàm kết thúc, nó bị chuyển đổi thành một đối
tượng ``std::string`` tạm thời khi trả về. Đối tượng này là cục bộ đối với
``f``.

Một cách để đảm bảo rằng một lệnh trả về là an toàn là đặt câu hỏi: tham
chiếu (con trỏ) được trả về tham chiếu (trỏ) tới *đối tượng đã tồn tại từ
trước* nào?


Overload hàm
============
Mặc dù overload giúp tránh việc phải nghĩ ra (và nhớ) tên mới cho một thao
tác chung, chúng ta chỉ nên overload các thao tác thực sự thực hiện các công
việc có tính tương đồng cao. Có những tình huống mà các tên khác nhau giúp
chương trình dễ hiểu hơn. Xét một ví dụ về các hàm thành viên di chuyển con
trỏ trên màn hình soạn thảo. Cách đặt tên

.. sourcecode:: cpp

    Screen& moveCursorHome();
    Screen& moveCursorAbsolute(int row, int col);
    Screen& moveCursorRelative(int rowOffset, int colOffset, Direction);


là tốt hơn so với cách overload

.. sourcecode:: cpp

    Screen& moveCursor();
    Screen& moveCursor(int row, int col);
    Screen& moveCursor(int rowOffset, int colOffset, Direction);


do các di chuyển này dù tương tự nhưng có những đặc tính riêng. Khi gọi hàm,
đặt tên theo cách thứ nhất cho mã dễ hiểu hơn.

.. sourcecode:: cpp

    textScreen.moveCursorHome();  // chúng ta nghĩ: "di chuyển con trỏ về đầu dòng"
    textScreen.moveCursor();      // huh? di chuyển con trỏ đi đâu?


``const_cast`` và overload
~~~~~~~~~~~~~~~~~~~~~~~~~~
``const_cast`` hay dùng nhất trong các tình huống overload dựa trên ``const``:

.. sourcecode:: cpp

    const std::string& betterString(const std::string& s1, const std::string& s2) {
        // thực hiện tính toán để xác định xâu nào "tốt" hơn theo một tiêu chí nào đó
        // trả về xâu "tốt" hơn (s1 hoặc s2)
    }

    std::string& betterString(std::string& s1, std::string& s2) {
        auto& ret = betterString(const_cast<const std::string&>(s1),
                                 const_cast<const std::string&>(s2));
        return const_cast<std::string&>(ret);
    }


Hai bản overload trên cho phép gọi trả về tham chiếu tới xâu ``const`` hoặc
không ``const`` phù hợp với tham số truyền vào. Nếu chỉ có bản thứ nhất, kể
cả khi đối số ban đầu là không ``const``, kết quả trả về vẫn là ``const`` và
do đó có thể gây hạn chế số lượng thao tác có thể thực hiện trên kết quả.


Overload và phạm vi
~~~~~~~~~~~~~~~~~~~
Giống như mọi tên khác, hàm được khai báo ở phạm vi trong sẽ che hàm được
khai báo ở phạm vi ngoài. **Phân giải tên xảy ra trước kiểm tra kiểu**. Do đó
không thể overload giữa các phạm vi.

.. sourcecode:: cpp

    void print(const std::string&);
    void print(double);

    int main() {
        void print(int);  // Bad practice!

        print("Hello");   // LỖI vì gọi print(int) và không chuyển đổi được từ literal xâu sang int
        print(42);        // OK, gọi print(int)
        print(3.14);      // OK, nhưng gọi print(int) thay vì print(double)
    }


Đối số mặc định
===============
Vẫn có thể bỏ qua tên tham số được chỉ định giá trị mặc định, chẳng hạn khai báo

.. sourcecode:: cpp

    double f(double, double = 3.14);


là hợp lệ.

Các lần khai báo sau của hàm có thể chỉ định thêm định giá trị mặc định cho
tham số chưa có giá trị mặc định.

Tên dùng trong biểu thức dùng làm đối số mặc định được phân giải trong phạm
vi của khai báo hàm. Giá trị của chúng được tính tại thời điểm gọi hàm.

.. sourcecode:: cpp

    int globalX = 0;
    int globalY = 0;

    int getCurrentZ();
    double distance(int x = globalX, int y = globalY, int z = getCurrentZ());

    double g() {
        globalX = 42;
        int globalY = 1482;
        return distance();  // gọi distance(42, 0, getCurrentZ())
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
- Định nghĩa biến không phải kiểu literal, biến ``static``, biến
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
một giá trị*.

Tương tự như mảng, khi ta khai báo tham số của hàm là một hàm, nó được hiểu
là con trỏ tới hàm.

Ngoài các trường hợp trên, hàm và con trỏ hàm là khác nhau.

.. sourcecode:: cpp

    using F = int(int, int);  // F là kiểu hàm, không phải kiểu con trỏ hàm
    using PF = int (*)(int, int);  // PF là kiểu con trỏ hàm

    F  f1(int funcNumber);  // lỗi, không thể trả về hàm
    F* f2(int funcNumber);  // OK
    PF f3(int funcNumber);  // OK


``decltype`` khi áp dụng lên hàm cũng `cho kiểu hàm chứ không phải là con trỏ
hàm`__.

.. __: VarsAndBasicTypes.rst#chi-dinh-kieu-decltype

Không có phép chuyển đổi nào giữa các kiểu con trỏ hàm khác nhau, nhưng ta có
thể gán ``nullptr`` hoặc một `biểu thức hằng`_ nguyên có giá trị 0 vào con trỏ
hàm.

.. _biểu thức hằng: VarsAndBasicTypes.rst#constexpr-va-bieu-thuc-hang
