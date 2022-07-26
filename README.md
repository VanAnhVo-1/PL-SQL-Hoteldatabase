# PL-SQL Hoteldatabase
## Introduction
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
    - 
