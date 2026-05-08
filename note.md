# 理解 Terraform

## Terraform 不是拿來「登入機器」的，而是拿來「建立與管理資源」的。

是用來宣告「我需要什麼資源」，Terraform 幫你建立 / 修改 / 刪除



## ASW 設定
aws configure -> 設定AWS憑證 會出現如下


```bash

AWS Access Key ID:
AWS Secret Access Key:
Default region name: -> 所在區域，一般會用 ap-northeast-1 (東京)
Default output format: -> json ( AWS CLI 是以 JSON 為基礎設計的 )

```


aws sts get-caller-identity -> 驗證憑證是否成功，驗證成功的話會如下

```bash

{
  "Account": "123456789012",
  "Arn": "arn:aws:iam::123456789012:user/your-user",
  "UserId": "AIDXXXXXXXX"
}

```

## ASW 初始化 EC2 可行方法範例

使用 EC2 User Data : Terraform 建立 EC2 -> EC2 在第一次開機時，可以自己安裝我要的程式做初始化

---

Terraform file + remote-exec -> EC2 建好之後，Terraform 用 SSH 連進 EC2，Terraform執行初始化指令

條件：需要有key pair、機器需要開SSH、如果有Private EC2就需跳板機/VPC/SSM

```hcl

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
  key_name      = "my-key"

  provisioner "remote-exec" {
    inline = [
      "sudo yum update -y",
      "sudo yum install -y nginx",
      "sudo systemctl start nginx",
      "sudo systemctl enable nginx"
    ]

    connection {
      type        = "ssh"
      user        = "ec2-user"
      private_key = file("~/.ssh/my-key.pem")
      host        = self.public_ip
    }
  }
}

```
---
使用 User Data + shell 檔案(.sh) 

install_nginx.sh

```bash

#!/bin/bash
yum update -y
yum install -y nginx
systemctl start nginx
systemctl enable nginx

```

Terraform：


```bash

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  user_data = file("${path.module}/install_nginx.sh")  #在這邊將.sh檔案引入

  tags = {
    Name = "terraform-web"
  }
}

```

---

用 local-exec 在本機執行指令

```bash
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  provisioner "local-exec" {
    command = "echo ${self.public_ip} >> ec2_ip.txt"
  }
}
```

---

用 AWS Systems Manager Session Manager / Run Command



## Terraform  指令

terraform init -> 初始化專案


terraform plan -> 預覽變更


terraform apply -> 建立資源


terraform destroy -> 安全刪除資源
