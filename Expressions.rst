*********
Biểu thức
*********

.. contents:: Mục lục

Căn bản
=======

Lvalue và Rvalue
~~~~~~~~~~~~~~~~
Mọi biểu thức trong C++ đều là rvalue hoặc lvalue.

Hiểu một cách đơn giản, khi một đối tượng được dùng như rvalue, chúng ta sử
dụng giá trị (nội dung) của đối tượng đó, còn khi đối tượng được dùng như
lvalue, chúng ta sử dụng vị trí của đối tượng đó trong bộ nhớ.

Rvalue không thể được sử dụng tại các vị trí yêu cầu lvalue. Trong hầu hết
các trường hợp (không phải tất cả), chúng ta có thể sử dụng lvalue tại vị
trí yêu cầu rvalue.

Thứ tự tính toán
~~~~~~~~~~~~~~~~
Đối với các toán tử không chỉ rõ thứ tự tính toán, biểu thức là không xác
định nếu nó đọc và thay đổi cùng một đối tượng.

.. sourcecode:: cpp

    int i = 0;
    std::cout << i << " " << ++i << std::endl;  // không xác định


Đoạn mã trên không xác định vì không thể xác định được thứ tự tính toán
trước sau giữa hai biểu thức ``i`` và ``++i``.

Có 4 toán tử đảm bảo thứ tự tính toán của các toán hạng là:

* Toán tử AND logic (``&&``).
* Toán tử OR logic (``||``).
* Toán tử điều kiện (``? :``).
* Toán tử phảy (``,``).

Khi viết các biểu thức phức hợp, có hai quy tắc nên nhớ:

#. Khi không chắc chắn, dùng ngoặc đơn để bắt buộc cách nhóm toán hạng cần
   thiết.
#. Nếu một toán hạng bị thay đổi giá trị trong biểu thức, đừng dùng toán
   hạng đó ở vị trí khác trong cùng biểu thức.


Chuyển đổi kiểu
===============

Chuyển đổi số học
~~~~~~~~~~~~~~~~~

Nâng kiểu nguyên
----------------
Nâng kiểu nguyên (integral promotion) chuyển đổi kiểu nguyên nhỏ sang kiểu
nguyên lớn hơn.

Các kiểu ``bool``, ``char``, ``signed char``, ``unsigned char``, ``short``,
``unsigned short`` được nâng lên kiểu ``int`` nếu tất cả giá trị của kiểu
đó đều thể hiện được trong kiểu ``int``. Nếu không, các giá trị đó sẽ được
nâng lên kiểu ``unsigned int``.

Các kiểu kí tự lớn hơn (``wchar_t``, ``char16_t`` và ``char32_t``) được
nâng lên kiểu nhỏ nhất có thể biểu diễn được tất cả các giá trị của kiểu
kí tự đó trong danh sách: ``int``, ``unsigned int``, ``long``, ``unsigned
long``, ``long long`` và ``unsigned long long``.


Toán hạng không dấu
-------------------
Nếu các toán hạng của một toán tử có kiểu khác nhau, chúng sẽ được chuyển đổi
sang một kiểu chung. Nếu có toán hạng không dấu, kiểu chung này tuỳ thuộc vào
kích thước tương đối giữa các kiểu nguyên trên máy.

Đầu tiên, các toán hạng được nâng kiểu. Nếu kiểu kết quả khớp nhau thì không
cần thực hiện thêm chuyển đổi nào nữa. Nếu cả hai toán hạng sau khi nâng kiểu
cùng là có dấu hoặc không dấu, toán hạng có kiểu nhỏ hơn được chuyển đổi lên
kiểu lớn hơn.

Nếu tính chất dấu khác nhau và kiểu không dấu bằng hoặc lớn hơn kiểu có dấu,
toán tử có dấu được chuyển đổi sang kiểu không dấu. Nếu kiểu có dấu lớn hơn
kiểu không dấu, kết quả phục thuộc máy. Nếu tất cả giá trị của kiểu không dấu
đều biểu diễn được trong kiểu có dấu, toán tử không dấu được chuyển đổi sang
kiểu có dấu. Ngược lại, toán tử có dấu được chuyển đổi sang kiểu không dấu.


Các chuyển đổi ẩn khác
~~~~~~~~~~~~~~~~~~~~~~
Chuyển đổi từ mảng thành con trỏ không xảy ra khi mảng được sử dụng với
``decltype`` hoặc khi mảng là toán hạng của các toán tử lấy địa chỉ (``&``),
``sizeof`` và ``typeid``. Chuyển đổi này cũng không xảy ra khi khởi tạo một
tham chiếu từ mảng.


Độ ưu tiên của các toán tử
==========================

