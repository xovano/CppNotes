****
Mảng
****

.. contents:: Mục lục

Định nghĩa mảng
===============
Xem chú ý về mảng ở phần chỉ định kiểu auto_ và decltype_.

.. _auto: VarsAndBasicTypes.rst#chi-dinh-kieu-auto
.. _decltype: VarsAndBasicTypes.rst#chi-dinh-kieu-decltype

Con trỏ và mảng
===============

Mặc dù chỉ số mảng nên được định nghĩa với kiểu không dấu ``size_t`` trong
tiêu đề ``cstddef``, đánh chỉ số âm với con trỏ đang trỏ vào mảng (con trỏ
này có thể chính là tên mảng) là hợp lệ nếu không có truy cập ngoài biên. Đó
là do toán tử đánh chỉ số dựng sẵn không ràng buộc kiểu của chỉ số phải là
không dấu như đối với ``string`` hay ``vector``.

Một tính chất đặc biệt của mảng là ta có thể lấy địa chỉ của phần tử (không
tồn tại) ngay sau phần tử cuối.

.. sourcecode:: cpp

    int a[] = {1, 2, 3};
    int* p = &a[3];  // hợp lệ
    a[3] = 4;        // không xác định


Thao tác hợp lệ duy nhất mà ta có thể làm với phần tử này là lấy địa chỉ của
nó.

Xem thêm về các trạng thái của con trỏ.


Mảng nhiều chiều
================

Khởi tạo mảng nhiều chiều
~~~~~~~~~~~~~~~~~~~~~~~~~
Các dấu ngoặc nhọn lồng nhau cho phần khởi tạo của mảng nhiều chiều là tuỳ
chọn:

.. sourcecode:: cpp

    int a[3][3] = {1, 2, 3, 4, 5, 6, 7};


là tương đương với

.. sourcecode:: cpp

    int a[3][3] = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 0, 0},
    }


Tuy nhiên nếu không cung cấp kích thước chiều thứ nhất và bỏ qua cặp ngoặc,
trình dịch có thể xác định số chiều không như mong muốn.


Sử dụng vòng lặp ``for`` dựa trên khoảng với mảng nhiều chiều
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Để sử dụng mảng nhiều chiều trong vòng lặp ``for`` dựa trên khoảng, biến
điều khiển cho tất cả các chiều (ngoại trừ chiều trong cùng) phải là tham
chiếu. Làm vậy để tránh việc mảng bị chuyển đổi thành con trỏ.

Xét ví dụ sau:

.. sourcecode:: cpp

    int arr2d[3][3];
    // điền đầy mảng arr2d

    for (auto row : arr2d)  // Sai, row có kiểu int*
        for (auto elem : row)
            std::cout << elem << std::endl;


Đoạn mã trên sẽ không dịch được. Khi ``row`` được khởi tạo ở vòng lặp phía
ngoài, mã khởi tạo sẽ chuyển đổi mỗi mảng con (kiểu ``int[3]``) thành con
trỏ tới phần tử đầu tiên của nó. Kết quả là ``row`` có kiểu ``int*`` và vòng
lặp phía trong là bất hợp lệ. Hãy nhớ rằng trong C++ một mảng không thể được
khởi tạo bằng cách copy một mảng khác, do đó ``row`` không thể là mảng và có
kiểu ``int[3]`` được.

Cách viết đúng cho đoạn mã trên:

.. sourcecode:: cpp

    for (const auto& row : arr2d)
        for (auto elem : row)
            std::cout << elem << std::endl;


