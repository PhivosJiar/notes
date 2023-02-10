# 在AWS EC2安裝MongoDB 5.X

## 連線至EC2
首先我們先在AWS EC2上建立一個linux執行個體，執行個體可以根據需求選擇不同等級，這邊我選擇首年免費的t2.micro🤭🤭🤭

![](https://i.imgur.com/qBWdSg4.png)

建立完成之後，接著我們ssh進aws主機。

```bash
ssh -i /path/filename.pem ec2-user@ec2-xx-xx-xx-xx.compute-1.amazonaws.com
```
第一次會看到下面的訊息，輸入***yes***並送出。

![](https://i.imgur.com/aBIVBcJ.png)

成功連進主機後可以看到下面這一段。

```bash
Last login: Sat Feb  4 05:56:18 2023

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-172-31-11-10 ~]$
```
## 安裝mongoDB
現在我們已經連接進主機了，我們今天使用yum來安裝MongoDB，開始之前，我們需要手動建立mongodb.repo。
```bash
vim /etc/yum.repos.d/mongodb-org-5.0.repo
```
在vim中將下面資訊貼到檔案內並保存退出
```
[mngodb-org]
name=MongoDB Repository
baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/7Server/mongodb-org/5.0/x86_64/
gpgcheck=0
enabled=1
```

建立完mongodb.repo檔案之後就可以開始安裝了，輸入完指令要讓子彈飛一會，可以先去上個廁所再回來。
```bash!
sudo yum install -y mongodb-org-5.0.0 mongodb-org-server-5.0.0 mongodb-org-shell-5.0.0 mongodb-org-mongos-5.0.0 mongodb-org-tools-5.0.0
```

上完廁所回來，不出意外的話就可以看見完成的畫面。

![](https://i.imgur.com/AMiqejU.png)

到這邊我們就成功在AWS EC2 Linux中成功安裝MongoDB 5.0.0了。
* MongoDB指令
```bash
啟動：systemctl start mongod.service
停止：systemctl stop mongod.service
開機自動啟動：systemctl enable mongod.service
```

## 安全的本地連接到 AWS EC2 上的遠程 MongoDB

如果想在本地電腦連接上 AWS EC2 的 MongoDB，雖然可以透過更改安全群組的傳入方式來簡單的打開27017的port，但我們一般不會希望曝露出port讓外人連接。

這時我們可以通過ssh來創建連接通道，安全的從本地連接到 AWS EC2 上的遠程 MongoDB。

```bash!
ssh -i /path/filename.pem -N -f -L 27017:localhost:27017 ec2-user@ec2-xx-xx-xx-xx.compute-1.amazonaws.com
```
這一段跟上面要連接進主機的指令非常像，比較不一樣的地方有
- -N 代表ssh 不執行遠程命令，因此它不會在服務器上打開遠程 shell。
- -f 代表ssh 在後台運行。
- -L 27017:localhost:27017 告訴 ssh 將您的本地27017的port連接到server上的port 27017

現在我們可以通過本機連接上 AWS EC2 的 MongoDB了
![](https://i.imgur.com/FPJvvKV.png)

![](https://i.imgur.com/sGqaXzM.png)


## 關閉 SSH 連線
上一節我們使用 SSH 連接到了 AWS EC2 的 MongoDB，用完了之後我們如果想關閉 SSH 連接可以先用以下命令找出process id

```bash!
ps aux | grep ssh
```

![](https://i.imgur.com/uiXCACV.png)

找出process id後使用以下命令終止process

```bash!
kill -9 <process id>
```