+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| Toán tử                   | Ý nghĩa                           | Cách dùng                                              | Tính kết hợp |
+===========================+===================================+========================================================+==============+
| ``::``                    | Định phạm vi                      | | ``::``\ *tên*                                        | Trái         |
|                           |                                   | | *lớp*\ ``::``\ *tên*                                 |              |
|                           |                                   | | *không_gian_tên* \``::`` \*tên*                      |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``.``                   | | Chọn thành viên                 | | *đối_tượng*\ ``.``\ *thành_viên*                     | Trái         |
| | ``->``                  | | Chọn thành viên                 | | *con_trỏ*\ ``->``\ *thành_viên*                      |              |
| | ``[]``                  | | Đánh chỉ số                     | | *biểu_thức*\ ``[``\ *biểu_thức*\ ``]``               |              |
| | ``()``                  | | Gọi hàm                         | | *tên*\ ``(``\ *danh_sách_biểu_thức*\ ``)``           |              |
| | ``()``                  | | Dựng kiểu                       | | *kiểu*\ ``(``\ *danh_sách_biểu_thức*\ ``)``          |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``++``                  | | Tăng hậu tố                     | | *lvalue*\ ``++``                                     | Phải         |
| | ``--``                  | | Giảm hậu tố                     | | *lvalue*\ ``--``                                     |              |
| | ``typeid``              | | Định danh (ID) kiểu             | | ``typeid(``\ *kiểu*\ ``)``                           |              |
| | ``typeid``              | | ID kiểu runtime                 | | ``typeid(``\ *biểu_thức*\ ``)``                      |              |
| | ép kiểu hiện            | | Chuyển đổi kiểu                 | | *cách_ép*\ ``<``\ *kiểu*\ ``>(``\ *biểu_thức*\ ``)`` |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``++``                  | | Tăng tiền tố                    | | ``++``\ *lvalue*                                     | Phải         |
| | ``--``                  | | Giảm tiền tố                    | | ``--``\ *lvalue*                                     |              |
| | ``~``                   | | NOT theo bit (đảo bit)          | | ``~``\ *biểu_thức*                                   |              |
| | ``!``                   | | NOT logic                       | | ``!``\ *biểu_thức*                                   |              |
| | ``-``                   | | Trừ một ngôi                    | | ``-``\ *biểu_thức*                                   |              |
| | ``+``                   | | Cộng một ngôi                   | | ``+``\ *biểu_thức*                                   |              |
| | ``*``                   | | Khử tham chiếu                  | | ``*``\ *biểu_thức*                                   |              |
| | ``&``                   | | Lấy địa chỉ                     | | ``&``\ *lvalue*                                      |              |
| | ``()``                  | | Chuyển đổi kiểu                 | | ``(``\ *kiểu*\ ``)``\ *biểu_thức*                    |              |
| | ``sizeof``              | | Kích thước đối tượng            | | ``sizeof`` *biểu thức*                               |              |
| | ``sizeof``              | | Kích thước kiểu                 | | ``sizeof(``\ *kiểu*\ ``)``                           |              |
| | ``sizeof...``           | | Kích thước gói tham số          | | ``sizeof...(``\ *tên*\ ``)``                         |              |
| | ``new``                 | | Định phần đối tượng             | | ``new`` *kiểu*                                       |              |
| | ``new[]``               | | Định phần mảng                  | | ``new`` *kiểu*\ ``[``\ *kích_thước*\ ``]``           |              |
| | ``delete``              | | Giải phóng đối tượng            | | ``delete`` *biểu_thức*                               |              |
| | ``delete[]``            | | Giải phóng mảng                 | | ``delete[]`` *biểu_thức*                             |              |
| | ``noexcept``            | | Biểu thức có ném ngoại lệ không | | ``noexcept(``\ *biểu_thức*\ ``)``                    |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``->*``                 | Chọn thành viên trỏ bởi con trỏ   | | *con_trỏ*\ ``->*``\ *con_trỏ_tới_thành_viên*         | Trái         |
| | ``.*``                  |                                   | | *đối_tượng*\ ``.*``\ *con_trỏ_tới_thành_viên*        |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``*``                   | | Nhân                            | | *biểu_thức* ``*`` *biểu_thức*                        | Trái         |
| | ``/``                   | | Chia                            | | *biểu_thức* ``/`` *biểu_thức*                        |              |
| | ``%``                   | | Lấy phần dư                     | | *biểu_thức* ``%`` *biểu_thức*                        |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``+``                   | | Cộng                            | | *biểu_thức* ``+`` *biểu_thức*                        | Trái         |
| | ``-``                   | | Trừ                             | | *biểu_thức* ``-`` *biểu_thức*                        |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``<<``                  | | Dịch trái bit                   | | *biểu_thức* ``<<`` *biểu_thức*                       | Trái         |
| | ``>>``                  | | Dịch phải bit                   | | *biểu_thức* ``>>`` *biểu_thức*                       |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``<``                   | | Nhỏ hơn                         | | *biểu_thức* ``<`` *biểu_thức*                        | Trái         |
| | ``<=``                  | | Nhỏ hơn hoặc bằng               | | *biểu_thức* ``<=`` *biểu_thức*                       |              |
| | ``>``                   | | Lớn hơn                         | | *biểu_thức* ``>`` *biểu_thức*                        |              |
| | ``>=``                  | | Lớn hơn hoặc bằng               | | *biểu_thức* ``>=`` *biểu_thức*                       |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``==``                  | | Bằng                            | | *biểu_thức* ``==`` *biểu_thức*                       | Trái         |
| | ``!=``                  | | Khác                            | | *biểu_thức* ``!=`` *biểu_thức*                       |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``&``                     | AND theo bit                      | *biểu_thức* ``&`` *biểu_thức*                          | Trái         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``^``                     | XOR theo bit                      | *biểu_thức* ``^`` *biểu_thức*                          | Trái         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``|``                     | OR theo bit                       | *biểu_thức* ``|`` *biểu_thức*                          | Trái         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``&&``                    | AND logic                         | *biểu_thức* ``&&`` *biểu_thức*                         | Trái         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``||``                    | OR logic                          | *biểu_thức* ``||`` *biểu_thức*                         | Trái         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``?:``                    | Điều kiện                         | *biểu_thức* ``?`` *biểu_thức* ``:`` *biểu_thức*        | Phải         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| | ``=``                   | | Gán                             | | *lvalue* ``=`` *biểu_thức*                           | Phải         |
| | ``*=``, ``/=``, ``%=``, | | Gán phức hợp                    | | *lvalue* ``*=`` *biểu_thức*                          |              |
| | ``+=``, ``-=``,         | |                                 | | ...                                                  |              |
| | ``<<=``, ``>>=``,       | |                                 | |                                                      |              |
| | ``&=``, ``|=``, ``^=``  | |                                 | |                                                      |              |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``throw``                 | Ném ngoại lệ                      | ``throw`` *biểu_thức*                                  | Phải         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+
| ``,``                     | Phảy                              | *biểu_thức*\ ``,`` *biểu_thức*                         | Trái         |
+---------------------------+-----------------------------------+--------------------------------------------------------+--------------+


