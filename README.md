# SAO-Network
    SAO Network是由 Hedra Labs 提供支持的 Web3 去中心化存储基础设施。它提供了一个基于Cosmos SDK和IPFS的去中心化存储协议。
    2023年3月15日推出了Testnet1，参与测试的方式分为：在Notater上做笔记，运行存储节点，运行共识节点，验证人和委托人质押。
    
    SAO存储节点与IPFS共识协议兼容。接受SAO网络共识节点分配的存储交易，提供数据访问服务。
    共识节点可以参与节点投票和验证人选举。SAO网络的共识网络建立在cosmos-sdk的基础上。共识节点将记录整个网络的节点信息和存储数据信息。
    验证者及其委托人通过执行SAO共识协议，以区块规定和交易费用的方式赚取SAO代币。
    
    Notater应用程序：https://notater.sao.network/
    Keplr钱包：https://www.keplr.app/download/
    水龙头网页：https://faucet.testnet.sao.network/

## 部署共识节点
    SAO Network 使用 Go 1.19.1 来编译代码，需要安装Go 1.19.1及以上版本

#### 源码构建
    git clone https://github.com/SaoNetwork/sao-consensus.git
    git checkout testnet1
    make
    which saod

#### 加入测试网，初始化节点
    saod init <your_node_name> --chain-id=sao-testnet1
    
#### 下载配置文件
    cd $HOME/.sao/config
    wget https://github.com/SAONetwork/sao-consensus/releases/download/v0.1.3/app.toml -O app.toml
    wget https://github.com/SAONetwork/sao-consensus/releases/download/v0.1.3/config.toml -O config.toml
    wget https://github.com/SAONetwork/sao-consensus/releases/download/v0.1.3/genesis.json -O genesis.json












