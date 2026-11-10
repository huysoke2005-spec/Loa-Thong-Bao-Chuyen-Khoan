# LOA THÔNG BÁO CHUYỂN KHOẢN SỬ DỤNG ESP32
> **Đề tài cuối kỳ môn học:** Phát triển ứng dụng IoT  
> **Giảng viên hướng dẫn:** TS. Nguyễn Anh Tuấn  
> **Sinh viên thực hiện:** Lã Quốc Huy - 23001609

## Đặt vấn đề
### 1. Nhu cầu thực tế
Ngày nay, thanh toán bằng mã QR đã trở thành phương thức phổ biến tại các cửa hàng, quán ăn và các điểm bán lớn nhỏ nhờ sự tiện lợi và nhanh chóng. Tuy nhiên, việc xác nhận giao dịch vẫn gặp một số khó khăn như người bán phải thường xuyên kiểm tra điện thoại, dễ bỏ sót thông báo khi đông khách và mất thời gian đối chiếu số tiền nhận được. Việc khách hàng đưa ảnh chụp giao dịch cũng chưa đủ để xác nhận tiền đã vào tài khoản của người bán.

Những vấn đề trên đặt ra nhu cầu xây dựng một thiết bị thông báo chuyển khoản tự động với các chức năng:

- **Thông báo tiền vào bằng giọng nói**: Phát số tiền đã nhận bằng tiếng Việt khi hệ thống tiếp nhận thông tin giao dịch, giúp người bán nhận biết mà không cần liên tục kiểm tra điện thoại.

- **Hiển thị thông tin thanh toán**: Cung cấp mã QR và thông tin tài khoản trên màn hình phía trước; hiển thị giao dịch gần nhất và trạng thái thiết bị trên màn hình phía sau.

- **Theo dõi và điều khiển từ xa**: Sử dụng Node-RED Dashboard để xem lịch sử giao dịch, theo dõi trạng thái và điều chỉnh các chức năng của thiết bị từ máy tính hoặc điện thoại.

- **Thích ứng với điều kiện sử dụng**: Điều chỉnh độ sáng theo cảm biến ánh sáng, hỗ trợ các nút thao tác trực tiếp và sử dụng nguồn pin để thuận tiện bố trí tại quầy.

### 2. Nguồn cảm hứng cho dự án
Ý tưởng xuất phát từ nhu cầu vừa phục vụ khách hàng vừa theo dõi thanh toán của người bán. Khi cửa hàng đông khách, việc phải dừng công việc để mở điện thoại kiểm tra từng giao dịch gây bất tiện. Một thiết bị thông báo bằng giọng nói kết hợp hiển thị số tiền có thể hỗ trợ người bán tiếp nhận thông tin nhanh chóng và đối chiếu thanh toán thuận tiện hơn.

Bên cạnh đó, đề tài xuất phát từ mong muốn vận dụng kiến thức hệ thống nhúng và IoT để giải quyết một nhu cầu thực tế. Việc kết hợp ESP32, màn hình, loa và cảm biến với giao thức MQTT và Node-RED tạo cơ hội tìm hiểu một hệ thống hoàn chỉnh, từ tiếp nhận dữ liệu qua Internet đến xử lý, hiển thị và điều khiển phần cứng. Qua đó, em hướng tới tự thiết kế và lập trình một mô hình có tính ứng dụng, phù hợp với phạm vi học phần và có khả năng mở rộng.

## Nội dung chính

### 1. Phần cứng

- **Vi điều khiển:** ESP32-WROOM-32, board 30 chân; xử lý dữ liệu, kết nối Wi-Fi và điều khiển các ngoại vi.
- **Hệ thống hiển thị:**
  - Màn hình TFT ILI9341 2.4 inch phía trước: Hiển thị mã QR thanh toán, tên người nhận và ngân hàng.
  - Màn hình OLED I2C 0.96 inch phía sau: Hiển thị giao dịch gần nhất, trạng thái kết nối và mức âm lượng.
- **Hệ thống âm thanh:**
  - Module MAX98357A: Nhận dữ liệu âm thanh từ ESP32 qua giao tiếp I2S và khuếch đại tín hiệu.
  - Loa 4 Ω, 3 W: Phát thông báo số tiền đã nhận bằng tiếng Việt.
- **Cảm biến ánh sáng BH1750:** Đo độ rọi môi trường để tự động điều chỉnh độ sáng màn hình.
- **Sáu nút nhấn phía sau:** Nghe lại, tăng âm lượng, giảm âm lượng, điều chỉnh độ sáng, thiết lập Wi-Fi và chuyển chế độ chờ.
- **Khối nguồn:** Pin LiPo 3.7 V kết hợp mạch sạc, bảo vệ pin và nâng áp 5 V để cấp nguồn cho thiết bị.
- **Linh kiện phụ trợ:** Tụ lọc nguồn, dây nối, đầu cắm, bo lắp ráp và vỏ thiết bị.

### 2. Phần mềm

- **Môi trường phát triển:** PlatformIO trên Visual Studio Code, sử dụng Arduino framework để lập trình ESP32 bằng C/C++.
- **MQTT qua broker online:** Trao đổi thông tin giao dịch, trạng thái thiết bị và lệnh điều khiển giữa ESP32 với Node-RED.
- **Node-RED:**
  - Tiếp nhận thông tin tiền vào từ dịch vụ thông báo giao dịch thông qua webhook.
  - Kiểm tra dữ liệu, lọc giao dịch phù hợp và kiểm tra mã giao dịch để hạn chế thông báo trùng.
  - Lưu lịch sử và gửi thông tin đến ESP32 qua MQTT.
