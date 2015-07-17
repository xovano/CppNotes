Biến và các kiểu cơ bản
#######################

.. contents:: Mục lục

Các kiểu nguyên thuỷ dựng sẵn
*****************************
Các kiểu nguyên thuỷ trong C++ gồm các kiểu số học và kiểu ``void``.


Các kiểu số học
~~~~~~~~~~~~~~~
Kiểu số học gồm các kiểu nguyên và các kiểu dấu phảy động.

Kích thước của các kiểu số học phụ thuộc vào nền tảng. Tiêu chuẩn chỉ đảm bảo
kích thước tối thiểu như bảng dưới đây.

=============== ===================================== ====================
Kiểu            Ý nghĩa                               Kích thước tối thiểu
=============== ===================================== ====================
``bool``        Boolean                               Không có
``char``        Kí tự                                 8 bit
``wchar_t``     Kí tự rộng                            16 bit
``char16_t``    Kí tự Unicode                         16 bit
``char32_t``    Kí tự Unicode                         32 bit
``short``       Số nguyên ngắn                        16 bit
``int``         Số nguyên                             16 bit
``long``        Số nguyên                             32 bit
``long long``   Số nguyên dài                         64 bit
``float``       Số dấu phảy động độ chính xác đơn     6 chữ số có nghĩa
``double``      Số dấu phảy động độ chính xác kép     10 chữ số có nghĩa
``long double`` Số dấu phảy động độ chính xác mở rộng 10 chữ số có nghĩa
=============== ===================================== ====================

Phần bộ nhớ nhỏ nhất có thể đánh địa chỉ được trên máy được gọi là "byte". Do đó
byte trong C++ không nhất thiết phải là 8 bit, mặc dù hầu hết các máy có các
byte 8 bit.

Kiểu ``char`` được đảm bảo là đủ lớn để có thể có thể lưu một giá trị số tương
ứng với các kí tự trong bộ kí tự căn bản của máy, tức là ``char`` có cùng kích
thước với một byte của máy.

Kiểu ``wchar_t`` được đảm bảo là đủ lớn để lưu mọi kí tự trong bộ kí tự mở rộng
lớn nhất của máy.

Kiểu ``long long`` được thêm vào kể từ C++11.

Đối với các kiểu dấu phảy động, tiêu chuẩn chỉ định một số lượng chữ số có nghĩa
tối thiểu. Hầu hết trình dịch cung cấp độ chính xác cao hơn. Thông thường
``float`` được biểu diễn bởi 32 bit, ``double`` được biểu diễn bởi 64 bit và
``long double`` được biểu diễn bởi 96 hoặc 128 bit. Do đó ``float`` và
``double`` thường cho lần lượt 7 và 16 chữ số có nghĩa. ``long double`` thường
được dùng để tận dụng các phần cứng dấu phảy động chuyên biệt, do đó độ chính
xác của kiểu này phụ thuộc implementation.


Có dấu và không dấu
-------------------
Ngoại trừ kiểu ``bool`` và các kiểu kí tự mở rộng, các kiểu nguyên có thể là có
dấu hoặc không dấu.

Không giống với các kiểu nguyên khác, ``char`` và ``signed char`` là hai kiểu
*khác nhau*. Mặc dù ``char``, ``unsigned char`` và ``signed char`` là ba kiểu
phân biệt, chỉ có hai các biểu diễn là không dấu và có dấu. Kiểu ``char`` sử
dụng một trong hai cách biểu diễn này, tuỳ thuộc vào implementation.

Đối với kiểu không dấu, tất cả các bit đều biểu diễn giá trị.

Tiêu chuẩn không quy định cách biểu diễn kiểu có dấu mà chỉ quy định rằng khoảng
biểu diễn cần được chia đều cho các giá trị âm và dương. Vì thế, nếu kiểu
``signed char`` rộng 8 bit, nó được đảm bảo là sẽ biểu diễn được các giá trị từ
*-127* đến 127.


