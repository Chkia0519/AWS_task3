# 【簡答題】
## 1. 何為 IaC? 除了 terraform 以外，還有哪些工具呢？
## 2. 執行 terraform 時，會有一個檔案 .tfstate，功能是什麼？
## 3. 多人協作時，如何確保 terraform 的狀態一致性？
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
