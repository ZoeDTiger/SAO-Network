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

#### 运行节点
    screen -S sao
    saod start
    
#### 查看区块同步高度
    saod status 2>&1 | jq .SyncInfo

## 部署验证人节点
    前提：
    1、需要运行共识节点，并且共识节点完成区块同步；
    2、需要创建SAO账户，并且帐户中有一定的SAO测试币。
    
#### 创建帐户
    saod keys add <your_key_name>
    
#### 创建验证器
    saod tx staking create-validator \
    --amount=10000000sao \
    --pubkey=$(saod tendermint show-validator) \
    --moniker="dekun-sao" \
    --chain-id=sao-testnet1 \
    --commission-rate="0.10" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.01" \
    --min-self-delegation="1000000" \
    --gas="2000000" \
    --gas-prices="0.0025sao" \
    --from=<your_key_address>

#### 修改验证器参数
    saod tx staking edit-validator \
    --new-moniker=<new_node_name> \
    --chain-id=sao-testnet1 \
    --gas="2000000" \
    --gas-prices="0.0025sao" \
    --from=<your_key_address>

#### 验证节点是否正常
    如果以下命令返回任何内容，则说明验证器处于活跃状态：
    saod query tendermint-validator-set | grep "$(saod tendermint show-address)"

#### 委托人质押
    saod tx staking delegate <validatorAddress> <amountToBond> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>
    比如：<validatorAddress>=saovaloper1x7l79704jdmmscjtjltsa0n7nx33a3p95jc8kj, <amountToBound>=10000000sao, <gasPrice>=0.0025sao


