Lựa chọn kiểu
-------------

* Sử dụng kiểu không dấu cho các giá trị không thể âm.
* Sử dụng kiểu ``int`` cho tính toán số học cho số nguyên. ``short`` thường quá
  nhỏ, ``long`` thường có cùng kích thước với ``int``. Nếu cần tính các giá trị
  lớn hơn giá trị tối thiểu được đảm bảo bới ``int``, hãy dùng ``long long``.
* Đừng dùng ``char`` hay ``bool`` trong các biểu thức số học. Nếu cần tính các
  số nguyên rất nhỏ, hãy chỉ rõ kiểu cần dùng là ``signed char`` hay ``unsigned
  char``.
* Dùng ``double`` cho tính toán dấu phảy động, ``float`` thường không có đủ độ
  chính xác, còn độ chính xác cung cấp bởi ``long double`` thường không cần
  thiết. Trên thực tế tính toán với ``double`` có thể cho tốc độ cao hơn.


Chuyển đổi kiểu
~~~~~~~~~~~~~~~

* Khi gán giá trị dấu phảy động vào một đối tượng kiểu nguyên, phần sau dấu
  phảy bị cắt bỏ. Hành vi là không xác định nếu giá trị sau khi được cắt bỏ
  không thể biểu diễn được trong kiểu đích.
* Khi gán một giá trị ngoài khoảng biểu diễn vào một đối tượng không dấu, kết
  quả được lưu trữ là phần dư của giá trị đó với số lượng giá trị mà kiểu đích
  có thể lưu trữ.
* Khi gán một giá trị ngoài khoảng biểu diễn vào một đối tượng có dấu, kết quả
  là không xác định.

Quy tắc trên có thể dẫn đến một số tình huống "lạ" như sau:

.. sourcecode:: cpp

    unsigned u = -0.5;  // XÁC ĐỊNH, u được khởi tạo với giá trị 0

    unsigned u = -3.14;  // (1) KHÔNG xác định, sau khi cắt bỏ phần sau dấu phảy
                         //     ta được giá trị -3 không biểu diễn được trong
                         //     kiểu unsigned
    unsigned u = -3;     // (2) XÁC ĐỊNH, khởi tạo theo quy tắc lấy phần dư

    unsigned u = static_cast<unsigned>(-3.14);  // KHÔNG xác định, tương tự như (1)
    unsigned u = static_cast<int>(-3.14);       // XÁC ĐỊNH, tương đương với (2)


**Đừng trộn lẫn số không dấu với số có dấu, nhất là khi số có dấu mang giá trị
âm**. Cần nhớ rằng số có dấu sẽ được chuyển đổi tự động sang không dấu nếu cần
và có thể cho kết quả không như mong đợi.


Literal
~~~~~~~

Literal nguyên
--------------

Literal số nguyên thập phân có kiểu mặc định là kiểu nhỏ nhất trong danh sách:
``int``, ``long`` và ``long long``.

Literal hệ cơ số 8 và 16 có kiểu mặc định là kiểu nhỏ nhất trong danh sách:
``int``, ``unsigned``, ``long``, ``unsigned long``, ``long long`` và ``unsigned
long long``.

Literal có giá trị quá lớn không thể biểu diễn được bởi kiểu lớn nhất trong danh
sách sẽ gây ra lỗi.

Escape sequence
---------------
Dạng escape sequence tổng quát trong C++ là ``\x`` theo sau bởi một số chữ số
của hệ cơ số 16 hoặc ``\`` theo sau bởi một, hai hoặc ba chữ số của hệ cơ số 8.
Số này biểu diễn giá trị số học của kí tự cần chỉ định.

