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
|FUNC_HOADON_DICHVU|MA_HD HOADONTHANHTOAN.MAHD%TYPE|HOADONTHANHTOAN.TIENDICHVU%TYPE|Caculate services charge.|
|FUNC_HOADON_MONAN|MAHD HOADONTHANHTOAN.MAHD%TYPE|HOADONTHANHTOAN.TIENMONAN%TYPE|Caculate food money.|
|FUNC_HOADON_DENBU|MAHD HOADONTHANHTOAN.MAHD%TYPE|HOADONTHANHTOAN.TIENDENBU%TYPE|Caculate compensation money.|
|FUNC_HOADON_TIENGIAM|MAHD HOADONTHANHTOAN.MAHD%TYPE|HOADONTHANHTOAN.TIENGIAM%TYPE|Caculate discount.|
## LOST UPDATE, NON-REPEATABLE READ, PHANTOM READ, DEADLOCK
### LOST UPDATE
1. Hypothetical situation

    An employee updates the arrival and departure dates for a customer's reservation. For some reason the room that the employee's reservation is updating needs to be repaired, the manager updates the room status to repair and re-updates the previous reservation with an empty room of the same type. When the employee updates and commits the data, the manager also performs the update and closes, leading to the data of the previous update manager being overwritten by the data that the employee updates later and leading to errors.
  
2. Relational Procedures, Trigger
    - SLEEP
    - PRO_UPDATE_PHIEUDATTRUOC
    - TRIG_UPDATE_TINHTRANGPHONG
    - FUNC_RANDOM_PHONG
    
3. Conflicting Writes and Lost Updates in a READ COMMITTED Transaction

|Session 1| Session 2|Explanation|
| --- | --- | --- |
|EXEC PRO_UPDATE_PHIEUDATTRUOC(87,43,20,1,'09-JULY-2020','11-JULY-2020',1,'CHƯA NHẬN');|UPDATE PHONG SET TINHTRANG='SỬA CHỮA' WHERE MAPHONG=43;|While session 1 updates the reservation ticket, session 2 updates the status for room code 43.|
4. Causes and solutions
    - The problem occurs: The data that the employee makes for the reservation is overwritten by the data that the manager updates later. Leads to data errors.
    - Cause: Because the read committed isolation level is set, T2 does not realize that T1 has updated the arrival date of the reservation. T2 takes the old data and updates the reservation again.
    - Solution: Use the statement “SET TRANSACTION ISOLATION LEVEL SERIALIZABLE ” instead of the statement “SET TRANSACTION ISOLATION LEVEL READ COMMITTED” in Session 2 when updating the reservation ticket, the statement will wait for T1 to complete, then T2 will get the data. there to update.
### Non-repeatable read
  1. Hypothetical situation
  
      While processing the bill payment for the customer, the receptionist makes a query for the price of the dish that the customer used to calculate the money for the customer, the manager updates the price of the dish. When checking the payment invoice before delivering it to the customer, the data has changed, leading to an error.
 
 2. Relational Procedures, Triggers:
    - PROD_BK_MONAN_THANHTIEN.
 3. Non-repeatable read transactions
 
 |Session 1|Session 2|Explaination|
 | --- | --- | --- |
 |SELECT GIAMON FROM MONAN WHERE MAMON = 1| No action.| Session 1 executes the query for the price of the dish with the item code = 1.|
 |DECLARE IN_THANHTIEN INT;<br />BEGIN<br />PROD_BK_MONAN_THANHTIEN(144,1,'07-JULY-20',2);<br />SELECT THANHTIEN INTO IN_THANHTIEN FROM BANGKE_MONAN WHERE MAHD = 144 AND MAMON = 1 AND NGAY = '07-JULY-2020';<br />DBMS_OUTPUT.PUT_LINE('THANH TIEN:' \|\| IN_THANHTIEN);<br />COMMIT;<br />END;| UPDATE MONAN SET GIAMON = 120000 WHERE MAMON = 1;|Session 1 calculates money for the list of medium dishes, then Session 2 updates the price of dishes with MAMON = 1 from 100000 --> 120000.|
 |No action.|COMMIT;|Transaction 2 completes and terminates.|
 |COMMIT;| No action.|Transaction 1 is done, prints the output as 240000 and ends. Results is wrong.|
 
 4. Causes and solutions
    - The problem occurs: T1 executes the query for the item price of the dish with the item code = 1, at that moment, T2 performs the update of the dish price for the item code = 1, resulting in T1 getting the price of the dish that T2 updated. to charge for the declared dish list before the item price is updated, resulting in a deviation from the actual list price.
    - Cause: T1 sets the read committed isolation level, so every time SELECT in the same data operation, it will read back data from the database (the database may now be changed) even though the This statement reads on the same data units.
    - Solution: Use “SET TRANSACTION ISOLATION LEVEL SERIALIZABLE” statement instead of “SET TRANSACTION ISOLATION LEVEL READ COMMITTED” statement.
    - Result: The cost of the dish list will be calculated with the original item price and not the updated item price by T2.