Các toán tử số học
==================
Các toán tử số học bao gồm ``+`` (một ngôi và hai ngôi), ``-`` (một ngôi và
hai ngôi), ``*``, ``/``, ``%``.

Các toán tử này đều kết hợp trái.

Khi áp lên toán hạng là con trỏ hoặc một giá trị số học, toán tử cộng một
ngôi cho kết quả là bản sao của giá trị (có thể đã được nâng kiểu) của toán
hạng đó.

Trong phép chia, thương số khác không mang giá trị dương nếu các toán hạng
cùng dấu và âm nếu ngược lại. Trước C++11, chuẩn cho phép thương số âm trong
phép chia số nguyên được làm tròn lên hoặc xuống. Kể từ C++11, thương số âm
trong phép chia số nguyên phải được làm tròn về phía 0 (nói cách khác là cắt
bỏ phần sau dấu phảy sau khi thực hiện phép chia).

Toán tử lấy phần dư được định nghĩa sao cho với hai số nguyên ``m`` và ``n``,
``n`` khác không thì ``(m/n)*n + m%n == m``. Hệ quả là nếu ``m % n`` khác
không, nó sẽ có cùng dấu với ``m``.

Trước C++11, chuẩn cho phép ``m % n`` có cùng dấu với ``n`` trên các
implementation có ``m / n`` được làm tròn ra xa phía 0. Kể từ C++11, các
implementation như vậy bị cấm. Hơn nữa, ngoại trừ trường hợp đặc biệt mà
``-m`` bị tràn số, ``(-m) / n`` và ``m / (-n)`` luôn bằng ``-(m / n)``,
``m % (-n)`` bằng ``m % n``, ``(-m) % n`` bằng ``-(m % n)``.

Các biểu thức sau có kết quả là ``true``:

.. sourcecode:: cpp

    -21 / -8 ==  2; -21 % -8 == -5;
    21  / -5 == -4;  21 % -5 ==  1;


Toán hạng và kết quả của các toán tử số học đều là rvalue.


Các toán tử logic và quan hệ
============================
Các toán tử logic bao gồm: ``!``, ``&&``, ``||``. Các toán tử quan hệ bao
gồm: ``==``, ``!=``, ``<``, ``<=``, ``>``, ``>=``.

Toán hạng và kết quả của các toán tử này đều là rvalue.

Một điều cần lưu ý khi sử dụng các toán tử ``==`` và ``!=`` là nên tránh
dùng chúng để kiểm tra tính bằng nhau với các ``bool`` literal. Xét đoạn mã
sau:

