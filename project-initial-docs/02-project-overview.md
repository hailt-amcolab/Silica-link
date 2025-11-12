# Project Overview - Tổng quan Kiến trúc Hệ thống

## Kiến trúc Tổng thể

Hệ thống được xây dựng với **Kintone** làm Database & Workflow trung tâm, tích hợp các hệ thống bên ngoài thông qua API và các công cụ tự động hóa.

## Nền tảng Trung tâm

### Kintone
- **Vai trò**: Database & Workflow trung tâm
- **Chức năng**: 
  - Lưu trữ dữ liệu chính của hệ thống
  - Quản lý workflow và quy trình nghiệp vụ
  - Điều phối các tích hợp với hệ thống bên ngoài

### AWS Lambda
- **Vai trò**: Batch xử lý, giám sát và logging
- **Chức năng**:
  - Xử lý các tác vụ batch định kỳ
  - Giám sát hệ thống
  - Lưu trữ và quản lý log

## Hệ thống Tích hợp

### 1. Yoom
- **Mục đích**: Nhập đơn từ các kênh bán hàng điện tử
- **Tích hợp**: 
  - Shopify
  - Rakuten
  - Amazon
- **Chức năng**: Tự động nhập đơn hàng vào hệ thống

### 2. Ship&Co
- **Mục đích**: Quản lý vận đơn và tracking
- **Chức năng**:
  - Phát hành vận đơn tự động
  - Gửi thông báo cho khách hàng
  - Tracking đơn hàng

### 3. WMS (Warehouse Management System)
- **Hệ thống**: 
  - ロジザードZERO
  - WMS Lite
- **Mục đích**: Quản lý kho hàng
- **Chức năng**:
  - Quản lý tồn kho
  - Quét mã vạch để xác nhận xuất kho
  - Cập nhật tồn kho realtime

### 4. GMO Aozora Bank API
- **Mục đích**: Quản lý tài chính và thanh toán
- **Chức năng**:
  - Đối soát giao dịch tự động
  - Thực hiện chuyển khoản
  - Quản lý thanh toán

### 5. Supplier Portal / EDI
- **Mục đích**: Kết nối với nhà cung cấp
- **Chức năng**:
  - Nhà cung cấp trả lời thời gian giao hàng
  - Kết nối API với hệ thống nhà cung cấp
  - Quản lý đơn đặt hàng (PO)

### 6. Affiliate/Đại lý giới thiệu (取次店)
- **Hệ thống**: RewardsLedger
- **Mục đích**: Quản lý hoa hồng và cổ tức
- **Chức năng**:
  - Tổng hợp hoa hồng theo tháng
  - Chi trả tự động
  - Xuất PDF báo cáo

## Tính năng Bổ sung

### Hóa đơn Song ngữ
- Hỗ trợ xuất hóa đơn tiếng Anh
- Xuất hóa đơn song ngữ (tiếng Nhật/tiếng Anh)

### Xuất kho Mẫu (Sample Outflow)
- Quản lý hàng mẫu
- Tự động trừ tồn kho khi xuất mẫu
- Lưu lịch sử xuất mẫu

## Luồng Dữ liệu Chính

```
Đơn hàng EC (Shopify/Rakuten/Amazon)
    ↓
Yoom → Kintone
    ↓
Kintone → WMS (xuất kho)
    ↓
WMS → Ship&Co (phát hành vận đơn)
    ↓
Ship&Co → Khách hàng (thông báo)
    ↓
GMO Aozora Bank API (đối soát & thanh toán)
    ↓
RewardsLedger (tính hoa hồng/cổ tức)
```

## Công nghệ và Công cụ

- **Backend**: Kintone (Database & Workflow)
- **Cloud**: AWS Lambda (Batch processing)
- **Integration**: REST API, EDI
- **Notification**: Email, SMS (qua Ship&Co)
- **Reporting**: PDF export, Dashboard

