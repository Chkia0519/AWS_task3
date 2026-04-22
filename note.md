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

## Terraform  指令

terraform init -> 初始化專案


terraform plan -> 預覽變更


terraform apply -> 建立資源


terraform destroy -> 安全刪除資源
