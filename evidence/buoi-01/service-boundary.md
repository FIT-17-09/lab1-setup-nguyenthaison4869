# Service Boundary - Nhóm 1

## 1. Thông tin nhóm
- **Tên nhóm:** Nhóm 1
- **Lớp:** CNTT 17-09
- **Thành viên:** Nguyễn Đức Hợp, Nguyễn Xuân Thành, Nguyễn Thái Sơn, Nguyễn Duy Mạnh
- **Service nhóm phụ trách:** Service A2: Camera Stream
- **Sản phẩm tổng thể của lớp:** []

## 2. Actor
Ai tương tác với hệ thống/service?
- **Camera vật lý / Script giả lập:** Nguồn đẩy dữ liệu chính.
- **Hệ thống giám sát (Monitoring):** Gọi để check xem service còn sống hay đã chết.
- **Quản trị viên (Admin):** Theo dõi qua các kết quả báo về từ các service liên quan.

## 3. System Boundary
**Phần nhóm kiểm soát:**
- Toàn bộ logic xử lý tại Service A2.
- Tiếp nhận, kiểm tra dữ liệu JSON đầu vào.
- Điều hướng dữ liệu (Routing) sang các service vệ tinh (A4, A5, A6).

**Phần nhóm chỉ tích hợp:**
- **Service A4 (AI Vision):** Gửi ảnh qua nhờ AI soi.
- **Service A6 (Core Business):** Bắn báo cáo khi có sự cố.
- **Service A5 (Analytics):** Đẩy log thô sang cho service làm thống kê.

## 4. Service Boundary
**Service của nhóm có trách nhiệm gì?**
- Làm "trạm trung chuyển" dữ liệu từ Camera đến các bộ phận khác.
- Đảm bảo việc tiếp nhận dữ liệu stream không bị gián đoạn.
- Phối hợp nhịp nhàng giữa việc gọi AI (đứng chờ) và báo cáo sự cố (chuyển tiếp).

**Service KHÔNG làm gì?**
- Không tự xử lý AI (không chạy model nhận diện).
- Không lưu trữ database lâu dài cho việc vẽ biểu đồ.
- Không trực tiếp xử lý các hành động nghiệp vụ (như hú còi, khóa cửa).

## 5. Input / Output
### Input
- Dữ liệu JSON từ Camera/Script: `{ "camera_id": "...", "frame_url": "...", "motion_detected": true/false }`.
- Tín hiệu check-alive từ hệ thống giám sát.

### Output
- Request gửi sang A4 để lấy kết quả phân tích AI.
- Cục dữ liệu tổng hợp (Gốc + AI result) gửi sang A6 để xử lý nghiệp vụ.
- Log dữ liệu đơn giản gửi sang A5.

## 6. API dự kiến
| Method | Endpoint | Mục đích | Payload chính |
|---|---|---|---|
| GET | `/api/v1/health` | Check xem service A2 còn thở không | `{"status": "alive"}` |
| POST | `/api/v1/frames/stream` | Tiếp nhận frame_url từ Camera | JSON chứa `frame_url` & `motion_detected` |

## 7. Phụ thuộc service khác
**Service này gọi đến service nào?**
- Gọi **Service A4** (AI-Vision) để phân tích hình ảnh.
- Gọi **Service A6** (Core Business) để báo cáo sự kiện.
- Gọi **Service A5** (Analytics) để lưu log thống kê.

**Service nào gọi đến service này?**
- **Camera/Script giả lập** gọi vào để đẩy data.
- **API Gateway/Monitoring** gọi vào để kiểm tra trạng thái.

## 8. Sơ đồ minh họa
![Sơ đồ](./images/sodo.png)*