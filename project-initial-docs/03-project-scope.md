# Project Scope - Phạm vi Dự án

## 1. Quản lý Đơn hàng

### Chức năng
- **Nhập đơn EC tự động**: Tích hợp với Yoom để nhập đơn từ Shopify, Rakuten, Amazon
- **Portal cho đại lý**: Cung cấp giao diện web cho đại lý đặt hàng
- **Mapping sản phẩm**: Quản lý mapping giữa sản phẩm đại lý giới thiệu và sản phẩm hệ thống

### Yêu cầu
- Tự động hóa hoàn toàn quy trình nhập đơn
- Hỗ trợ nhiều kênh bán hàng
- Quản lý đơn hàng tập trung trên Kintone

## 2. Quản lý Xuất kho

### Chức năng
- **Quét mã kho/xuất kho**: Sử dụng mã vạch để xác nhận xuất kho
- **Cập nhật tồn kho**: Tự động cập nhật tồn kho sau khi xuất
- **Phát hành vận đơn**: Tự động tạo vận đơn trên Ship&Co
- **Gửi thông báo**: Thông báo cho khách hàng về tình trạng đơn hàng

### Yêu cầu
- Tích hợp với WMS (ロジザードZERO / WMS Lite)
- Quy trình xuất kho realtime
- Đồng bộ dữ liệu giữa Kintone và WMS

## 3. Đối soát & Chi trả

### Chức năng
- **Kết nối GMO API**: Tích hợp với GMO Aozora Bank API
- **Đối soát giao dịch**: Tự động đối soát giao dịch thanh toán
- **Quản lý thanh toán**: Theo dõi và quản lý các khoản thanh toán
- **Xử lý ngoại lệ**: Xử lý các trường hợp thanh toán bất thường

### Yêu cầu
- Đối soát tự động hàng ngày
- Báo cáo chi tiết về thanh toán
- Cảnh báo khi có giao dịch bất thường

## 4. Quản lý Nhập hàng

### Chức năng
- **Cảnh báo tồn thấp**: Tự động cảnh báo khi tồn kho xuống thấp
- **Phát hành PO PDF**: Tự động tạo và gửi đơn đặt hàng (PO) dạng PDF
- **Portal nhà cung cấp**: Cung cấp giao diện cho nhà cung cấp xác nhận đơn hàng
- **Kết nối EDI**: Tích hợp với hệ thống EDI của nhà cung cấp

### Yêu cầu
- Tự động hóa quy trình đặt hàng
- Quản lý thời gian giao hàng từ nhà cung cấp
- Đồng bộ dữ liệu với hệ thống nhà cung cấp

## 5. Quản lý Rank Đại lý

### Chức năng
- **Batch tính toán**: Tính toán rank đại lý định kỳ
- **Cập nhật rank & hệ số chiết khấu**: Tự động cập nhật rank và hệ số chiết khấu dựa trên doanh số
- **Hiển thị tiến độ đạt rank**: Dashboard hiển thị tiến độ đạt rank cho đại lý

### Yêu cầu
- Tính toán rank theo quy tắc nghiệp vụ
- Cập nhật realtime khi có thay đổi
- Báo cáo tiến độ cho đại lý

## 6. Quản lý Hoa hồng/Cổ tức

### Chức năng
- **Ghi nhận RewardsLedger**: Ghi nhận các giao dịch hoa hồng vào RewardsLedger
- **Tính cổ tức theo quý**: Tự động tính toán cổ tức theo quý
- **Dashboard tổng hợp**: Hiển thị tổng hợp hoa hồng và cổ tức
- **Chi trả tự động**: Tự động chi trả hoa hồng/cổ tức
- **Xuất PDF**: Xuất báo cáo PDF cho đại lý

### Yêu cầu
- Tính toán chính xác hoa hồng và cổ tức
- Quản lý lịch sử chi trả
- Báo cáo chi tiết cho từng đại lý

## 7. Xuất kho Mẫu

### Chức năng
- **Form nhập liệu**: Form để nhập thông tin xuất kho mẫu
- **Giảm tồn kho**: Tự động trừ tồn kho khi xuất mẫu
- **Lưu lịch sử**: Lưu lại lịch sử xuất kho mẫu để theo dõi

### Yêu cầu
- Quản lý riêng biệt hàng mẫu và hàng bán
- Theo dõi lịch sử xuất mẫu
- Báo cáo xuất mẫu

## 8. Nền tảng Chung

### Chức năng
- **Quản lý quyền**: Phân quyền người dùng và quyền truy cập
- **Logging**: Ghi log các hoạt động hệ thống
- **Monitoring**: Giám sát hiệu năng và sức khỏe hệ thống
- **Dashboard**: Dashboard tổng hợp cho quản lý

### Yêu cầu
- Bảo mật thông tin
- Theo dõi và phát hiện lỗi nhanh chóng
- Báo cáo tổng hợp realtime

## Phạm vi Ngoài Dự án

Các chức năng **KHÔNG** nằm trong phạm vi dự án:

- Phát triển hệ thống WMS mới (chỉ tích hợp với WMS hiện có)
- Phát triển hệ thống thanh toán mới (chỉ tích hợp với GMO Aozora Bank)
- Phát triển hệ thống bán hàng EC mới (chỉ tích hợp với các kênh hiện có)
- Phát triển ứng dụng mobile riêng (có thể truy cập qua web portal)

## Tiêu chí Thành công

- ✅ Tự động hóa 90% các quy trình thủ công
- ✅ Giảm thời gian xử lý đơn hàng xuống 50%
- ✅ Độ chính xác dữ liệu đạt 99.9%
- ✅ Hệ thống hoạt động 24/7 với uptime > 99.5%
- ✅ Tích hợp thành công với tất cả các hệ thống yêu cầu