.. sourcecode:: cpp

    if (val)          // (1)
        // ...

    if (val == true)  // (2)
        // ...


Cách viết (2) không những dài hơn mà còn có thể không hoạt động như mong
muốn vì nếu ``val`` không có kiểu ``bool``, ``true`` sẽ được chuyển đổi sang
kiểu của ``val``. Việc kiểm tra giá trị trân lí bằng cách so sánh với
``true`` và ``false`` thường không phải là ý hay. Chỉ nên so sánh các
literal này với các đối tượng kiểu ``bool``.


Toán tử gán
===========
Kết quả của phép gán là toán tử ở vế trái và là lvalue.

Toán tử gán có tính kết hợp phải.

Toán tử gán có độ ưu tiên thấp hơn các toán tử quan hệ nên thường cần dùng
thêm ngoặc đơn quanh phép gán dùng bên trong một biểu thức điều kiện.

.. sourcecode:: cpp

    int i;
    while ((i = getValue()) != 42)  // đúng
        // do something

    while (i = getValue() != 42)  // sai, i nhận giá trị 1 hoặc 0
        // do something


Toán tử tăng và giảm
====================
Dạng tiền tố của toán tử tăng và giảm trả về đối tượng bị tăng (giảm) và là
lvalue. Dạng hậu tố của các toán tử này trả về bản sao của giá trị ban đầu
của đối tượng và là rvalue.


Toán tử truy cập thành viên
===========================
Toán tử ``->`` cho kết quả là lvalue.

Toán tử ``.`` cho kết quả là lvalue nếu đối tượng có thành viên được truy
cập là lvalue, và cho kết quả là rvalue nếu ngược lại.


Toán tử điều kiện
=================
Toán tử điều kiện có dạng *điều_kiện* ``?`` *biểu_thức_1* ``:`` *biểu_thức_2*
với *điều_kiện* là một biểu thức có thể được sử dụng như một điều kiện đúng
sai, *biểu_thức_1* và *biểu_thức_2* là các biểu thức cùng kiểu hoặc có kiểu
có thể chuyển đổi về một kiểu chung.

Toán tử điều kiện đảm bảo rằng *điều_kiện* được tính trước và chỉ có một
trong hai biểu thức *biểu_thức_1* và *biểu_thức_2* được tính.

Kết quả của toán tử điều kiện là lvalue nếu cả *biểu_thức_1* và *biểu_thức_2*
là lvalue hoặc nếu chúng có thể chuyển đổi được thành một kiểu lvalue chung.
Trong các trường hợp khác kết quả là rvalue.

Toán tử điều kiện có tính kết hợp phải.

Toán tử điều kiện có độ ưu tiên khá thấp, do đó ta có thể gặp phải kết quả
không mong đợi nếu không dùng thêm các dấu ngoặc.

.. sourcecode:: cpp

    std::cout << ((grade < 4) ? "fail" : "pass");  // in ra fail hoặc pass
    std::cout << (grade < 4) ? "fail" : "pass";    // in ra 1 hoặc 0
    std::cout << grade < 4 ? "fail" : "pass";      // lỗi, so sánh std::cout với 4


Dòng 2 tương đương với ``(std::cout << (grade < 4)) ? "fail" : "pass";``.
Dòng 3 tương đương với ``((std::cout << grade) < 4) ? "fail" : "pass";``.


Các toán tử trên bit
====================
Các toán tử trên bit bao gồm: ``~``, ``<<``, ``>>``, ``&``, ``^``, ``|``.

Nếu toán hạng có kiểu có dấu và mang giá trị âm, cách mà bit dấu được xử lí
lệ thuộc máy. Tốt nhất là dùng kiểu không dấu với các toán tử trên bit.

Đối với các toán tử dịch bit, toán hạng bên phải phải không âm và có giá trị
nhỏ hơn số bit có trong kết quả. Chú ý rằng kết quả dịch bit là bản sao *có
thể đã nâng kiểu* của toán hạng bên trái với các bit đã được dịch đi.

Vì vậy biểu thức ``1 << 27`` là không an toàn do kiểu ``int`` chỉ đảm bảo có
ít nhất 16 bit (và là kiểu có dấu), còn biểu thức ``1uL << 27`` đảm bảo sẽ
cho giá trị có duy nhất bit số 27 được bật.


Toán tử ``sizeof``
==================
Chú ý rằng toán tử ``sizeof`` không tính toán hạng của nó và giá trị trả về
của nó là một biểu thức hằng.


Toán tử phảy
============
Toán tử phảy đảm bảo rằng thứ tự tính toán các toán hạng là từ phải qua trái.

Kết quả của toán tử phảy là lvalue nếu toán tử bên phải là lvalue.

