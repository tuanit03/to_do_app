---
title : "Cấu hình DynamoDB"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3. </b> "
---


## 1. Giới thiệu về Amazon DynamoDB

**Amazon DynamoDB** là dịch vụ cơ sở dữ liệu NoSQL được quản lý bởi AWS, cung cấp hiệu năng cao, khả năng mở rộng linh hoạt và độ trễ thấp. DynamoDB lý tưởng cho các ứng dụng cần lưu trữ lượng lớn dữ liệu và có khả năng truy xuất nhanh chóng, như các ứng dụng thời gian thực, ứng dụng di động, hoặc ứng dụng có yêu cầu truy cập dữ liệu liên tục như To-Do App của bạn.

---

## 2. Hướng dẫn triển khai

### Bước 1: Tạo bảng DynamoDB cho To-Do App

1. **Truy cập AWS Console** và mở **Amazon DynamoDB**.
2. Chọn **Create Table** để tạo bảng mới.
3. **Cấu hình các trường trong bảng**:
   - **Table name**: Đặt tên bảng (ví dụ: `to_do_user`).
   - **Partition key**: Tạo khóa phân vùng (Partition Key) để xác định người dùng.
     - Với bảng `UsersTable`, bạn có thể đặt `email` (kiểu String) làm Partition Key.

4. **Thiết lập các tùy chọn khác**:
   - **Capacity mode**: Chọn chế độ `On-demand` để DynamoDB tự động điều chỉnh dung lượng, hoặc chọn `Provisioned` nếu muốn điều chỉnh dung lượng thủ công.
   - **Encryption**: Chọn `AWS managed key` để mã hóa dữ liệu tự động.

5. Nhấn **Create Table** để tạo bảng.

![Screenshot 2024-10-31 152701](https://github.com/user-attachments/assets/5a1dcd9d-6835-44e9-a92e-7f8c527c69e5)


### Bước 2: Cấu hình các thuộc tính và chỉ mục bổ sung

1. Chọn Create item, copy file json này :

  
   ```
{
  "email": {
    "S": "tuan@gmail.com"
  },
  "tasks": {
    "L": [
      {
        "M": {
          "Description": {
            "S": "Họp dự án"
          },
          "DueDate": {
            "S": "2024-10-30T14:00:00Z"
          },
          "IsNotified": {
            "BOOL": false
          },
          "TaskId": {
            "S": "1"
          }
        }
      },
      {
        "M": {
          "Description": {
            "S": "Báo cáo tuần"
          },
          "DueDate": {
            "S": "2024-11-01T09:00:00Z"
          },
          "IsNotified": {
            "BOOL": false
          },
          "TaskId": {
            "S": "2"
          }
        }
      },
      {
        "M": {
          "Description": {
            "S": "Gặp gỡ khách hàng"
          },
          "DueDate": {
            "S": "2024-11-05T16:00:00Z"
          },
          "IsNotified": {
            "BOOL": false
          },
          "TaskId": {
            "S": "3"
          }
        }
      },
      {
        "M": {
          "Description": {
            "S": "Hehe"
          },
          "DueDate": {
            "S": ""
          },
          "IsNotified": {
            "BOOL": false
          },
          "TaskId": {
            "N": "5"
          }
        }
      }
    ]
  }
}

   ```

