********
Câu lệnh
********

.. contents:: Mục lục

Lệnh ``switch``
===============
Các nhãn ``case`` phải là biểu thức hằng nguyên.

Nhảy từ vị trí mà một biến *có phần khởi tạo* đang ở ngoài phạm vi vào phạm
vi của biến đó là bất hợp lệ.


Lệnh ``goto``
=============
Tương tự ``switch``, ``goto`` không thể nhảy qua thao tác khởi tạo. Tuy nhiên
nó có thể nhảy ngược về trước một định nghĩa. Khi đó biến bị huỷ và được tạo
lại.


Xử lí ngoại lệ
==============
Trong trường hợp không tìm được handler thích hợp cho ngoại lệ, luồng thực
thi được chuyển tới một hàm thư viện là ``terminate``. Hành vi của hàm này
tuỳ thuộc vào hệ thống, nhưng nó được đảm bảo là sẽ dừng thực thi chương trình.

Một chương trình thực hiện các thao tác dọn dẹp cần thiết khi gặp ngoại lệ
được gọi là an toàn ngoại lệ.
