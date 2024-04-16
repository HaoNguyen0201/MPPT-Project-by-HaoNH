# MPPT-Project-by-HaoNH
## MPPT Charge Controller using Arduino 

Để tối đa hóa công suất quang điện (PV), việc liên tục theo dõi điểm công suất tối đa (MPP) của hệ thống là rất cần thiết. MPP của hệ thống PV phụ thuộc vào điều kiện bức xạ mặt trời, nhiệt độ môi trường và nhu cầu phụ tải. Kỹ thuật theo dõi điểm công suất tối đa (MPPT) có thể bắt được MPP của hệ thống PV. Những kỹ thuật như vậy có thể được thực hiện dưới nhiều dạng phần cứng và phần mềm khác nhau. Mục tiêu của dự án này là phát triển, xây dựng và thử nghiệm một giải pháp hiệu quả nhằm tìm ra MPP với ngân sách hạn chế. Dự án bao gồm bốn chương, nói về mạch MPPT, tấm pin năng lượng mặt trời và công thức của nó, về cách hoạt động của MPPT, các bộ phận và mạch phụ cần thiết, phân tích bộ chuyển đổi DC-DC cũng như mô phỏng và tiến hành làm mạch thực tế.

## Thông số kỹ thuật:
<ol>
  <li>Dựa trên thuật toán MPPT</li>
  <li>Đèn LED báo trạng thái sạc</li>
  <li>LCD 20x4 để quan sát dòng, áp, công suất,...</li>
  <li>Bảo vệ quá áp, quá tải, ngắn mạch và công suất chảy ngược</li>
  <li>Điện áp định mức: 12V</li>
  <li>Dòng điện cực đại: 5A</li>
  <li>Dòng tải cực đại: 10A</li>
  <li>Điện áp ngõ vảo: Tấm pin mặt trời với điện áp dao động từ 12-25V</li>
  <li>Công suất tấm pin năng lượng mặt trời: 50W</li>
</ol>

## Linh kiện:
<ol>
  <li>Điện trở: 3x200R, 3x330R, 1x1K, 2x10K, 2x20K, 2x100k, 1x470K</li>
  <li>Diode TVS: 2xP6KE36CA</li>
  <li>Arduino Nano v3</li>
  <li>Cảm biến dòng: ACS712 - 5A</li>
  <li>Màn hình LCD 20x4</li>
  <li>MOSFET: 4xIRFZ44N </li>
  <li>MOSFET driver: IR2104</li>
  <li>Transistor: 2N2222</li>
  <li>Diodes: 2xIN4148, 1xUF4007</li>
  <li>Điện trở: 3x200R, 3x330R, 1x1K, 2x10K, 2x20K, 2x100k, 1x470K</li>
  <li>Tụ điện: 4x0.1 uF, 3x10uF ,1x100 uF, 1x220uF</li>
  <li>Cuộn cảm: 1x33uH - 5A</li>
  <li>LED: đỏ, vàng, xanh</li>
  <li>Cầu chì: 5A</li>
  <li>Pin năng lượng mặt trời: 12V, 50W</li>
  <li>Pin sạc: Ắc quy 12V</li>
</ol>

## Giải thuật MPPT:
Giải thuật theo dõi công suất tối đa sử dụng phương pháp lặp đi lặp lại để
tìm MPP thay đổi liên tục. Phương pháp lặp này được gọi là Perturb-and-Observe
(P&O) hoặc thuật toán leo đồi (hill climbing algorithm). Để đạt được MPPT, bộ
điều khiển sẽ điều chỉnh điện áp một lượng nhỏ từ tấm pin mặt trời và đo công suất,
nếu công suất tăng, nó sẽ thử tiếp tục điều chỉnh thêm cho đến khi công suất không
tăng thêm được nữa.

Điện áp vào tấm pin mặt trời ban đầu được tăng lên, nếu công suất đầu ra tăng,
điện áp sẽ liên tục tăng cho đến khi công suất đầu ra bắt đầu giảm. Khi công suất
đầu ra bắt đầu giảm, điện áp trên tấm pin mặt trời sẽ giảm cho đến khi đạt công
suất tối đa. Quá trình này được tiếp tục cho đến khi đạt được MPP. Kết quả này là
sự dao động của công suất đầu ra xung quanh MPP.

