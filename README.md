# PL-SQL Hoteldatabase
## Introduction
This is project, my partners and I using Oracle DBMS to Database Management System (DBMS) Project.
## Modeling database
  - Physical diagram
![image](https://user-images.githubusercontent.com/72924182/180970610-f2383220-3af1-43d0-87b7-606f074961d9.png)
  - Entities in model:
    - Type of account: code, name and description for each type of account - LOAITAIKHOAN TABLE
    - Employee: code, code of type account, code of manager, employee name, date of birth, gender, identification card, address, phone number, email, start date of work, name of account, password - NHANVIEN TABLE.
    - Room: code, code of type room, status - PHONG TABLE.
    - Customer: code, name, date of birth, identification card, address, phone number, email, gender - KHACHHHANG TABLE
    - Services: code, name, price, status - DICHVU TABLE.
    - Dishes: code, name, price, status - MONAN TABLE.
    - Infrastructures: code, name, price - CSVC TABLE.
    - check-in: code, customer code, room code, employee code, number of people accompanying, arrival-date, left-date - PHIEUNHANPHONG TABLE.
    - Reservation: code, customer code, room code, employee code, arrival-date, left-date - PHIEUDATTRUOC TABLE.
    - holiday: day, discount. - NGAYLE TABLE.
    - receipt: code, employee code, check-in code, room charge, services charge, food money, compensation money, discount, arrival-date, left-date, total - HOADONTHANHTOAN.
    - service detail sheet: code, receipt code, create date, total - BANGKE_DV
    - dish detail sheet: code, receipt code, create date, quantities, total - BANGKE_MONAN
    - facilities compensation sheet: code, receipt code, quantity, total - HOADON_CSVC.
## TRIGGER
|Name|Action|Table|Desciprtion|
| --- | --- | --- | --- |
|TRIG_INSERT_PHIEUDATTRUOC|INSERT|PHIEUDATTRUOC|Check if the reservation ticket exists in the system or not|
|TRIG_BK_DV_NGAYSD| INSERT| BANGKE_DV| Check if the service date is within the period of your stay at the hotel.|
|TRIG_BK_MONAN_NGAYSD|INSERT|BANGKE_MONAN| Check if the dish date is within the period of your stay at the hotel/|
|TRIG_DATTRUOC_NGAY|INSERT|PHIEUDATTRUOC|Check if the arrival date and left date in reservation is valid or not.|
|TRIG_DELETE_PHIEUNHANPHONG|DELETE|PHIEUNHANPHONG|Delete receipts before delete check-in|
|TRIG_HOADON_NGAY|INSERT,UPDATE|HOADONTHANHTOAN|Check if the arrival date and left date in receipt is valid or not.|
|TRIG_INSERT_PHIEUNHANPHONG|INSERT|PHIEUNHANPHONG| Check if the check-in or reservation is exsit or not with input data|
|TRIG_NV_NGAYVAOLAM|INSERT,UPDATE|NHANVIEN|Check if the start date of work is valid or not.|
|TRIG_PHIEUNHAN_NGAY|INSERT,UPDATE|PHIEUNHANPHONG|Check if the arrival date and left date in receipt is valid or not.|
|TRIG_PNP_TAOHD|INSERT|PHIEUNHANPHONG|Automatic create receipt with check-in code created.|
|TRIG_PNP_UPDATE_HD|UPDATE|PHIEUNHANPHONG|Automatic update receipt with check-in code.|
|TRIG_TUOI|INSER,UPDATE|NHANVIEN|Check age > 18 or not.|
|TRIG_UPDATE_TINHTRANG_PHONG|UPDATE|PHONG| Lock room code if status of room is "repairing" "SỬA CHỮA".|
|TRIG_PHIEUNHAN_NGDIKEM|INSERT,UPDATE|PHIEUNHANPHONG|Check the number of accompanying people <= maximum number of people for the room type.|
|TRIG_LOAIPHONG_GIA|INSERT,UPDATE|LOAIPHONG|Check the price of the room type must be lower than the price of a higher room type.|
## STORED PROCEDURE
|Name|Input parameters|Output parameters| Meaning|
| --- | --- | --- | --- |
|PRO_UPDATE_PHIEUDATTRUOC|MAPHIEU_IN INT<br />MAPHONG_IN INT<br />MAKH_IN INT<br />MANV_IN INT<br />NGAYDEN_IN DATE<br />NGAYROI_IN DATE<br />SONGUOIDIKEM_IN INT<br />TINHTRANG_IN PHIEUDATTRUOC.TINHTRANG%TYPE||Update information of reservation.|
|PRO_UPDATE_PHIEUNHANPHONG|MAPHIEUNHAN_IN INT<br/>MAKH_IN INT<br/>MAPHONG_IN INT<br />SONGUOIDIKEM_IN INT<br/>NGAYDEN_IN DATE<br />NGAYROI_IN DATE<br/>||Update information of check-in.|
|PROD_HOADONTHANHTOAN_TONGTIEN|MA_HD INT||Caculate total money of receipt.|
|PROD_BK_MONAN_THANHTIEN|MA_HD INT<br/>MA_MON INT<br/>NGAY_SD DATE||Caculate money of dish sheet.|
|SLEEP|IN_TIME NUMBER|||
|FUNC_CHECK_PHONGTRONG|MAPHONG_IN INT<br/>NGAYDEN_IN DATE<br/>NGAYROI_IN DATE<br/>MAPHIEU_IN INT|CHECK_PHONGTRONG NUMBER|Check room is available or not.|
|FUNC_HOADON_TIENPHONG|MA_HD HOADONTHANHTOAN.MAHD%TYPE||Caculate room charge.|
|FUNC_HOADON_DICHVU|MA_HD HOADONTHANHTOAN.MAHD%TYPE
