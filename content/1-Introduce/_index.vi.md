---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---


To-Do App là một ứng dụng quản lý công việc cá nhân được xây dựng trên nền tảng AWS. Ứng dụng cung cấp các chức năng cơ bản như đăng ký, đăng nhập, thêm, xóa, và quản lý các task. Bên cạnh đó, người dùng sẽ nhận được thông báo qua email nhắc nhở về các công việc sắp đến hạn. Ứng dụng sử dụng các dịch vụ của AWS để triển khai toàn bộ quy trình, từ xác thực người dùng, lưu trữ dữ liệu cho đến việc gửi thông báo email.

### 1. Chức năng chính

- **Đăng ký và đăng nhập người dùng**: Quản lý xác thực và phân quyền người dùng thông qua **AWS Cognito**.
- **Thêm và xóa task**: Lưu trữ và quản lý các task của người dùng bằng **AWS DynamoDB**.
- **Thông báo nhắc nhở**: Gửi email nhắc nhở người dùng về các công việc sắp đến hạn thông qua **AWS Simple Email Service (SES)**.
- **Lập lịch tự động**: Kiểm tra các task sắp đến hạn và tự động gửi thông báo mỗi giờ nhờ **Amazon EventBridge** và **AWS Lambda**.

### 2. Kiến trúc hệ thống

To-Do App được thiết kế với kiến trúc serverless trên nền tảng AWS, với các thành phần chính như sau:

- **Frontend (Giao diện người dùng)**: 
  - Sử dụng **AWS Amplify** để triển khai frontend, cung cấp giao diện người dùng cho ứng dụng. Người dùng có thể đăng ký, đăng nhập và quản lý task qua giao diện này.
  
- **Backend (Xử lý dữ liệu và API)**:
  - **AWS Cognito**: Quản lý đăng ký và đăng nhập người dùng.
  - **AWS API Gateway**: Làm trung gian giữa client và các Lambda Function. Mỗi endpoint trong API Gateway tương ứng với một chức năng như đăng ký, đăng nhập, thêm hoặc xóa task.
  - **AWS Lambda**: Xử lý logic nghiệp vụ cho các hành động của người dùng.
    - `register_user`: Tạo user mới trong DynamoDB khi người dùng đăng ký.
    - `login_user`: Lấy danh sách task của người dùng khi đăng nhập.
    - `add_delete_tasks`: Thêm và xóa task khỏi DynamoDB theo yêu cầu từ client.
    - `send_task_reminder`: Tự động kiểm tra các task sắp đến hạn và gửi email nhắc nhở qua SES.
  - **AWS DynamoDB**: Lưu trữ thông tin người dùng và các task của họ.

- **Thông báo và lập lịch**:
  - **Amazon EventBridge**: Lập lịch chạy `send_task_reminder` mỗi giờ một lần để kiểm tra các task sắp đến hạn.
  - **AWS SES**: Gửi email thông báo nhắc nhở đến người dùng về các task sắp đến hạn.
![image](https://github.com/user-attachments/assets/83dae68d-ddde-422c-ae77-7da9709542f4)
---
