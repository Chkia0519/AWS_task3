# 【簡答題】
## 1. 何為 IaC? 除了 terraform 以外，還有哪些工具呢？　

IaC -> 用「程式碼」來定義、建立、修改與管理 IT 基礎架構，IaC 就是把「架構」當成「軟體」來管理。

其他 IaC 工具 :　

AWS CloudFormation　-> Only for AWS 

Azure ARM / Bicep -> Microsoft Azure 官方 IaC，目前實務上幾乎都用 Bicep。 只能用在 Azure ，生態圈比 Terraform 小

Pulumi -> 用「程式語言」寫 IaC，酷 他可以用C#、Py、JS來定義基礎架構  

## 2. 執行 terraform 時，會有一個檔案 .tfstate，功能是什麼？ 

.tf -> 我要的目標狀態 

.tfstate -> terraform 記得的實際狀態 

但是.tfstate 不是**即時狀態**，而是**上一次知道的狀態**，只有在 **terraform apply** 時才會更新

.tfstate 是 Terraform 用來記錄「它所管理的資源，在真實世界中是哪些、目前狀態是什麼」的狀態檔。  

## 3. 多人協作時，如何確保 terraform 的狀態一致性？

Remote Backend -> 把 .tfstate 放在「共享的遠端位置」，為確保所有人的狀態一致，只存在一份 .tfstate

State Locking -> 避免同時 apply 導致檔案損毀 同一時間 只能一個人 apply，當有人正在 apply 就會上鎖，直到操作完成才會解鎖

Git -> 作為唯一變更來源，避免各自亂改 .tf

## 4. 何為冪等性？他在 IaC 工具中帶來怎樣的好處？
## 5. 何為 terraform module，它解決了什麼問題？
## 6. terraform 的資源創建順序為何？如何去控制相依性？
## 7. 何為 datasource?
## 8. 若使用 terraform 創建一台 ec2，希望對該 ec2 進行初始化操作（例如安裝 Nginx 或是需要執行某個 shell script），有哪些方式做到這件事，盡可能地列舉。

# 【實作題】
## 1. 創建一個 project(github repo)，使用 terraform 創建一台 EC2，並且設定相關資源，例如 VPC（可選）、security group(firewall)、key pair 等。（可以嘗試跟 ai 詢問怎樣的檔案架構是比較好的。）
## 2. 延續上一題，嘗試將一些可變動的參數改成使用 variables 輸入，以及使用 output 來輸出一些資訊（例如 ssh 登入指令或是 public ip 等）。
## 3. 嘗試使用 draw.io 這類的工具，畫簡單的架構圖，並附於 github readme 中。

# 【進階題】
## 1. 嘗試了解 s3 作為 remote backend 的使用方式，如果不嫌麻煩，做個範例吧！
## 2. 嘗試了解 terragrunt 的使用方式，做個範例分享給其他同學。
