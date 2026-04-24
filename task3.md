# 【簡答題】
## 1. 何為 IaC? 除了 terraform 以外，還有哪些工具呢？　

IaC -> 用「程式碼」來定義、建立、修改與管理 IT 基礎架構（例如 VM、網路、資料庫、IAM)，IaC 就是把「架構」當成「軟體」來管理。

不再手動點 UI，而是用可版本控、可重現的方式部署環境。 (有點像是Docker?

其他 IaC 工具 :　

AWS CloudFormation　-> Only for AWS 

Azure ARM / Bicep -> Microsoft Azure 官方 IaC，目前實務上幾乎都用 Bicep。 只能用在 Azure ，生態圈比 Terraform 小

Pulumi -> 用「程式語言」寫 IaC，酷 他可以用C#、Py、JS來定義基礎架構  

## 2. 執行 terraform 時，會有一個檔案 .tfstate，功能是什麼？ 

.tf -> 我要的目標狀態 

.tfstate -> terraform 記得的實際狀態 

但是.tfstate 不是**即時狀態**，而是**上一次知道的狀態**，只有在 **terraform apply** 時才會更新

.tfstate 是 Terraform 用來記錄「它所管理的資源，在真實世界中是哪些、目前狀態是什麼」的狀態檔。  

Terraform 會先計算目標狀態 (.tf) 與目前狀態 (.tfstate) 的差異，產生執行計畫，然後透過 Provider 呼叫雲端 API 來完成資源的新增、修改或刪除，以達到狀態一致。
## 3. 多人協作時，如何確保 terraform 的狀態一致性？

Remote Backend -> 把 .tfstate 放在「共享的遠端位置」，為確保所有人的狀態一致，只存在一份 .tfstate

State Locking -> 避免同時 apply 導致檔案損毀 同一時間 只能一個人 apply，當有人正在 apply 就會上鎖，直到操作完成才會解鎖

Git -> 作為唯一變更來源，避免各自亂改 .tf

## 4. 何為冪等性？他在 IaC 工具中帶來怎樣的好處？
冪等性是指：同一個操作，執行一次或執行很多次，最終結果都一樣。 

白話一點就是重複做同一件事，不會造成額外影響。(當我把音量設置到50，你設置100次音量還是會是50)

意思就是如果 .tfstate = .tf ，就什麼都不做，不會越建越多

---
**他在 IaC 工具中帶來怎樣的好處？**

1.可以一直重跑，無論是 apply 失敗、網路斷線，只要你想就可以一直重複跑，且最終結果都一樣

2.因為承上"可以一直重跑"，所以很適合做自動化

3.如果有人不小心改壞了，冪等行為會把它拉回至 .tf 宣告的狀態

4.在多人協作下非常好用，因為不管是誰跑，只要 state 一致，結果就一致

**沒有冪等性，就不可能有安全的自動化。**

## 5. 何為 terraform module，它解決了什麼問題？

Terraform module 是一組 **可重複使用的 Terraform 設定（.tf）** 的封裝單位 -> 類似python的def自訂義函式

---
**它解決了什麼問題？**

1.不需要一直重複寫一樣的 Terraform 程式碼

2.解決「架構難以理解」的問題

```terraform

module "vpc_dev" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
}

module "vpc_prod" {
  source = "./modules/vpc"
  cidr   = "10.1.0.0/16"
}
```

VPC 寫一次就好，可以用不同的參數呼叫，很容易理解架構也很容易讀

3.解決「團隊協作混亂」的問題 -> 由固定資深工程師維護，其他人只需要填參數，架構不會更動 = 安全性變高

4.解決「變更風險太高」的問題 -> 修改 module 時 可明確知道：哪些環境會受影響、哪些呼叫這個 module & 限制可變更範圍（guardrail），避免誤動關鍵架構，提升安全性與一致性

## 6. terraform 的資源創建順序為何？如何去控制相依性？

**Terraform 不靠寫的順序執行，而是靠「相依性圖（dependency graph）」自動決定順序。**

Terraform 會根據資源之間的參考關係，自動推導出正確的建立順序，而不是依照 .tf 撰寫順序。

```
解析 .tf
↓
建立 dependency graph（資源依賴圖）
↓
拓撲排序（topological sort）
↓
依序執行（可並行）
```

1.隱式相依 -> 透過「引用」產生

```
resource "aws_subnet" "subnet" {
  vpc_id = aws_vpc.main.id
}
```
先建 VPC -> 再建 Subnet

2.顯式相依 -> 用 depends_on **必要時才使用 depends_on 強制順序**

```
resource "aws_instance" "web" {
  depends_on = [aws_subnet.subnet]
}
```
意思：先做 subnet，才能做這台 EC2

如果我沒寫 depends_on = [aws_subnet.subnet] 他就不會建立 subnet 他會直接建EC2

## 7. 何為 datasource?

Data source 是用來「讀取已存在的資源或外部資訊」，而不是建立新資源

只讀（Read-only）、不建立任何東西、不改變現在狀況

```

data "aws_vpc" "default" {
  default = true
}

resource "aws_instance" "web" {
  subnet_id = data.aws_vpc.default.id
}

```
不要自己建 VPC，用 AWS 已經存在的那個就好查一下 已經存在的 VPC -> 把它的資訊拿來用

可用在:

1.讀取既有資源或外部資料

2.跨 module 以及跨系統的整合

## 8. 若使用 terraform 創建一台 ec2，希望對該 ec2 進行初始化操作（例如安裝 Nginx 或是需要執行某個 shell script），有哪些方式做到這件事，盡可能地列舉。
1.使用 EC2 User Data : Terraform 建立 EC2 -> EC2 在第一次開機時，可以自己安裝我要的程式做初始化

2.Terraform file + remote-exec -> EC2 建好之後，Terraform 用 SSH 連進 EC2，Terraform執行初始化指令

3.

# 【實作題】
## 1. 創建一個 project(github repo)，使用 terraform 創建一台 EC2，並且設定相關資源，例如 VPC（可選）、security group(firewall)、key pair 等。（可以嘗試跟 ai 詢問怎樣的檔案架構是比較好的。）
## 2. 延續上一題，嘗試將一些可變動的參數改成使用 variables 輸入，以及使用 output 來輸出一些資訊（例如 ssh 登入指令或是 public ip 等）。
## 3. 嘗試使用 draw.io 這類的工具，畫簡單的架構圖，並附於 github readme 中。

# 【進階題】
## 1. 嘗試了解 s3 作為 remote backend 的使用方式，如果不嫌麻煩，做個範例吧！
## 2. 嘗試了解 terragrunt 的使用方式，做個範例分享給其他同學。
