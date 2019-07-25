---
title: "Smartcash 货币主节点"
date: 2019-01-16T20:24:49+08:00
draft: false
---

1. smartcash的masternode官方称作smartnode<br>
2. 建立smartnode节点要求：10000个smartcash，每个smartnode需要1GB的内存空间，一个公网的IPV4地址和此地址上的9678端口。<br>
3. masternode.sh可用于建立smartnode节点，并为每个节点创建名为smartnode开头的目录文件，比如smartnode1、smartnode2等。<br>
masternode.sh脚本须运行在 Ubuntu 18.04系统上, 在VPS上建立多个smartnode时，需要提前在/etc/netplan/10-ens3.yaml中设置好对应数量的IPV4地址（可参考Vultr VPS服务器给出的设置）。

**一、 smartnode节点设置**

将masternode.sh拷贝到VPS中，并执行脚本<br>
`第一步：选择要安装smartnode则填入1。`<br>
`第二步：选择要安装smartnode的节点数量。`<br>
如下图中(Max :2)代表根据内存大小，最多能在此VPS上部署2个smartnode节点，同时VPS上已经有2个公网的IPV4地址，所以选择2。`

masternode.sh在安装过程中会先建立一个名为bootstrap的smartnode节点，当bootstrap的节点同步到最新的block区块后，masternode.sh将会根据用户选择的smartnode节点数量将bootstrap拷贝成新的smartnode1、smartnode2 ...节点目录，并在每个节点目录下生成smartcash.conf文件。<br>
![](/smartnode/1.png "img")

出现以下log时，代表smartnode1和smartnode2已安装完毕，可以用ps查看下smartnode1和2对应的smartcashd是否正在运行
![](/smartnode/2.png "img")

查看smartnode1 和 smartnode2目录下的smartcash.conf文件，这个文件中的`externalip，port和smartnodeprivkey`分别用于稍后在smartcash客户端钱包中建立smartnode, `smartnodeprivkey`就是smartcash钱包中的genkey。
![](/smartnode/3.png "img")

使用如下命令可以查看smartnode节点的block数量是否已经同步到[最新](https://smart.ccore.online)
```shell
smartcash-cli -datadir=/root/smartnode/smartnode1 getinfo
smartcash-cli -datadir=/root/smartnode/smartnode2 getinfo
```

```shell
root@smartnode:~# smartcash-cli -datadir=/root/smartnode/smartnode1 getinfo
{
  "version": 1020600,
  "protocolversion": 90026,
  "walletversion": 130000,
  "balance": 0.00000000,
  "blocks": 339222,
  "timeoffset": 0,
  "connections": 8,
  "proxy": "",
  "difficulty": 212421.3970612101,
  "testnet": false,
  "keypoololdest": 1547686271,
  "keypoolsize": 199,
  "paytxfee": 0.00000000,
  "relayfee": 0.00100000,
  "errors": ""
}
root@smartnode:~# smartcash-cli -datadir=/root/smartnode/smartnode2 getinfo
{
  "version": 1020600,
  "protocolversion": 90026,
  "walletversion": 130000,
  "balance": 0.00000000,
  "blocks": 347781,
  "timeoffset": 0,
  "connections": 8,
  "proxy": "",
  "difficulty": 194537.1604978901,
  "testnet": false,
  "keypoololdest": 1547686276,
  "keypoolsize": 199,
  "paytxfee": 0.00000000,
  "relayfee": 0.00100000,
  "errors": ""
}
```

使用如下命令可以查看smartnode与smartcash钱包的连接状态
```shell
smartcash-cli -datadir=/root/smartnode/smartnode1 smartnode status
smartcash-cli -datadir=/root/smartnode/smartnode2 smartnode status
```
```shell
root@smartnode:~/smartnode# smartcash-cli -datadir=/root/smartnode/smartnode1 smartnode status
{
  "outpoint": "COutPoint(0000000000000000000000000000000000000000000000000000000000000000, 4294967295)",
  "service": "[::]:0",
  "status": "Node just started, not yet activated"
}
root@smartnode:~/smartnode# smartcash-cli -datadir=/root/smartnode/smartnode2 smartnode status
{
  "outpoint": "COutPoint(0000000000000000000000000000000000000000000000000000000000000000, 4294967295)",
  "service": "[::]:0",
  "status": "Node just started, not yet activated"
}
```
<br>
**二、smartcash钱包设置**

1. 下载[**smartcash钱包**](https://smartcash.cc/wallets/#nodeclient)，安装并等待钱包同步到最新的block。
![](/smartnode/4.png "img")

2. 新建一个收款地址，并起一个标签名，比如SmartNode1，并复制SmartNode1的地址。
![](/smartnode/5.png "img")

3. 向SmartNode1的收款地址上打入10000个smartcash币，这个10000个币中不包含交易费，要完完整整的10000个币。
![](/smartnode/6.png "img")


4. 在smartcash钱包上选择SmartNodes->Create Smartnode创建smartnode，将“smartnode节点设置”中的`externalip，port和smartnodeprivkey`添到选项中。最后点击“Start alias”来启动smartnode节点。
![](/smartnode/7.png "img")

5. 在~/Library/Application Support/Smartcash/smartnode.conf中查看smartcash钱包对应的smartnode配置<br>
`alias IP:port smartnodeprivkey collateral_output_txid collateral_output_index`<br>
示例图片:<br>
![](/smartnode/10.png "img")

<br>
**三、(示例)正常连接的smartcash钱包和smartnode的状态如下**
![](/smartnode/8.png "img")
![](/smartnode/9.png "img")

<br>
**四、smartnode节点建立官方资料**
<br>
[All in One - SmartNode Guide](https://smartcash.cc/wp-content/uploads/2018/07/All-in-One-SmartNode-v1.2.4-2.pdf)<br>
[Guide to Creating a SmartCash SmartNode VPS in 10 Minutes with Vultr](https://smartcash.blockchainlibrary.org/2018/01/guide-to-creating-a-smartcash-smartnode-vps-in-10-minutes-with-vultr/)
