---
title: "Triển khai VSCode Server trên EC2 và truy cập bằng VSCode trên máy của bạn"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 2.1.3 </b>"
---
### **Tạo IAM Policy cho phép tạo SSH Key**
Truy cập vào [mục Policies của bảng điều khiển IAM](https://console.aws.amazon.com/iam/home#/policies).
- Tại đây, chọn **_Create Policy_** để vào giao diện tạo Policy

![](/EKS-Workshop-1/images/2/1/3/001.jpg?width=110pc)

Trong giao diện **_Specify Policy_**:
- Chọn định dạng JSON và dán vào đoạn mã dưới đây:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateKeyPair",
                "ec2:DescribeKeyPairs",
                "ssm:PutParameter"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DeleteKeyPair",
                "ssm:DeleteParameter"
            ],
            "Resource": "*"
        }
    ]
}
```

![](/EKS-Workshop-1/images/2/1/3/002.jpg?width=50pc)

- Chọn **_Next_**

Trong giao diện **_Review and Create_**, tại phần **_Policy details_**, nhập tên cho chính sách (VD: `ssh-key-gen-policy`)

![](/EKS-Workshop-1/images/2/1/3/003.jpg?width=50pc)

### **Tạo IAM Role cho phép tạo SSH Key**
Truy cập vào [mục Roles của bảng điều khiển IAM](https://console.aws.amazon.com/iam/home#/roles).
- Tại đây, chọn **_Create Role_** để vào giao diện tạo Role.

![](/EKS-Workshop-1/images/2/1/3/004.jpg?width=100pc)

Tại bước **_Select trusted entity_**:
- Ở mục **_Trusted entity type_**, chọn **_AWS Services_**.
- Ở mục **_Use case_**, chọn **_Lambda_**

Tại bước **_Add Permissions_**, tìm tên Policy vừa tạo và tích chọn nó.

![](/EKS-Workshop-1/images/2/1/3/005.jpg?width=100pc)

Tại bước **_Name, review, and create_**, nhập tên cho Role muốn tạo (VD: `Name, review, and create`).

![](/EKS-Workshop-1/images/2/1/3/006.jpg?width=50pc)

Kéo xuống cuối trang và nhấn **_Create Role_**.

### **Tạo hàm Lambda tạo SSH Key**

Trong giao diện AWS Lambda:
- Chọn **_Functions_**
- Chọn **_Create function_**

![](/EKS-Workshop-1/images/2/1/3/007.jpg?width=50pc)

Trong giao diện Create funtion:
- Chọn **_Author from scratch_**
- Mục **_Function name_**, nhập `ssh-key-gen`
- Với **_Runtime_**, chọn **_Python 3.9_**
- Với **_Architecture_**, chọn **_x86_64_**

![](/EKS-Workshop-1/images/2/1/3/008.jpg?width=50pc)

Mở rộng mục **_Change default execution role_**:
- Chọn **_Use an existing role_**
- Tìm và chọn `ssh-key-gen-role`

Kéo xuống cuối trang và nhấn **_Create Function_**

Sửa nội dung hàm lambda như sau:
```python
"""
This lambda implements the custom resource handler for creating an SSH key
and storing in in SSM parameter store.

e.g.

SSHKeyCR:
    Type: Custom::CreateSSHKey
    Version: "1.0"
    Properties:
      ServiceToken: !Ref FunctionArn
      KeyName: MyKey

An SSH key called MyKey will be created.

"""

from json import dumps
import sys
import traceback
import urllib.request

import boto3


def log_exception():
    "Log a stack trace"
    exc_type, exc_value, exc_traceback = sys.exc_info()
    print(repr(traceback.format_exception(
        exc_type,
        exc_value,
        exc_traceback)))


def send_response(event, context, response):
    "Send a response to CloudFormation to handle the custom resource lifecycle"

    responseBody = { 
        'Status': response,
        'Reason': 'See details in CloudWatch Log Stream: ' + \
            context.log_stream_name,
        'PhysicalResourceId': context.log_stream_name,
        'StackId': event['StackId'],
        'RequestId': event['RequestId'],
        'LogicalResourceId': event['LogicalResourceId'],
    }

    print('RESPONSE BODY: \n' + dumps(responseBody))

    data = dumps(responseBody).encode('utf-8')
    
    req = urllib.request.Request(
        event['ResponseURL'], 
        data,
        headers={'Content-Length': len(data), 'Content-Type': ''})
    req.get_method = lambda: 'PUT'

    try:
        with urllib.request.urlopen(req) as response:
            print(f'response.status: {response.status}, ' + 
                  f'response.reason: {response.reason}')
            print('response from cfn: ' + response.read().decode('utf-8'))
    except urllib.error.URLError:
        log_exception()
        raise Exception('Received non-200 response while sending ' +\
            'response to AWS CloudFormation')

    return True


