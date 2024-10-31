---
title : "Cấu hình API Gateway"
date :  "`r Sys.Date()`" 
weight : 5 
chapter : false
pre : " <b> 5. </b> "
---

Dưới đây là hướng dẫn từng bước để cấu hình **API Gateway** trong dự án To-Do App. API Gateway sẽ đóng vai trò làm cầu nối giữa client và các Lambda function, cho phép các request từ client được xử lý một cách bảo mật và hiệu quả.

---

# Giới thiệu API Gateway và Hướng dẫn triển khai

## Mục tiêu
Sử dụng **API Gateway** để kết nối các request từ client đến các Lambda function, phục vụ cho các chức năng chính của ứng dụng To-Do App như đăng ký, đăng nhập, quản lý task và gửi nhắc nhở.

---

## 1. Giới thiệu về Amazon API Gateway

**Amazon API Gateway** là một dịch vụ quản lý API cho phép bạn dễ dàng tạo, xuất bản, duy trì, giám sát và bảo mật các API RESTful và WebSocket. Trong dự án này, API Gateway sẽ là cầu nối trung gian, chuyển các request từ client đến các Lambda function để xử lý logic của ứng dụng.

---

## 2. Hướng dẫn triển khai API Gateway

### Bước 1: Tạo API mới

1. Truy cập **API Gateway** trong **AWS Console**.
2. Nhấn **Create API**.
3. Chọn **REST API** và nhấn **Build**.
4. **Thiết lập API**:
   - **API Name**: Đặt tên cho API (ví dụ: `ToDoAppAPI`).
   - **Description**: Mô tả ngắn gọn cho API.
   - **Endpoint Type**: Chọn **Regional**.

5. Nhấn **Create API** để tạo API mới.

   *(Chèn ảnh minh họa cho các bước này)*

---

### Bước 2: Tạo Resource và Method cho các Endpoint

API sẽ có các endpoint để xử lý đăng ký, đăng nhập, quản lý task và nhắc nhở.

#### 2.1 Tạo Resource cho các Endpoint

1. Trong API vừa tạo, chọn **Actions** > **Create Resource**.
2. **Resource Name**: Đặt tên cho resource (ví dụ: `/register`, `/login`, `/tasks`, `/reminder`).
3. Chọn **Enable API Gateway CORS** nếu muốn API hỗ trợ CORS.
4. Nhấn **Create Resource**.

   *(Chèn ảnh minh họa cho các bước này)*

#### 2.2 Tạo Method cho từng Resource

1. Chọn resource đã tạo (ví dụ: `/register`) và nhấn **Create Method**.
2. Chọn **HTTP method** (POST cho `/register` và `/login`, GET cho `/tasks`, DELETE cho `/tasks/{taskId}`, POST cho `/reminder`).
3. Nhấn **Check**.
4. **Method Request**: Chọn **Lambda Function** là Integration Type.
5. Trong phần **Lambda Region**, chọn khu vực đã triển khai Lambda.
6. Nhập tên **Lambda Function** tương ứng (ví dụ: `register_user`, `login_user`, `add_task`, `delete_task`, `send_task_reminder`).
7. Nhấn **Save** và xác nhận quyền cho API Gateway gọi Lambda.

   *(Chèn ảnh minh họa cho các bước này)*

---

### Bước 3: Cấu hình CORS cho API Gateway

1. Chọn từng Resource cần hỗ trợ CORS (như `/register`, `/login`, `/tasks`).
2. Chọn **Actions** > **Enable CORS**.
3. Chọn **Enable CORS and replace existing CORS headers** để cấu hình lại header CORS cho resource.
4. Nhấn **Confirm** để lưu.

   *(Chèn ảnh minh họa cho các bước này)*

---

### Bước 4: Cấu hình Authorization (Tích hợp với AWS Cognito)

1. Chọn **Method Request** cho từng Method.
2. Trong phần **Authorization**, chọn **Cognito** để sử dụng AWS Cognito làm phương thức xác thực.
3. Tạo một **Cognito User Pool** (nếu chưa có) và chọn pool này trong **Method Authorization** để xác thực người dùng cho API.
4. Nhấn **Save** để lưu thay đổi.

   *(Chèn ảnh minh họa cho các bước này)*

---

### Bước 5: Triển khai API

1. Chọn **Actions** > **Deploy API**.
2. **Deployment Stage**: Nhấn **[New Stage]** và điền tên (ví dụ: `prod`).
3. Nhấn **Deploy** để triển khai API.

Sau khi triển khai, API Gateway sẽ tạo một URL endpoint cho mỗi resource và method mà bạn có thể sử dụng trong ứng dụng.

   *(Chèn ảnh minh họa cho các bước này)*

---

### Bước 6: Kiểm tra API Endpoint

1. Sau khi triển khai, truy cập **Invoke URL** để kiểm tra từng endpoint (POST /register, POST /login, GET /tasks, DELETE /tasks/{taskId}, POST /reminder).
2. Sử dụng **Postman** hoặc **cURL** để gửi request và kiểm tra xem Lambda function có phản hồi chính xác không.

   *(Chèn ảnh minh họa cho các bước này)*

---

## Kết luận

API Gateway hiện đã được cấu hình và kết nối với các Lambda function, đảm bảo rằng các request từ client có thể được gửi đến các Lambda function tương ứng để xử lý.