Chú ý rằng nếu ``\`` được theo sau bởi nhiều hơn ba chữ số hệ cơ số 8, chỉ có ba
chữ số đầu tiên là gắn với ``\``. Trong khi đó, dạng ``\x`` sử dụng toàn bộ các
chữ số. Ví dụ, ``\1234`` tương ứng với kí tự thể hiện giá trị 123 trong hệ cơ số
8, theo sau bởi kí tự ``4``, còn ``\x1234`` thể hiện một kí tự 16 bit có giá trị
1234 trong hệ cơ số 16.

Chỉ định kiểu cho literal
-------------------------

=======  ========================  ============
Kiểu kí tự
-----------------------------------------------
Tiền tố         Ý nghĩa                Kiểu
=======  ========================  ============
``u``    Kí tự Unicode 16          ``char16_t``
``U``    Kí tự Unicode 32          ``char32_t``
``L``    Kí tự rộng                ``wchar_t``
``u8``   UTF-8 (chỉ dùng với xâu)  ``char``
=======  ========================  ============


==================  ================
Kiểu nguyên
------------------------------------
      Hậu tố        Kiểu *tối thiểu*
==================  ================
``u`` hoặc ``U``    ``unsigned``
``l`` hoặc ``L``    ``long``
``ll`` hoặc ``LL``  ``long long``
==================  ================


================  ===============
Kiểu dấu phảy động
---------------------------------
     Hậu tố            Kiểu
================  ===============
``f`` hoặc ``F``  ``float``
``l`` hoặc ``L``  ``long double``
================  ===============

Nên dùng hậu tố ``L`` thay vì ``l`` do chữ ``l`` rất dễ nhầm với số ``1``.


Biến
****

Khởi tạo biến
~~~~~~~~~~~~~

Khởi tạo và gán là hai thao tác *khác nhau* trong C++. Khởi tạo xảy ra khi biến
được cấp một giá trị khi nó được tạo ra. Gán phá huỷ giá trị (trạng thái) hiện
tại của biến và thay thế nó bởi một giá trị mới.

Trình dịch sẽ báo lỗi nếu ta khởi tạo biến có kiểu dựng sẵn bằng list
initialization (khởi tạo bằng danh sách) nếu việc khởi tạo đó dẫn đến mất thông
tin (thu hẹp kiểu).

Biến có kiểu dựng sẵn được định nghĩa ngoài thân hàm được khởi tạo mặc định với
giá trị 0.


Khai báo và định nghĩa
~~~~~~~~~~~~~~~~~~~~~~
Khai báo xác định một tên trong chương trình. Định nghĩa tạo ra thực thể gắn với
tên đó.

.. sourcecode:: cpp

    extern int i;       // khai báo, nhưng không định nghĩa
    int j;              // khai báo và định nghĩa
    extern int k = 10;  // khai báo và định nghĩa


Chú ý rằng khai báo biến ``extern`` có phần khởi tạo bên trong hàm là lỗi.

Một biến có thể được khai báo nhiều lần, nhưng chỉ có thể được định nghĩa đúng
một lần. Để sử dụng một biến trong nhiều hơn một tệp, chúng ta cần định nghĩa
biến đó trong một và chỉ một tệp, các tệp còn lại khai báo biến đó chứ không
định nghĩa.


Định danh
~~~~~~~~~
Danh sách tên các toán tử thay thế trong C++:

``and``
``and_eq``
``bitand``
``bitor``
``compl``
``not``
``not_eq``
``or``
``or_eq``
``xor``
``xor_eq``

Danh sách từ khoá trong C++:

``alignas``
``alignof``
``asm``
``auto``
``bool``
``break``
``case``
``catch``
``char16_t``
``char32_t``
``char``
``class``
``const_cast``
``const``
``constexpr``
``continue``
``decltype``
``default``
``delete``
``do``
``double``
``dynamic_cast``
``else``
``enum``
``explicit``
``export``
``extern``
``false``
``float``
``for``
``friend``
``goto``
``if``
``inline``
``int``
``long``
``mutable``
``namespace``
``new``
``noexcept``
``nullptr``
``operator``
``private``
``protected``
``public``
``register``
``reinterpret_cast``
``return``
``short``
``signed``
``sizeof``
``static_assert``
``static_cast``
``static``
``struct``
``switch``
``template``
``this``
``thread_local``
``throw``
``true``
``try``
``typedef``
``typeid``
``typename``
``union``
``unsigned``
``using``
``virtual``
``void``
``volatile``
``wchar_t``
``while``


