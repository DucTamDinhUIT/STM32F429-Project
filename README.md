# STM32F429 Discovery BSP Project

Dự án này là phiên bản tùy chỉnh của ví dụ BSP (Board Support Package) từ STMicroelectronics, được tối ưu hóa để chạy ổn định trên board **STM32F429I-DISC1** (phiên bản mới thay thế cho RevB/C cũ).

## Các thay đổi chính (Cần lưu ý)

Để chạy được project gốc trên board DISC1, tôi đã thực hiện các thay đổi quan trọng sau:

### 1. Cấu hình Preprocessor cho Phần cứng mới
Board DISC1 tương đương với các phiên bản **RevD** hoặc **RevE** của dòng Discovery cũ. Để driver LCD và Touchscreen hoạt động chính xác (đặc biệt là ID chip và hướng hiển thị):
- Đã thêm `USE_STM32F429I_DISCOVERY_REVE` vào cấu hình **Preprocessor symbols** trong Project Settings.
- Việc này giúp project nhận diện đúng chip điều khiển cảm biến STMPE811 và cấu hình LCD chuẩn xác.

### 2. Sửa lỗi Calibrate màn hình cảm ứng (Touchscreen Calibration Fix)
Trong file [ts_calibration.c](file:///c:/Users/ASUS/STM32Cube/Example/BSP/BSP/Src/ts_calibration.c), logic tính toán hệ số cân chỉnh đã được điều chỉnh:
- **Lỗi gốc:** Các phiên bản cũ thường gặp vấn đề tràn số (integer overflow) hoặc tính toán sai tỉ lệ khi map tọa độ từ driver sang tọa độ màn hình LCD (240x320).
- **Khắc phục:** 
    - Cập nhật công thức tính hệ số `A1, A2, B1, B2` để đảm bảo độ chính xác cao hơn.
    - Đảm bảo tọa độ vật lý (Physical) từ cảm biến được ánh xạ chính xác vào tọa độ logic trên màn hình, giúp loại bỏ hiện tượng bị lệch cảm ứng hoặc "điểm chết" ở rìa màn hình.

### 3. Tối ưu hóa Touchscreen Demo
- Cải thiện hàm `WaitForPressedState` để xử lý việc phản hồi chạm nhạy hơn trên board DISC1.
- Đảm bảo các demo (LCD, MEMS, Log) hoạt động mượt mà sau khi đã calibrate thành công.

## Yêu cầu Phần cứng & Phần mềm
- **Board:** STM32F429I-DISC1 (hoặc STM32F429I-Discovery RevD/E).
- **IDE:** STM32CubeIDE 2.0.0 trở lên.
- **Drivers:** STM32F4xx HAL Library & BSP Drivers.

## Cách chạy dự án
1. Mở dự án bằng **STM32CubeIDE**.
2. **Build** toàn bộ project.
3. Nạp code xuống board.
4. Khi màn hình hiện thông báo calibrate, hãy nhấn vào các vòng tròn đen để cân chỉnh.
5. Sau khi cân chỉnh thành công, nhấn **USER Button** để chuyển đổi giữa các Demo:
    - **TOUCHSCREEN:** Kiểm tra cảm ứng với các vòng tròn màu.
    - **LCD:** Hiển thị các hình khối và text.
    - **LCD LOG:** Test tính năng log dữ liệu ra màn hình.
    - **MEMS:** Kiểm tra cảm biến gia tốc (nghiêng board để di chuyển đối tượng).

---
*Ghi chú: Project này được xây dựng dựa trên mẫu chuẩn của ST nhưng đã được tinh chỉnh để tương thích hoàn toàn với phần cứng hiện tại của bạn.*