- **Node-RED Dashboard:**
  - Hiển thị giao dịch gần nhất, lịch sử và tổng số tiền đã ghi nhận trong khoảng thời gian lựa chọn.
  - Theo dõi trạng thái kết nối, độ rọi, âm lượng và chế độ hoạt động.
  - Điều chỉnh âm lượng, độ sáng, nghe lại giao dịch và chuyển chế độ chờ từ máy tính hoặc điện thoại.
- **Xử lý âm thanh:** Lưu các đoạn âm thanh trong bộ nhớ flash của ESP32 và ghép để đọc số tiền, không sử dụng thẻ nhớ.

### 3. Ý tưởng hoạt động

- **Thanh toán và thông báo giao dịch:**
  - Khách hàng quét mã QR trên màn hình phía trước để thực hiện chuyển khoản.
  - Khi dịch vụ thông báo giao dịch ghi nhận tiền vào, dữ liệu được gửi đến Node-RED để xử lý và chuyển qua broker MQTT đến ESP32.
  - ESP32 cập nhật màn hình và phát số tiền đã nhận bằng tiếng Việt. Việc quét QR không tự kích hoạt thông báo nhận tiền.
  - Các giao dịch đến liên tiếp được đưa vào hàng đợi để phát lần lượt; mã giao dịch được kiểm tra nhằm hạn chế phát trùng.

- **Hiển thị và điều chỉnh độ sáng:**
  - Màn hình trước phục vụ khách hàng quét QR; màn hình sau giúp người bán theo dõi giao dịch và trạng thái thiết bị.
  - Cảm biến BH1750 đo ánh sáng môi trường để hệ thống lựa chọn mức sáng phù hợp.
  - Người dùng có thể chuyển giữa chế độ tự động và các mức sáng thủ công bằng nút nhấn hoặc Dashboard.

- **Điều khiển tại thiết bị:**
  - Nút nghe lại phát thông tin giao dịch gần nhất, có lời dẫn để phân biệt với giao dịch mới.
  - Hai nút âm lượng cho phép tăng hoặc giảm mức âm thanh.
  - Nút thiết lập Wi-Fi mở chế độ cấu hình mạng bằng điện thoại.
  - Nút chế độ chờ tắt hiển thị và ngừng phát loa nhưng vẫn duy trì kết nối để cập nhật dữ liệu; không ngắt nguồn thật.

- **Giám sát và sử dụng từ xa:**
  - Dữ liệu giao dịch và trạng thái được cập nhật trên Dashboard để người bán theo dõi bằng trình duyệt.
  - Thiết bị sử dụng pin để bố trí linh hoạt tại quầy, trong phạm vi có Wi-Fi và Internet.
  - Node-RED dự kiến chạy trên máy chủ online để hệ thống hoạt động không phụ thuộc vào laptop cá nhân.

## Sơ đồ khối

## Linh kiện dự kiến

| 1. ESP32 Devkit V1 | 2. TFT ILI9341 2.4 inch | 3. OLED I2C 0.96 inch |
| :---: | :---: | :---: |
| <img src="https://github.com/user-attachments/assets/17ba51f1-1d18-4823-a3a3-2cb74f1aa5f9" width="250" alt="ESP32"> | <img src="https://github.com/user-attachments/assets/5249714f-3d61-497d-b4d7-77f7870ee992" width="250" alt="TFT"> | <img src="https://github.com/user-attachments/assets/62dae4ce-b2a9-485a-bfdf-4c52fd858194" width="250" alt="OLED"> |
| **4. BH1750 GY-302** | **5. Module MAX98357A** | **6. Loa 4Ω 3W** |
| <img src="https://github.com/user-attachments/assets/3459dceb-3d8b-4f52-9411-4d49602839ef" width="250" alt="BH1750"> | <img src="https://github.com/user-attachments/assets/dd238bcb-eb12-488d-a36b-2bf195c1104b" width="250" alt="MAX"> | <img src="https://github.com/user-attachments/assets/084d9cc6-7cc1-44ea-a327-5b5a5fc529cc" width="250" alt="LOA"> |
| **7. Nút nhấn 4 chân** | **8. Pin Lipo 803040 3.7V** | **9. Module sạc và nâng áp** |
| <img src="https://github.com/user-attachments/assets/7d6f8bfd-e6b3-4ed7-a559-c39d2e9015da" width="250" alt="NutNhan"> | <img src="https://github.com/user-attachments/assets/b38e3ca1-2a59-471b-bdf8-c57aa26e55a4" width="250" alt="PinLipo"> | <img src="https://github.com/user-attachments/assets/f7fb4c8e-550d-43f1-b7aa-7e10588a164a" width="250" alt="Machsac"> |
  
## Tài liệu tham khảo

## Tài liệu tham khảo

1. **Giới thiệu loa thông báo chuyển khoản TingBox Pro:** Tham khảo chức năng, cách bố trí màn hình, loa và các nút điều khiển.  
   https://quang.vn/review-tingbox-pro-loa-chuyen-khoan-tot-nhat-2025

2. **Hướng dẫn sử dụng module khuếch đại âm thanh MAX98357A:** Tham khảo nguyên lý hoạt động, kết nối I2S và đấu nối loa.  
   https://learn.adafruit.com/adafruit-max98357-i2s-class-d-mono-amp/overview

3. **BH1750 Arduino Library:** Thư viện và ví dụ đọc dữ liệu cảm biến ánh sáng BH1750.                           
   https://github.com/claws/BH1750
   

   