Kiểu phức hợp
*************

Tham chiếu
~~~~~~~~~~
C++11 đưa thêm một loại tham chiếu mới gọi là *tham chiếu rvalue*. Do đó, khái
niệm "tham chiếu" nếu không nói cụ thể gì thêm được hiểu là tham chiếu "kiểu
cũ", hay chính xác hơn là *tham chiếu lvalue*.

Tham chiếu không phải là đối tượng, do đó không thể lưu được trên mảng hay
container.

Con trỏ
~~~~~~~
Con trỏ có thể ở một trong bốn trạng thái:

1. Trỏ tới một đối tượng.
2. Trỏ tới vị trí ngay sau điểm cuối của đối tượng.
3. Không trỏ tới đối tượng nào (null).
4. Không hợp lệ, nếu không thuộc ba trạng thái trên.

Mặc dù các trạng thái 2 và 3 là hợp lệ nhưng vì con trỏ không trỏ tới đối tượng,
sử dụng con trỏ như vậy để truy cập tới đối tượng (giả định) ở vị trí đó gây
hành vi không xác định.

Gán một biến kiểu nguyên vào con trỏ là bất hợp lệ, ngay cả khi giá trị của biến
đó bằng 0.

.. sourcecode:: cpp

    int* p1 = 0;  // hợp lệ, khởi tạo con trỏ null

    int a = 0;
    int* p2 = a;  // KHÔNG hợp lệ vì gán int vào con trỏ


Ta có thể cộng hoặc trừ con trỏ null với một biểu thức hằng có giá trị bằng
0. Cũng có thể trừ hai con trỏ null cho nhau và thu được kết quả là 0.

Khoảng cách giữa hai con trỏ được thể hiện bởi kiểu có dấu ``ptrdiff_t``,
định nghĩa trong tiêu đề ``cstddef``.


``const`` qualifier
*******************

Chia sẻ đối tượng ``const`` giữa các tệp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Khi một đối tượng ``const`` được khởi tạo từ hằng số compile-time, trình dịch sẽ
thay thế các vị trí sử dụng biến đó bởi giá trị tương ứng. Điều này đòi hỏi
trình dịch phải thấy được phần khởi tạo của biến ``const`` đó. Khi chương trình
được chia thành nhiều tệp, mỗi tệp sử dụng ``const`` đều phải truy cập được đến
phần khởi tạo của nó, tức là biến ``const`` phải được định nghĩa trong tất cả
các tệp sử dụng nó. Để không vi phạm quy tắc một định nghĩa, **mặc định các
biến** ``const`` **chỉ có ý nghĩa cục bộ trong tệp**. Các biến ``const`` toàn
cục định nghĩa ở các tệp khác nhau là khác nhau, ngay cả khi chúng có cùng tên.

**Để chia sẻ đối tượng** ``const`` **giữa các tệp, chúng ta dùng thêm từ khoá**
``extern`` **cho cả khai báo cũng như định nghĩa của nó**. Chúng ta thường muốn
làm điều này khi biến ``const`` có phần khởi tạo không phải biểu thức hằng và
không muốn trình dịch sinh ra các biến tách rời ở các tệp khác nhau mà muốn tất
cả các tệp dùng chung một biến (như các biến không ``const``).

.. sourcecode:: cpp

    // file_A.cpp định nghĩa biến có thể truy cập được từ các tệp khác
    extern const int bufferSize = getGlobalBufferSize();

    // file_X.h, include vào các tệp cần dùng bufferSize trong file_A.cpp
    // tệp này rất nên được include cả vào file_A.cpp để kiểm tra lỗi
    extern const int bufferSize;


