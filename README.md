# Cement-Mixing-Plant
# 📦 Hệ Thống Cân Định Lượng & Phối Trộn Nguyên Liệu Tự Động (TIA Portal)

Dự án thiết kế và lập trình điều khiển tự động hệ thống cân định lượng và phối trộn 4 thành phần nguyên liệu (Đá vôi, Đất sét, Quặng, Phụ gia) sử dụng PLC Siemens và giao diện giám sát WinCC/HMI. 

Dự án có tích hợp sẵn chế độ **Mô phỏng (Simulation)**, cho phép chạy thử nghiệm và kiểm tra logic toàn bộ hệ thống ngay trên máy tính mà không cần kết nối phần cứng thực tế.

---

## 📌 1. Tổng Quan & Cấu Trúc Phần Cứng

Hệ thống được thiết kế để tự động hóa hoàn toàn khâu cấp liệu, đo lường chính xác khối lượng từng thành phần theo công thức (Recipe) và xả vào bồn trộn.

* **Bộ điều khiển trung tâm:** PLC Siemens S7-1200 / S7-1500.
* **Cảm biến đo lường:** Loadcell kết hợp module đọc tín hiệu Analog đo lường trọng lượng thực tế tại bồn cân.
* **Cơ cấu chấp hành:** 
  * 04 Băng tải cấp liệu cho 4 loại nguyên liệu.
  * Hệ thống van xả phễu.
  * Băng tải tổng (thu thập nguyên liệu) và Bồn trộn (Mixer).
* **Giám sát & Điều khiển:** Giao diện Siemens WinCC / HMI KTP Basic.

---

## 💻 2. Cấu Trúc Chương Trình (Program Blocks)

Chương trình PLC được viết trên TIA Portal, chia thành các khối hàm (FC) rõ ràng để dễ dàng quản lý và bảo trì:

* **`Main [OB1]`**: Khối tổ chức chính, gọi các chương trình con và vòng quét của PLC.
* **`FC_TRỌNG_LƯỢNG_CÂN [FC1]`**: Khối xử lý tín hiệu cân (Scale), đọc tín hiệu Analog/Loadcell và quy đổi ra khối lượng thực tế (Kg).
* **`FC_MANUAL_MODE [FC2]`**: Khối logic vận hành chế độ bằng tay (Manual), cho phép người dùng điều khiển độc lập từng băng tải, van xả trên HMI.
* **`FC_AUTO_MODE [FC3]`**: Khối logic vận hành chế độ tự động (Auto), chạy chu trình cấp liệu tuần tự/đồng thời cho đủ định mức cài đặt.
* **`FC_OUTPUT [FC4]`**: Khối tổng hợp tín hiệu điều khiển và xuất ra phần cứng (Relay, Contactor, Biến tần).
* **`FC_SIMULATION [FC5]`**: Khối tạo tín hiệu giả lập (tăng/giảm khối lượng ảo, feedback ảo) phục vụ việc chạy mô phỏng không cần đấu nối thiết bị thật.

---

## 🏷️ 3. Quản Lý Biến Nội Bộ (PLC Tags)

Các biến (Tags) được quy hoạch thành từng Tag Table riêng biệt biệt theo chức năng, giúp source code gọn gàng:

* **`INPUT` / `OUTPUT`**: Mapping với địa chỉ I/O vật lý của PLC.
* **`AUTO_OUTPUT` / `MANUAL_OUTPUT`**: Phân tách rõ ràng các tín hiệu đầu ra ở 2 chế độ khác nhau.
* **`SETTING`**: Lưu trữ các thông số cài đặt (Khối lượng Đá vôi, Đất sét, Quặng, Phụ gia).
* **`BANGTAI`**: Các biến cờ (flags) trạng thái điều khiển hệ thống băng tải cấp liệu và xả.
* **`ACT` / `TRUNG_GIAN` / `MEMORY`**: Các biến cờ tác động, điều kiện trung gian và vùng nhớ nội bộ của hệ thống.
* **`XUNG`**: Các biến Timer, Clock Memory để tạo nhấp nháy đèn hoặc độ trễ thời gian.

---

## 🖥️ 4. Giao Diện Giám Sát SCADA (WinCC)
<img width="1141" height="844" alt="image" src="https://github.com/user-attachments/assets/b07ec4ec-e465-462a-9184-94fde626994a" />
Giao diện vận hành được thiết kế trực quan, mô phỏng đúng cấu trúc vật lý của trạm trộn:

* **Bảng điều khiển Mode:** Gạt chuyển đổi 2 chế độ vận hành (MANUAL / AUTO) và công tắc bật/tắt chế độ MÔ PHỎNG.
* **Bảng nút nhấn & Đèn báo:** Điều khiển tổng thể (START, STOP, RESET) và đèn trạng thái hệ thống.
* **Khu vực Cài đặt (Settings):** Cho phép nhập thông số khối lượng định mức (Kg) cho 4 nguyên liệu:
  * 🪨 Đá vôi
  * 🧱 Đất sét
  * 💎 Quặng
  * 🧪 Phụ gia
* **Mô phỏng quy trình (Animation):** Hiển thị thực thời trạng thái các băng tải nhánh đang chạy, van xả đang mở, nguyên liệu trên băng tải tổng, và giá trị khối lượng thực tế (Kg) đang nạp vào bồn trộn.

---

## 🚀 5. Hướng Dẫn Chạy Mô Phỏng (Simulation)

Để chạy thử nghiệm project trên máy tính cá nhân:
1. Mở project bằng TIA Portal (V15.1 trở lên).
2. Chuột phải vào thư mục PLC, chọn **Start Simulation** (S7-PLCSIM sẽ mở lên). Load chương trình xuống PLC ảo.
3. Chuột phải vào thư mục HMI, chọn **Start Simulation** để khởi chạy WinCC Runtime.
4. Trên màn hình HMI: Bật công tắc **MÔ PHỎNG** lên **ON**.
5. Nhập định mức khối lượng, chọn chế độ **AUTO** và nhấn **START** để xem chu trình chạy ảo tự động.