### Phantom Read
1. Hypothetical situation
    A customer calls the hotel to book a room, the receptionist makes a list of available rooms with room type code = 4(SUITE), at which time another staff member adds a customer's check-in voucher that has room type code = 4 whose arrival and left dates affect arrival and left dates for customer 1. After employee 1 and customer 1 discussed to come to an agreement on the room code to reserve, employee 1 proceeded to create a reservation form and performed a query for the list of available rooms with room type code = 4 and the list available rooms is change.
    
2. Relational Procedures, Triggers:
    - PRO_DANHSACH_PHONGTRONG
3. Phantom Read Transactions

|Session 1|Session 2|Explaination|
| --- | --- | --- |
|DECALRE PHONGTRONG INT;<br />C SYS_REFCURSOR;<br />BEGIN<br />PRO_DANHSACH_PHONGTRONG('08-JULY-2020','10-JUL-2020',4,C);<br />&nbsp;LOOP<br />&nbsp;&nbsp;FETCH C INTO PHONGTRONG;<br />&nbsp;&nbsp;EXIT WHEN C%NOTFOUND;<br />&nbsp;&nbsp;DBMS_OUTPUT.PUT_LINE('PHONG TRONG: '\|\| PHONGTRONG);<br />&nbsp;END LOOP;<br />&nbsp;CLOSE C;<br />&nbsp;COMMIT<br />END;|No action|Session 1 retrieves the list of available rooms with the arrival date '08/07/2020', the left date is '07/10/2020' with the room type code 4(Suite) to proceed with the announcement of the number of available rooms for client.<br />Results are printed: Phong Trong:47 Phong Trong: 49.|
|No action.|No action.||
|DECLARE PHONGTRONG INT;<br />C SYS_REFCURSOR;<br />BEGIN<br />&nbsp;PRO_DANHSACH_PHONGTRONG('08-JUL-2020','10-JUL-2020',4,C);<br />&nbsp;&nbsp;LOOP<br />&nbsp;&nbsp;&nbsp;FETCH C INTO PHONGTRONG;<br />&nbsp;&nbsp;&nbsp;EXIT WHEN C%NOTFOUND;<br />&nbsp;&nbsp;&nbsp;DBMS_OUTPUT.PUT_LINE('PHONG TRONG:' \|\| PHONGTRONG);<br />&nbsp;&nbsp;END LOOP;<br />&nbsp;&nbsp;CLOSE C;<br />&nbsp;&nbsp;COMMIT;<br />&nbsp;END;|INSERT INTO PHIEUNHANPHONG(MAPHIEU,MAPHONG,MAKH,MANV,<br />NGAYDEN,NGAYROI,SONGUOIDIKEM) VALUES (SEQ_PHIEUDAT.NEXTVAL,49,68,2,<br />'07-JUL-2020','08-JUL-2020',0);|While making a reservation for customer 1, employee 1 waits to load the list of available rooms to choose, staff 2 proceeds to add a check-in for customer 2 and finishes.|
||COMMIT;||
|Output Result: PHONG TRONG: 47||Session 1 executes the command and shows the current list of available rooms, employee 1 notices that the number of rooms has changed.|