``const`` cấp cao nhất và ``const`` cấp thấp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
``const`` cấp cao nhất cho biết chính đối tượng được định nghĩa là ``const``.
``const`` cấp cao nhất có thể xuất hiện ở mọi kiểu đối tượng.

``const`` không phải ``const`` cấp cao nhất được gọi là ``const`` cấp thấp.
``const`` cấp thấp xuất hiện ở kiểu cơ sở của các kiểu phức hợp như con trỏ hay
tham chiếu.

Sự phân biệt giữa ``const`` cấp cao nhất và ``const`` cấp thấp được thể hiện khi
chúng ta sao chép đối tượng. Khi sao chép đối tượng, ``const`` cấp cao nhất bị
bỏ qua, ``const`` cấp thấp không bao giờ bị bỏ qua.

.. sourcecode:: cpp

    const int a = 42;  // const cấp cao nhất
    int b = a;         // OK, const cấp cao nhất bị bỏ qua

    const int* pa = &a;   // const cấp thấp
    const int* pa2 = pa;  // OK, const cấp thấp khớp nhau
    int* pa3 = pa;        // lỗi, không thể loại bỏ const cấp thấp

    int* pb = &b;
    const int* pb2 = pb;  // OK, có thể chuyển đổi tự động từ int* sang const int*

    int& ra = a;  // lỗi, không thể loại bỏ const cấp thấp để gắn int& vào const int
    const int& rb = b;  // OK, có thể gắn const int& vào int


``constexpr`` và biểu thức hằng
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Biểu thức hằng là biểu thức có giá trị không thể thay đổi và có thể tính được
tại thời điểm dịch, chẳng hạn một literal, một đối tượng ``const`` được khởi tạo
từ một biểu thức hằng khác.

.. sourcecode:: cpp

    const int minLength = 10;          // minLength là biểu thức hằng
    const int limit = minLength + 5;   // limit cũng là biểu thức hằng
    int age = 20;                      // age KHÔNG phải là biểu thức hằng
    const int size = getBufferSize();  // size KHÔNG phải là biểu thức hằng


Biến ``constexpr``
------------------
Trong C++11, ta có thể yêu cầu trình dịch xác nhận một biến là biểu thức hằng
với từ khoá ``constexpr``. ``constexpr`` được ngầm định ``const`` *cấp cao nhất*
được áp dụng lên biến và biến đó phải được khởi tạo bởi biểu thức hằng. Thông
thường, việc dùng ``constexpr`` để khai báo các biến định sử dụng như những biểu
thức hằng là một điều nên làm. Một hàm cũng có thể được khai báo là
``constexpr`` nếu nó thoả mãn một số điều kiện để trình dịch có thể tính được
giá trị của nó ngay tại lúc dịch.


Kiểu literal
------------
Các kiểu có thể sử dụng được trong ``constexpr`` được gọi là kiểu literal vì
chúng đủ đơn giản để có giá trị literal.

Con trỏ ``constexpr`` có thể được khởi tạo từ các literal ``nullptr`` hoặc
``0``. Chúng ta cũng chỉ có thể trỏ hoặc gắn tham chiếu tới các đối tượng có địa
chỉ cố định.

Biến không ``static`` định nghĩa bên trong thân hàm không có địa chỉ cố định. Do
đó con trỏ ``constexpr`` không thể trỏ tới chúng. Địa chỉ của các đối tượng nằm
ngoài hàm và các biến ``static`` là biểu thức hằng và có thể được dùng để khởi
tạo con trỏ ``constexpr`` cũng như có thể gắn các tham chiếu ``constexpr`` vào
các biến đó.


Thao tác với kiểu
*****************

Tên khác cho kiểu
~~~~~~~~~~~~~~~~~
Các khai báo sử dụng tên khác cho kiểu (type alias) để thể hiện một kiểu phức
hợp cùng với ``const`` có thể dẫn đến kết quả không mong đợi.

