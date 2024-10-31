---
title : "Cấu hình Lambda"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---



## 1. Giới thiệu về AWS Lambda

**AWS Lambda** là dịch vụ điện toán không máy chủ, cho phép chạy code mà không cần quản lý máy chủ. Với Lambda, bạn chỉ cần cung cấp mã nguồn, và Lambda sẽ tự động mở rộng và thực thi code theo nhu cầu. Điều này rất hữu ích cho các tác vụ ngắn, có thể thực thi nhanh chóng, như xác thực người dùng hoặc thao tác cơ sở dữ liệu.

---

## 2. Hướng dẫn triển khai Lambda cho các tính năng của To-Do App

### Bước 1: Tạo Lambda Function

1. Truy cập **AWS Console** và mở **AWS Lambda**.
2. Nhấn **Create function** để tạo một hàm Lambda mới.
3. **Thiết lập hàm Lambda**:
   - **Function name**: Đặt tên cho hàm (ví dụ: `register_user`, `login_user`, `add_task`, `delete_task`, `send_task_reminder`).
   - **Runtime**: Chọn ngôn ngữ lập trình (ví dụ: `Python 3.8`, `Node.js 14.x`).
   - **Permissions**: Chọn **Create a new role with basic Lambda permissions** hoặc chọn IAM role có quyền truy cập DynamoDB và SES nếu đã có.

4. Nhấn **Create function** để tạo hàm.