![image](https://github.com/HaoNguyen0201/MPPT-Project-by-HaoNH/assets/137909212/4a61f750-4093-464e-b314-a4133882363a)

## Triển khai dự án:

### Flowchart:

![image](https://github.com/HaoNguyen0201/MPPT-Project-by-HaoNH/assets/137909212/d9853820-c089-4f02-b74c-926ac10f56d0)

#### Set up:
Khi hệ thống khởi động lần đầu tiên, nó sẽ khởi tạo chân đầu vào và đầu ra, khai
báo tất cả các biến, hằng và các hàm sẽ được sử dụng trong quá trình này.
<ol>
  <li>Minimum Battery Voltage (Low Voltage Disconnect-LVD): 11.9V</li>
  <li>Maximum Battery Voltage: 14.1V</li>
</ol>

Sau đó, nó cấu hình LCD và tắt MOSFET điều khiển đầu ra, MOSFET driver và
đặt PWM rate về 0%

#### Loop phase:
Bắt đầu đọc tín hiệu vào
<ol>
  <li>Điện áp cấp bởi tấm pin PV</li>
  <li>Dòng tạo ra bởi tấm pin PV</li>
  <li>Điện áp của pin</li>
</ol>

Khi đã nhận đủ các tín hiệu vào, tính công suất tạo ra của tấm pin PV bằng
cách lấy áp và dòng đọc được nhân với nhau.

Dựa vào các thông số đọc và tính toán được đó, cấu hình sạc pin sẽ được đặt như
sau:
<ol>
  <li>Nếu công suất PV cung cấp rất thấp (vào ban đêm, thời tiết nhiều mây, tấm
pin bị bẩn), trạng thái sạc được đặt thành OFF, MOSFET driver tắt và tần
số xung PWM được đặt xuống 0%</li>
  <li>Nếu công suất PV cung cấp yếu và pin chưa được sạc đầy, trạng thái sạc được
đặt thành ON, MOSFET driver được bật và tần số xung PWM được đặt lên
100%</li>
  <li>Nếu công suất PV cung cấp ở mức trung bình đến cao và pin chưa được sạc
đầy thì trạng thái sạc được đặt thành Bulk, MOSFET driver được bật và tần
số xung PWM được đặt thành Maximum.</li>
  <li>Nếu công suất PV cung cấp ở mức trung bình đến cao và pin đã được sạc đầy
thì trạng thái sạc được đặt thành Float, MOSFET driver được bật và tàn số
xung PWM được đặt thành Minimum.</li>
</ol>

Tiếp theo, ta cấu hình điều khiển tải ngõ ra như sau:

<ol>
  <li>Nếu vào ban đêm và điện áp của pin cao hơn điện áp ngưỡng LVD là 11,9V,
ngõ ra sẽ được bật và pin sẽ cung cấp năng lượng cho tải.</li>
  <li>Nếu vào ban ngày và điện áp pin cao hơn điện áp ngưỡng LVD là 11,9V thì
ngõ ra cũng được bật, nhưng lần này tải được cung cấp năng lượng bởi pin và
công suất dư thừa do tấm pin PV cung cấp.</li>
  <li>Nếu điện áp pin thấp hơn điện áp ngưỡng LVD là 11,9V, ngõ ra sẽ bị tắt và
tải sẽ ngắt kết nối.</li>
</ol>

Bước tiếp theo ta cài đặt bật tắt LED tương ứng với các mức điện áp của pin
như sau:
<ol>
  <li>Nếu điện áp của pin thấp hơn 11.9V, LED đỏ được bật.</li>
  <li>Nếu điện áp của pin trong khoảng 11,9V đến 14,1V, LED xanh được bật.</li>
  <li>Nếu điện áp của pin vượt quá 14,1V, LED vàng được bật.</li>
</ol>

Sau đó, Arduino sẽ cập nhật thông tin như tiến trình phía trên và hiển thị trên
LCD, sau đó tiếp tục đọc tín hiệu vào và bắt đầu lại Loop Phase. Cứ như thế quá
trình đọc và hiển thị dữ liệu được thực hiện liên tục.

### Sơ đồ mạch:

![image](https://github.com/HaoNguyen0201/MPPT-Project-by-HaoNH/assets/137909212/2c0023e2-a4b9-48c0-8220-0df5091365e0)

### Mạch hoàn chỉnh:

![image](https://github.com/HaoNguyen0201/MPPT-Project-by-HaoNH/assets/137909212/86ad095c-78af-4fd1-a0d4-1f526e8f77cd)

![image](https://github.com/HaoNguyen0201/MPPT-Project-by-HaoNH/assets/137909212/32043af9-c790-4474-bff4-c23d1b8d5f7c)

## Kết luận: 
Do hạn chế về khả năng hàn mạch và linh kiện nên mạch hoạt động không được trơn tru, như dựa vào mô phỏng và phần thực nghiệm có thể chứng minh tính thực tế của dự án.
