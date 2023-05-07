# 1.安装虚拟机

本次学习使用了两台虚拟机，每台虚拟机具有3个结点。
使用的平台和OS为VMware station Pro 17和 Ubuntu 20.04 LTS。

虚拟机安装步骤不再赘述。



# 2.创建创世区块（Genesis Block）

使用的共识算法为Clique，简而言之，就是理论上每个结点轮流封装新的区块（一个结点在封装区块之后需要等待一段时间才能再次封装新的区块）

虚拟机A包括结点：node1,node2
虚拟机B包括结点：node3,node4

以上结点均可以封装新的区块

具体流程如下

### 2.1安装geth

go下载地址：https://go.dev/doc/install
添加环境变量

```
export PATH=$PATH:/usr/local/go/bin
```

安装geth

```
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

### 2.2创建node

在虚拟机A上创建node1,node2的文件夹

```
mkdir node1 node2
```

创建一个新的节点

```
geth --datadir node1 account new
```

虚拟机B上重复上述操作，获得的四个地址为

```
node1:0x659e1BFC896E337B6b86012381D2cAbB3A9ca312
node2:0x85A61CEdC733E82594d81945ac6CfEdB264FdEA0
node3:0x69e847d196313e7E7F507Ecf018f9b8CaC416A08
node4:0xcB80c15E1B951B0a482A60ec982761A92AAB4111
```

### 2.2配置genesis.json文件

```json
{
  "config": {
    "chainId": 12345,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "muirGlacierBlock": 0,
    "berlinBlock": 0,
    "londonBlock": 0,
    "arrowGlacierBlock": 0,
    "grayGlacierBlock": 0,
    "clique": {
      "period": 1,
      "epoch": 30000
    }
  },
  "difficulty": "1",
  "gasLimit": "800000000",
  "extradata": "0x0000000000000000000000000000000000000000000000000000000000000000659e1BFC896E337B6b86012381D2cAbB3A9ca31285A61CEdC733E82594d81945ac6CfEdB264FdEA069e847d196313e7E7F507Ecf018f9b8CaC416A08cB80c15E1B951B0a482A60ec982761A92AAB41110000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "alloc": {
    "659e1BFC896E337B6b86012381D2cAbB3A9ca312": { "balance": "100000000000000000000" },
    "85A61CEdC733E82594d81945ac6CfEdB264FdEA0": { "balance": "100000000000000000000" },
    "69e847d196313e7E7F507Ecf018f9b8CaC416A08": { "balance": "100000000000000000000" },
    "cB80c15E1B951B0a482A60ec982761A92AAB4111": { "balance": "100000000000000000000" }
  }
}
```

初始化所有结点

```
geth init --datadir node1 genesis.json
geth init --datadir node2 genesis.json
geth init --datadir node3 genesis.json
geth init --datadir node4 genesis.json
```

### 2.3启动bootnode

```sh
bootnode -genkey boot.key
bootnode -nodekey boot.key -addr :30305
```

```
enode://0b06c1707bb2400f0f826c7c0280121f8d2ac581edd0b041599f058ec7d0df7f6adf4b70642833993ef92b52cc68fcdb40458bdb89f0f32d06d4e20fc870ba95@127.0.0.1:0?discport=30305
```

### 2.4启动节点

```sh
geth --datadir node1 --port 30311 --bootnodes enode://0b06c1707bb2400f0f826c7c0280121f8d2ac581edd0b041599f058ec7d0df7f6adf4b70642833993ef92b52cc68fcdb40458bdb89f0f32d06d4e20fc870ba95@127.0.0.1:0?discport=30305  --networkid 123454321 --unlock 0x659e1BFC896E337B6b86012381D2cAbB3A9ca312 --password node1/password.txt --authrpc.port 8551 --miner.etherbase=0x659e1BFC896E337B6b86012381D2cAbB3A9ca312
```

```sh
geth --datadir node2 --port 30312 --bootnodes enode://0b06c1707bb2400f0f826c7c0280121f8d2ac581edd0b041599f058ec7d0df7f6adf4b70642833993ef92b52cc68fcdb40458bdb89f0f32d06d4e20fc870ba95@127.0.0.1:0?discport=30305  --networkid 123454321 --unlock 0x85A61CEdC733E82594d81945ac6CfEdB264FdEA0 --password node2/password.txt --authrpc.port 8552 --miner.etherbase=0x85A61CEdC733E82594d81945ac6CfEdB264FdEA0
```

```sh
geth --datadir node3 --port 30311 --bootnodes enode://0b06c1707bb2400f0f826c7c0280121f8d2ac581edd0b041599f058ec7d0df7f6adf4b70642833993ef92b52cc68fcdb40458bdb89f0f32d06d4e20fc870ba95@192.168.174.129:0?discport=30305  --networkid 123454321 --unlock 0x69e847d196313e7E7F507Ecf018f9b8CaC416A08 --password node3/password.txt --authrpc.port 8551 --miner.etherbase=0x69e847d196313e7E7F507Ecf018f9b8CaC416A08
```

```sh
geth --datadir node4 --port 30312 --bootnodes enode://0b06c1707bb2400f0f826c7c0280121f8d2ac581edd0b041599f058ec7d0df7f6adf4b70642833993ef92b52cc68fcdb40458bdb89f0f32d06d4e20fc870ba95@192.168.174.129:0?discport=30305  --networkid 123454321 --unlock 0xcB80c15E1B951B0a482A60ec982761A92AAB4111 --password node3/password.txt --authrpc.port 8552 --miner.etherbase=0xcB80c15E1B951B0a482A60ec982761A92AAB4111
```

### 2.5控制台测试

进入node1的控制台

```sh
geth attach node1/geth.ipc
```

检查对等节点

```sh
net.peerCount
admin.peers
```

在所有节点中添加可信节点（添加之后似乎需要重新启动节点才能正常运作）

```
clique.propose("0x659e1BFC896E337B6b86012381D2cAbB3A9ca312", true)
clique.propose("0x85A61CEdC733E82594d81945ac6CfEdB264FdEA0", true)
clique.propose("0x69e847d196313e7E7F507Ecf018f9b8CaC416A08", true)
clique.propose("0xcB80c15E1B951B0a482A60ec982761A92AAB4111", true)
```

发起一比交易

```javascript
eth.sendTransaction({
  to: '0x69e847d196313e7E7F507Ecf018f9b8CaC416A08',
  from: eth.accounts[0],
  value: 5000000000000000000
});
```

所有节点开始封装区块

```
miner.start()
```

查看最近区块的封装者，可以发现是轮流进行的

```
clique.getSnapshot()
```



参考资料：

go-ethereum Private Networks：https://geth.ethereum.org/docs/fundamentals/private-network#clique-example
不同主机间POA以太坊节点连接: https://www.freesion.com/article/96631389256/

Clique PoA consensus 建立Private chain: https://blog.csdn.net/code_segment/article/details/78160660