![Screenshot 2024-10-31 153837](https://github.com/user-attachments/assets/7590ce4c-2f05-4a72-95d7-57b56ef8f3c4)

---

### Bước 2: Tạo đoạn mã cho các Lambda Function

1. Trong phần **Code** của Lambda, thêm mã nguồn để thực hiện chức năng tương ứng.

2. Ví dụ, dưới đây là mã Python cho từng chức năng:

   - **register_user**: Lưu thông tin người dùng mới vào DynamoDB.
     ```
import json
import boto3
from botocore.exceptions import ClientError

dynamodb = boto3.resource('dynamodb')
table_name = 'to_do_user'
table = dynamodb.Table(table_name)

def lambda_handler(event, context):
    print("Received event:", event)

    # Lấy thông tin từ API Gateway hoặc event JSON
    try:
        email = event['email']
    except KeyError:
        return {
            'statusCode': 400,
            'body': 'Invalid request. Missing email.'
        }
    
    # Kiểm tra nếu Username đã tồn tại trong bảng
    try:
        response = table.get_item(Key={'email': email})
        if 'Item' in response:
            return {
                'statusCode': 400,
                'body': 'Email already exists.'
            }
        
        # Nếu User chưa tồn tại, tạo mục mới
        new_user = {
            'email': email,
            'tasks': []
        }
        
        # Ghi dữ liệu vào bảng
        table.put_item(Item=new_user)
        
        return {
            'statusCode': 200,
            'body': 'Account created successfully.'
        }
        
    except ClientError as e:
        return {
            'statusCode': 500,
            'body': f"Error accessing DynamoDB: {e.response['Error']['Message']}"
        }

     ```

   - **login_user**: Lấy danh sách task của người dùng từ DynamoDB.
     ```
import json
import boto3
from botocore.exceptions import ClientError

dynamodb = boto3.resource('dynamodb')
table_name = 'to_do_user'
table = dynamodb.Table(table_name)

def lambda_handler(event, context):
    # Lấy email từ tham số query string
    try:
        email = event['params']['querystring']['email']
    except KeyError:
        return {
            'statusCode': 400,
            'body': 'Invalid request. Missing email.'
        }

    # Truy vấn DynamoDB
    try:
        response = table.get_item(Key={'email': email})

        # Kiểm tra nếu email tồn tại
        if 'Item' in response:
            tasks = response['Item'].get('tasks', [])
            return {
                'statusCode': 200,
                'body': tasks
            }
        else:
            return {
                'statusCode': 404,
                'body': 'User does not exist.'
            }

    except ClientError as e:
        return {
            'statusCode': 500,
            'body': f"Error accessing DynamoDB: {e.response['Error']['Message']}"
        }

     ```

   - **add_delete_tasks**: Thêm task mới hoặc xóa task từ bảng DynamoDB.
     ```
import json
import boto3
from botocore.exceptions import ClientError

# Khởi tạo DynamoDB
dynamodb = boto3.resource('dynamodb')
table_name = 'to_do_user'
table = dynamodb.Table(table_name)

def lambda_handler(event, context):
    # Lấy loại API (add/del) và các tham số từ request
    try:
        type_api = event['params']['path']['type']
        task_body = event['body-json']
        task_id = task_body['task_id']
        email = task_body['email']
    except KeyError as e:
        return {
            'statusCode': 400,
            'body': f"Invalid request. Missing parameter: {str(e)}"
        }
    
    # Phương thức thêm task
    if type_api == "add":
        try:
            due_date = task_body['DueDate']
            description = task_body['Description']
        except KeyError as e:
            return {
                'statusCode': 400,
                'body': f"Invalid request. Missing parameter: {str(e)}"
            }
        
        # Kiểm tra trùng lặp TaskId
        try:
            # Lấy danh sách tasks hiện tại từ DynamoDB
            response = table.get_item(Key={'email': email})
            if 'Item' not in response:
                return {
                    'statusCode': 404,
                    'body': 'User does not exist.'
                }
            
            tasks = response['Item'].get('tasks', [])
            # Kiểm tra trùng lặp TaskId
            if any(task.get('TaskId') == task_id for task in tasks):
                return {
                    'statusCode': 400,
                    'body': f"TaskId {task_id} already exists. Please provide a unique TaskId."
                }
        
        except ClientError as e:
            return {
                'statusCode': 500,
                'body': f"Error accessing DynamoDB: {e.response['Error']['Message']}"
            }

        # Tạo task mới nếu TaskId không trùng
        new_task = {
            "TaskId": task_id,
            "DueDate": due_date,
            "Description": description,
            "IsNotified": False
        }
    
        # Sử dụng list_append để thêm task mới vào mảng tasks
        try:
            table.update_item(
                Key={'email': email},
                UpdateExpression="SET tasks = list_append(if_not_exists(tasks, :empty_list), :new_task)",
                ExpressionAttributeValues={
                    ':new_task': [new_task],
                    ':empty_list': []
                }
            )
            return {
                'statusCode': 200,
                'body': f"Task added successfully with TaskId {task_id}"
            }
        except ClientError as e:
            return {
                'statusCode': 500,
                'body': f"Error updating DynamoDB: {e.response['Error']['Message']}"
            }
    
    # Phương thức xóa task
    elif type_api == "del":
        try:
            # Lấy danh sách tasks hiện tại từ DynamoDB
            response = table.get_item(Key={'email': email})
            if 'Item' not in response:
                return {
                    'statusCode': 404,
                    'body': 'User does not exist.'
                }
            
            tasks = response['Item'].get('tasks', [])
            # Tìm task cần xóa
            updated_tasks = [task for task in tasks if task.get('TaskId') != task_id]
            
            # Kiểm tra nếu task không tồn tại
            if len(tasks) == len(updated_tasks):
                return {
                    'statusCode': 404,
                    'body': f"Task with TaskId {task_id} not found."
                }
            
            # Cập nhật lại bảng với danh sách task mới
            table.update_item(
                Key={'email': email},
                UpdateExpression="SET tasks = :updated_tasks",
                ExpressionAttributeValues={
                    ':updated_tasks': updated_tasks
                }
            )
            return {
                'statusCode': 200,
                'body': f"Task with TaskId {task_id} deleted successfully."
            }
        except ClientError as e:
            return {
                'statusCode': 500,
                'body': f"Error updating DynamoDB: {e.response['Error']['Message']}"
            }

    # Trường hợp type_api không hợp lệ
    else:
        return {
            'statusCode': 400,
            'body': "Invalid API type. Use 'add' to add a task or 'del' to delete a task."
        }

     ```

   - **send_task_reminder**: Gửi email nhắc nhở các task sắp đến hạn.
     ```
import boto3
from datetime import datetime, timezone, timedelta

# Khởi tạo các client
dynamodb = boto3.resource('dynamodb')
ses = boto3.client('ses')

# Hàm chính
def lambda_handler(event, context):
    table = dynamodb.Table('to_do_user')
    now = datetime.now(timezone.utc)
    
    # Truy vấn bảng để lấy các task chưa được thông báo
    response = table.scan()
    items = response['Items']
    
    for item in items:
        email = item['email']
        for task in item['tasks']:
            # Tính thời gian thông báo trước 1 giờ
            due_date = datetime.fromisoformat(task['DueDate'].replace("Z", "+00:00"))
            notify_time = due_date - timedelta(hours=1)
            
            # Gửi email nếu thời gian thông báo đạt điều kiện và chưa thông báo
            if notify_time <= now < due_date and not task['IsNotified']:
                send_email(email, task['DueDate'], task['Description'])
                task['IsNotified'] = True
        
        # Cập nhật trạng thái thông báo trong DynamoDB
        table.update_item(
            Key={'email': email},
            UpdateExpression="SET tasks = :tasks",
            ExpressionAttributeValues={':tasks': item['tasks']}
        )
    return 'Success'

# Hàm gửi email
def send_email(email, due_date, description):
    ses.send_email(
        Source="your-email@domain.com",  # Thay bằng email SES đã xác nhận
        Destination={'ToAddresses': [email]},
        Message={
            'Subject': {'Data': 'Task Reminder'},
            'Body': {
                'Text': {
                    'Data': f'Nhắc nhở: {description}\nThời gian: {due_date}'
                }
            }
        }
    )

     ```

---

### Bước 3: Cấu hình API Gateway để gọi Lambda Functions

1. **Truy cập AWS API Gateway** và tạo API mới hoặc cấu hình API hiện có.
2. Thiết lập **HTTP Methods** (POST, GET, DELETE) cho các endpoint và kết nối với các Lambda function đã tạo.
3. **Triển khai API** để tạo các endpoint cho ứng dụng.

---

### Bước 4: Tạo Trigger cho Lambda với EventBridge (Đối với Email nhắc nhở)

1. Truy cập **Amazon EventBridge** và chọn **Create rule** để tạo một trigger.
2. Đặt **Rule name** và chọn **Event Source** là `EventBridge Scheduler`.
3. Chọn **Target** là Lambda function `send_task_reminder` và cấu hình lịch chạy (1 giờ/lần).
4. Nhấn **Create** để hoàn thành.

---
{{% notice info %}} Đối với 2 bước cuối, tôi sẽ hướng dẫn bạn ở phần sau. {{% /notice %}}