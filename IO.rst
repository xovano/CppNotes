Vào ra
######

Ghi ra stream
*************
Các lệnh in để debug nên flush stream. Nếu không làm vậy, khi chương trình
crash, output có thể bị giữ lại trên buffer mà không được in ra, dẫn đến
kết luận sai về điểm crash.