4. Causes and solutions 
    - The problem occurs: When the procedure of transaction 1 executes the query for the list of available rooms corresponding to the room type code selected by the customer, the number of rooms available is 2. After discussing and performing the creation of a booking ticket in advance for customers. While waiting for the data of the available room list to be loaded into the combobox for selection, transaction 2 executes a statement to insert a check-in ticket for another customer with the same room type code as the room type code that customer 1 has selected. After completing the data upload to the combobox, the staff found that room 49 was not in the data as previously queried (Reduced the number of available rooms from 2 to 1).
    - Cause: T1 set isolation level read commited, so the second read to load data into combobox will remove the empty room with room code 49 because room 49 has been received by another customer.
    - Solution: Use “SET TRANSACTION ISOLATION LEVEL SERIALIZABLE” statement instead of “SET TRANSACTION ISOLATION LEVEL READ COMMITTED” statement.
    - Result: The second time reading to get the data on the combobox, the list of available rooms will still show the room with the room code 49.
### Deadlock
  1. Hypothetical situation
  
      Employee 1 executes a query for rooms information and requests a lock on the PHONG table that prevents another transaction from requesting a lock on the table. Meanwhile, the manager makes a query for the check-in list and requests a lock on the PHIEUNHANPHONG table, preventing transactions that require a lock. After performing a query for room information, the staff member extended the date for a check-in, and the manager also requested a lock to update the status of a room. Leads to a deadlock state.
  2. Deadlock Transactions
  
   |Session 1|Session 2|Explaination|
   | --- | --- | --- |
   |SELECT P.MAPHONG,TINHTRANG,TENLOAIPHONG<br />FROM PHONG P INNER JOIN LOAIPHONG LP<br />ON P.MALOAIPHONG = LP.MALOAIPHONG FOR UPDATE;|No action.|Session 1 performs a query for rooms information and requests a lock on the PHONG Table.|
   |No action.|SELECT * FROM PHIEUNHANPHONG G<br />FOR UPDATE;|Session 2 queries the check-in and requests a lock on the PHIEUNHANPHONG table.|
   |No action.|UPDATE PHONG SET TINHTRANG='SUA CHUA'<br />WHERE MAPHONG = 49;|Session 2 requests a lock on the data unit MAPHONG = 49 and updates the room's status.|
   |EXEC PRO_UPDATE_PHIEUNHANPHONG<br />(92,68,48,2,0,'07-JULY-2020','09-JULY-2020');|No action.|Session 1 performs a lock request on the data unit with MAPHIEUNHAN = 92 and extends the departure date for the check-in.|
   3. Causes and solutions 
        - The problem occurs: The problem occurs: Session 1 waits for Session 2 to release the lock on the PHIEUNHANPHONG data unit and Session 2 waits for Session 1 to release the lock on the PHIEUNHANPHONG data unit. As a result, the management system reported an error and canceled T3.
        - Cause: T1 performs a query for room information and requests a lock on the PHIEUNHANPHONG data unit, T2 makes a query for the check-in information and requests a lock on the PHIEUNHANPHONG data unit. T3 performs a lock request on the data unit with MAPHONG = 49 that T1 is holding. T4 makes a lock request on the data unit with MAPHIEUNHAN = 92 that T2 is holding the lock on. Session 1 waits for Session 2 to release the lock on the PHIEUNHANPHONG data unit. Session 2 waits for Session 1 to release the lock on the PHONG data unit. Leads to Deadlock status.
        - Solution: The system will automatically rollback the transaction and return a response to the rolled transaction. Normally, the management system will not lock the data unit when reading data. Multiable deadlocks can be used to avoid transactions accessing the same table lock.
