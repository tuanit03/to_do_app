---
title : "Cấu hình API Gateway"
date :  "`r Sys.Date()`" 
weight : 5 
chapter : false
pre : " <b> 5. </b> "
---


# Giới thiệu API Gateway và Hướng dẫn triển khai

## 1. Giới thiệu về Amazon API Gateway

**Amazon API Gateway** là một dịch vụ quản lý API cho phép bạn dễ dàng tạo, xuất bản, duy trì, giám sát và bảo mật các API RESTful và WebSocket. Trong dự án này, API Gateway sẽ là cầu nối trung gian, chuyển các request từ client đến các Lambda function để xử lý logic của ứng dụng.

---

## 2. Hướng dẫn triển khai API Gateway

### Bước 1: Tạo API mới

1. Truy cập **API Gateway** trong **AWS Console**.
2. Nhấn **Create API**.
3. Chọn **REST API** và nhấn **Build**.
4. **Thiết lập API**:
   - **API Name**: Đặt tên cho API (ví dụ: `to_do_api`).
   - **Description**: Mô tả ngắn gọn cho API.
   - **Endpoint Type**: Chọn **Regional**.

5. Nhấn **Create API** để tạo API mới.

---

### Bước 2: Tạo Resource và Method cho các Endpoint

API sẽ có các endpoint để xử lý đăng ký, đăng nhập, quản lý task và nhắc nhở.

#### 2.1 Tạo Resource cho các Endpoint

1. Trong API vừa tạo, chọn **Actions** > **Create Resource**.
2. **Resource Name**: Đặt tên cho resource (ví dụ: `to_do_app`).
3. Nhấn **Create Resource**.

#### 2.2 Tạo Method cho Resource `to_do_app`

1. Chọn resource đã tạo và nhấn **Create Method**.
2. Chọn **POST method** đối với api đăng ký và **GET method** đối với api đăng nhập
3. Nhập tên **Lambda Function** tương ứng (ví dụ: `register_user`, `login_user`).
4. Nhấn **Save** và xác nhận quyền cho API Gateway gọi Lambda.


#### 2.3 Tạo api cho lambda function `add_delete_tasks`

1. Chọn resource đã tạo và nhấn **Create Resource**.
2. Nhập **{type}** ở Resource name và chọn Create Resource
3. Chọn **POST method** và chọn lambda function `add_delete_tasks`
4. Nhấn **Save** và xác nhận quyền cho API Gateway gọi Lambda.

   ![image](https://github.com/user-attachments/assets/dd5dabe5-ce01-41ad-9da4-9b094553bc6d)

---

### Bước 3: Cấu hình CORS cho API Gateway

1. Chọn từng Resource cần hỗ trợ CORS (như `to_do_app`, `{type}`).
2. Chọn **Actions** > **Enable CORS**.
3. Trong **Access-Control-Allow-Methods** chọn các hết các method.
4. Nhấn **Save** để lưu.

![image](https://github.com/user-attachments/assets/637d3c23-27b3-4709-86c1-96c994209107)

---


### Bước 4: Triển khai API

1. Chọn **Actions** > **Deploy API**.
2. **Deployment Stage**: Nhấn **[New Stage]** và điền tên (ví dụ: `prod`).
3. Nhấn **Deploy** để triển khai API.

Sau khi triển khai, API Gateway sẽ tạo một URL endpoint cho mỗi resource và method mà bạn có thể sử dụng trong ứng dụng.

![image](https://github.com/user-attachments/assets/1a0a0c90-c6ff-4f92-9066-9c60f2b414f4)

---

### Bước 6: Kiểm tra API Endpoint

1. Sau khi triển khai, truy cập **Invoke URL** để kiểm tra từng endpoint (POST /register, GET /login, POST /add_delete_tasks).
2. Sử dụng **Postman** hoặc **cURL** để gửi request và kiểm tra xem Lambda function có phản hồi chính xác không.

---