def custom_resource_handler(event, context):
    '''
    This function creates a PEM key, commits it as a key pair in EC2, 
    and stores it, encrypted, in SSM.

    To retrieve the key with currect RSA format, you must use the command line: 

    aws ssm get-parameter \
        --name <KEYNAME> \
        --with-decryption \
        --region <REGION> \
        --output text

    Copy the values from (and including) -----BEGIN RSA PRIVATE KEY----- to 
    -----END RSA PRIVATE KEY----- into a file.

    To use it, change the permissions to 600
    Ensure to bundle the necessary packages into the zip stored in S3

    '''
    print("Event JSON: \n" + dumps(event))

    # session = boto3.session.Session()
    # region = session.region_name

    # Original
    # pem_key_name = os.environ['KEY_NAME']
    
    pem_key_name = event['ResourceProperties']['KeyName']

    response = 'FAILED'
    
    ec2 = boto3.client('ec2')

    if event['RequestType'] == 'Create':
        try:
            print("Creating key name %s" % str(pem_key_name))

            key = ec2.create_key_pair(KeyName=pem_key_name)
            key_material = key['KeyMaterial']
            ssm_client = boto3.client('ssm')
            param = ssm_client.put_parameter(
                Name=pem_key_name, 
                Value=key_material, 
                Type='SecureString')

            print(param)
            print(f'The parameter {pem_key_name} has been created.')

            response = 'SUCCESS'

        except Exception as e:
            print(f'There was an error {e} creating and committing ' +\
                f'key {pem_key_name} to the parameter store')
            log_exception()
            response = 'FAILED'

        send_response(event, context, response)

        return

    if event['RequestType'] == 'Update':
        # Do nothing and send a success immediately
        send_response(event, context, response)
        return

    if event['RequestType'] == 'Delete':
        #Delete the entry in SSM Parameter store and EC2
        try:
            print(f"Deleting key name {pem_key_name}")

            ssm_client = boto3.client('ssm')
            rm_param = ssm_client.delete_parameter(Name=pem_key_name)

            print(rm_param)

            _ = ec2.delete_key_pair(KeyName=pem_key_name)

            response = 'SUCCESS'
        except Exception as e:
            print(f"There was an error {e} deleting the key {pem_key_name} ' +\
            from SSM Parameter store or EC2")
            log_exception()
            response = 'FAILED'
         
        send_response(event, context, response)


def lambda_handler(event, context):
    "Lambda handler for the custom resource"

    try:
        return custom_resource_handler(event, context)
    except Exception:
        log_exception()
        raise
```

Lưu lại hàm và chọn **_Deploy_**.

![](/EKS-Workshop-1/images/2/1/3/009.jpg)

Lưu lại ARN của hàm để dùng cho các bước sau.

### **Tạo CloudFormation Stack**
Tải tệp template tại link này.

Truy cập giao diện [CloudFormation](console.aws.amazon.com/cloudformation/home)

![](/EKS-Workshop-1/images/2/1/3/010.jpg)

- Chọn **_Create Stack_**.

Nhập tên cho **_Stack Name_** và **_Environment Name_**. Với **_Function ARN_**, nhập ARN của hàm Lambda vừa tạo

![](/EKS-Workshop-1/images/2/1/3/011.jpg)

Nhấn Next qua các bước và đợi đến khi Stack được tạo xong.

### **Lấy private key của EC2 vừa tạo**
Vào giao diện của [System Manager](console.aws.amazon.com/systems-manager/home).

Trong giao diện AWS Systems Manager
- Chọn Parameter Store

Trong giao diện Parameters Store
- Chọn My parameters
- Tìm tham số có tên của mã SSH được tạo (có dạng **\<EnvironmentName\>-vscode-keypair**)

Trong giao diện của tham số vừa mở:
- Chọn **_Overview_**
- Xem **_Last modified user_**
- Chọn **_Show Value_**

Sao chép **_value_** và lưu thành file *.pem để truy cập vào ssh.

Thêm vào file `C:\Users\<Tên người dùng>\.ssh\config`:

```
Host remote-connection
    HostName <public ip của EC2>
    User ubuntu
    IdentityFile "<đường dẫn đến file pem đã lưu>"
```

### **Kết nối VSCode đến EC2**
- Cài đặt tiện ích Remote-SSH để hỗ trợ việc kết nối vối SSH Host

- Sau khi cài đặt tiện ích, bạn sẽ nhìn thấy icon ở phía dưới bên trái của màn hình.

![](/EKS-Workshop-1/images/2/1/3/014.png)

- Ấn vào icon để mở bảng chọn kết nối. Chọn “Connect to Host…”

![](/EKS-Workshop-1/images/2/1/3/015.png)

- Chọn “Add New SSH Host” để tạo một SSH Host mới

![](/EKS-Workshop-1/images/2/1/3/016.png)

- Chọn `C:\Users\<tên người dùng>\.ssh\config` để mở file config 

- Sau khi thiết lập xong, bạn ấn vào icon để mở bạng chọn và điều hướng tới “remote-connection”

![](/EKS-Workshop-1/images/2/1/3/017.png)

- Tiếp theo, bạn chọn Platform details “Linux” và chọn “Continue”

![](/EKS-Workshop-1/images/2/1/3/018.png)

Nếu thành công, một cửa sổ VSCode mới sẽ mở lên và bạn sẽ được truy cập vào EC2. Từ đây hãy tìm đến thư mục **_Environment_** để mở môi trường thực hiện workshop và qua bước tiếp theo.
