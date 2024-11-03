---
title : "Triển khai AWS Amplify"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---


### 1. Giới thiệu về AWS Amplify

**AWS Amplify** là một nền tảng mạnh mẽ giúp bạn dễ dàng phát triển và triển khai các ứng dụng web và mobile sử dụng các dịch vụ AWS. Amplify cung cấp các công cụ và dịch vụ tích hợp để xây dựng frontend, triển khai backend, và quản lý toàn bộ quá trình phát triển ứng dụng.

---

### 2. Hướng dẫn triển khai

#### Bước 1: Vào thanh Console trong giao diện chính, tìm Amplify và chọn Deploy an app

![Screenshot 2024-10-31 145712](https://github.com/user-attachments/assets/c50633a4-53ad-4f06-a0e6-80f2f3b51992)


#### Bước 2: Chọn deploy trên Github repository và nhấn Next

![Screenshot 2024-10-31 144723](https://github.com/user-attachments/assets/ee7e845a-f65c-45ec-b36f-69d2af2def68)


#### Bước 3: Chọn repository chứa frontend và chọn branch phù hợp

![Screenshot 2024-10-31 144835](https://github.com/user-attachments/assets/de8245a6-1abd-4772-b696-353041679033)


#### Bước 4: Giữ nguyên mặc định và nhấn Next

![Screenshot 2024-10-31 144941](https://github.com/user-attachments/assets/be1722ce-70d5-4fb0-928c-b9ec8220becd)


#### Bước 5: Giữ nguyên mặc định và nhấn Next

![Screenshot 2024-10-31 145023](https://github.com/user-attachments/assets/7d6c5769-4cbd-45b7-bfd8-622b4906d475)


#### Bước 6: Đợi trạng thái chuyển sang Deployed và chọn Visit deployed URL

![Screenshot 2024-10-31 145127](https://github.com/user-attachments/assets/fc7b6267-8596-49ab-8aef-04a07c21b3eb)


{{% notice info %}} Nếu không thành công, bạn có thể kiểm tra trong phần Rewrites and redirects, sửa file index.html nếu file đó không phải là file giao diện trên repository của bạn. {{% /notice %}}

![Screenshot 2024-10-31 145228](https://github.com/user-attachments/assets/66a7c0c7-889b-4fef-9207-aba409f2d279)

---
