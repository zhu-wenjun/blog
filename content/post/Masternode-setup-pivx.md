---
title: "在Docker中搭建PIVX Masternode [Draft]"
date: 2018-12-04T18:17:50+08:00
draft: false
---



{{% admonition tip 相关链接%}}
https://pivx.org/knowledge-base/masternode-setup-guide/<br>
https://masternode-setup.com/pivx/<br>
https://masternode-setup.com/pivx/hot-cold-vps.html
{{% /admonition %}}

{{% admonition tip 流程介绍%}}
1. 在Mac上安装PIVX钱包，这个钱包称之为Control wallet或者Hot wallet<br>
2. 在Ubuntu VPS上搭建Masternode节点，这个钱包称之为Remote wallet或者Cold wallet
{{% /admonition %}}

{{% admonition tip 重要提示%}}
成为Masternode节点的首要条件：``` 充10,000个PIV. 1个PIV相当于 $0.7 ```
{{% /admonition %}}


***
<br><br>

## 1. Control wallet config
从[官网](https://pivx.org/wallet/)或 下载系统对应的钱包, 我从[Github](https://github.com/PIVX-Project/PIVX/releases)下载的是pivx-3.1.1-osx64.tar.gz.

{{% admonition example "1.1 启动PIVX debug console"%}}
$ tar xvf pivx-3.1.1-osx64.tar.gz<br>
$ cd pivx-3.1.1/bin<br>
$ ls<br>
pivx-cli     pivx-qt      pivx-tx      pivxd        test_pivx    test_pivx-qt<br>
$ pwd<br>
/Users/zhuwenjun/work/Masternode/pivx-3.1.1/bin<br>
$ open pivx-qt<br>
{{% /admonition %}}


{{% center %}}
![img](/pivx/piv1.png "img")
{{% /center %}}

{{% center %}}
![img](/pivx/piv2.png "img")
{{% /center %}}



***
{{% admonition example "1.2 生成masternode private key"%}}
\> masternode genkey<br>
87j5ZoCqTf45Cwx1Ge63ctbfmNQ7Q941HVtF8ZPcueijhC3JfsR
{{% /admonition %}}

{{% center %}}
![img](/pivx/piv3.png "img")
{{% /center %}}



***
{{% admonition example "1.3 获得masternode账户地址"%}}
\> getaccountaddress test001(chooseAnyNameForYourMasternodeHere)
DPcstRGz6w3kcfFPPqUhT8xbM8munHZiru
{{% /admonition %}}

{{% center %}}
![img](/pivx/piv4.png "img")
{{% /center %}}



***
{{% admonition failure "1.4 发送10000个PIV到masternode账户地址"%}}
> 这个步骤只做演示，没有真正发送10000个PIV到我的masternode账户，主要是因为**穷**。
{{% /admonition %}}

{{% center %}}
![img](/pivx/piv5.png "img")
{{% /center %}}


***
{{% admonition failure "1.5 检查步骤4的发送交易"%}}
\> masternode outputs<br>
[<br>
]

> 这个步骤显示输出空只做演示。<br>因为步骤4没有向masternode账户发送10000个PIV币，所以这步检查也没啥意义，还是因为**穷**。
{{% /admonition %}}

{{% center %}}
![img](/pivx/piv6.png "img")
{{% /center %}}


***
{{% admonition failure "1.6 编辑masternode.conf配置文件"%}}
找到系统中的masternode.conf配置文件并按如下格式编辑<br>
```
\<Name of Masternode\> \<Unique IP address\>:51472 \<masternode private key\> \<Result of "masternode outputs"\> \<The number after the long line in "masternode outputs"\>
```


$ vim ~/Library/Application\ Support/PIVX/masternode.conf<br>
```
test001 114.243.212.29:51472 87j5ZoCqTf45Cwx1Ge63ctbfmNQ7Q941HVtF8ZPcueijhC3JfsR \<Result of "masternode outputs"\> \<The number after the long line in "masternode outputs"\>
```

> 注意⚠️ ：(masternode.conf 里的最后2部分内容需要debug console里masternode output(步骤5)的输出，由于在步骤4没有充值，所以这步也只做演示)<br>

{{% /admonition %}} 


<br>**masternode.conf在各个系统中的路径**

系统    | masternode.conf路径 
------- | ------------ 
Windows | %Appdata%/PIVX
Linux   | ~
MAC     |~/Library/Application Support/PIVX/
***
<br><br>



## 2. Remote wallet config

根据[masternode-setup/pivx](https://masternode-setup.com/pivx/hot-cold-vps.html)的步骤在Docker中启动masternode

***
{{% admonition example "2.1 在矿机上拉取docker镜像并运行"%}}
```
docker pull retlas/masternode-pivx
docker run -d --name pivx-wallet -v /tmp/pivx:/root/.pivx -p 51472:51472 retlas/masternode-pivx pivxd -daemon=0 -rpcport=51473 -rpcallowip=172.17.0.1 -rpcuser=rpc_user -rpcpassword=rpc_paswd
```

{{% /admonition %}}

{{% admonition example "2.2 配置pivx.conf文件"%}}
```
root@pi:/tmp/pivx# cat pivx.conf
masternode=1
masternodeprivkey=87j5ZoCqTf45Cwx1Ge63ctbfmNQ7Q941HVtF8ZPcueijhC3JfsR
masternodeaddr=114.243.212.29:51472
```
{{% /admonition %}}


{{% admonition question docker启动参数问题%}}
Docker 启动masternode镜像时的端口映射和rpc user/password没有找到设置依据, 导致查看container时
一直时Exit状态, 如果这一步启动成功，将可以继续以下2.3的步骤
{{% /admonition %}}
{{% center %}}
![img](/pivx/piv8.png "img")
{{% /center %}}


{{% admonition example "2.3 Remaining steps"%}}
docker restart pivx-wallet<br>

**Local hot wallet setup (next)**

* In masternode tab, clic start

**On vps**

* check debug logs :
  tail -f /data/pivx/debug.log
* check masternode status :
  docker exec pivx-wallet pivx-cli masternode status
* check if masternode is in global list :
  docker exec pivx-wallet pivx-cli masternodelist full

**On desktop wallet**

* in debug console :
  masternode outputs
* check debug.log
{{% /admonition %}}