.. sourcecode:: cpp

    typedef char* pstr;
    const pstr p1 = nullptr;


Có khả năng cao là khai báo của ``p1`` được nhiều người hiểu thành:

.. sourcecode:: cpp

    const char* p1 = nullptr;  // p1 là con trỏ trỏ tới const char (SAI)


bằng cách thay ``pstr`` bởi ``char*``. Tuy nhiên cách hiểu trên là sai.

Kiểu cơ sở trong khai báo trên là ``const pstr``. ``const`` xuất hiện trong kiểu
cơ sở làm thay đổi kiểu được cho. ``pstr`` ở đây là kiểu "con trỏ tới ``char``",
do đó ``const pstr`` là kiểu "hằng con trỏ trỏ tới ``char``", chứ không phải là
"con trỏ tới ``const char``". Khi được viết lại như cách hiểu sai, kiểu cơ sở
của khai báo bị thay đổi thành ``const char`` và ``*`` chỉ là một phần của phần
khai báo (declarator).


Chỉ định kiểu ``auto``
~~~~~~~~~~~~~~~~~~~~~~
Kiểu mà trình dịch xác định cho ``auto`` không luôn luôn chính xác là kiểu của
phần khởi tạo. ``auto`` bỏ qua ``const`` cấp cao nhất và tham chiếu.

.. sourcecode:: cpp

    int        i   = 42;
    int&       ri  = i;
    const int  ci  = 24;
    const int& rci = ci;

    auto a = ci;   // int, bỏ qua const cấp cao nhất
    auto b = ri;   // int, bỏ qua tham chiếu

    auto c = rci;  // const int, const cấp thấp không bị bỏ qua

    const auto  d = ci;  // const int
    auto&       e = i;   // int&
    auto&       f = ci;  // const int&, const cấp thấp không bị bỏ qua
    const auto& g = i;   // const int&


``auto`` và ``auto*`` có thể thay thế cho nhau trong hầu hết các trường hợp
khi định nghĩa con trỏ, trừ khi có cv-qualifier:

.. sourcecode:: cpp

    int i = 42;
    const auto  p1 = &i;  // p1 có kiểu int* const
    const auto* p2 = &i;  // p2 có kiểu const int*


Khi định nghĩa mảng, ta cần chỉ định rõ kiểu. ``auto`` không suy luận được
kiểu mảng từ danh sách các initializer.


Chỉ định kiểu ``decltype``
~~~~~~~~~~~~~~~~~~~~~~~~~~
``decltype`` trả về kiểu của toán hạng nhưng *không tính* toán hạng đó.

Khác với ``auto``, khi áp ``decltype`` lên biến, kiểu trả về là kiểu của biến
đó, bao gồm cả ``const`` cấp cao nhất và tham chiếu. ``decltype`` là trường hợp
duy nhất mà tham chiếu không được coi là đồng nhất với đối tượng được tham
chiếu.

Khi áp dụng ``decltype`` lên biểu thức không phải là biến và biểu thức đó cho
kết quả là lvalue, kiểu thu được là kiểu tham chiếu.

.. sourcecode:: cpp

    int i = 42;
    int* p = &i;

    decltype(*p)  a;  // lỗi, a có kiểu int& và phải được khởi tạo

    decltype(i)   b;  // b có kiểu int
    decltype((i)) c;  // lỗi, c có kiểu int& và phải được khởi tạo


Chú ý rằng ``decltype((variable))`` luôn cho kiểu tham chiếu, còn
``decltype(variable)`` chỉ cho kiểu tham chiếu nếu ``variable`` là tham chiếu.

``decltype`` cũng thể hiện sự khác biệt với ``auto`` khi áp dụng với mảng.

.. sourcecode:: cpp

    int[5] a;
    auto p(a);      // p có kiểu int*
    decltype(a) b;  // b có kiểu int[5];

