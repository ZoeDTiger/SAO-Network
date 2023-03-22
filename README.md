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
    
#### 安装Go
    cd $HOME
    wget "https://go.dev/dl/go1.19.4.linux-amd64.tar.gz"
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -zxvf go1.19.4.linux-amd64.tar.gz
    
    echo "export GOROOT=/usr/local/go" |  sudo tee -a /etc/profile
    echo "export GOPATH=$HOME/go" |  sudo tee -a /etc/profile
    echo "export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin" |  sudo tee -a /etc/profile
    echo "export GO111MODULE=on" |  sudo tee -a /etc/profile
    echo "export GOPROXY=https://goproxy.cn" |  sudo tee -a /etc/profile
    
    source /etc/profile
    go version

#### 源码构建SAO Network
    git clone https://github.com/SaoNetwork/sao-consensus.git
    cd sao-consensus/
    git checkout testnet1
    make
    which saod
    sudo cp $HOME/go/bin/saod /usr/local/bin/

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
    
<img width="548" alt="微信截图_20230317133817" src="https://user-images.githubusercontent.com/100336530/225821709-84c14e75-443e-42ae-80b9-b6010da461a0.png">

## 部署验证节点
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

#### 相关操作
##### 委托人质押
###### 比如：validatorAddress=saovaloper1x7l79704jdmmscjtjltsa0n7nx33a3p95jc8kj, amountToBound=10000000sao, gasPrice=0.0025sao
    saod tx staking delegate <validatorAddress> <amountToBond> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>

##### 转质押
###### 比如：stcValidatorAddress=saovaloper1r5targh3z3e2s3q9lgv68y0lkpd2urqxrugpxq, destValidatorAddress=saovaloper1x7l79704jdmmscjtjltsa0n7nx33a3p95jc8kj,amountToRedelegate=100000000sao, gasPrice=0.0025sao
    saod tx staking redelegate <srcValidatorAddress> <destValidatorAddress> <amountToRedelegate> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>

##### 取消委押
###### 比如：validatorAddress=saovaloper1x7l79704jdmmscjtjltsa0n7nx33a3p95jc8kj, amountToUnbound=10000000sao, gasPrice=0.0025sao
    saod tx staking unbond <validatorAddress> <amountToUnbond> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>

##### 解除监禁
###### 比如：destKeyAddress=sao1x7l79704jdmmscjtjltsa0n7nx33a3p9rs98jh,gasPrice=0.0025sao
    saod tx slashing unjail --from <destKeyAddress> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>

## 部署存储节点
    前提：需要运行共识节点，并且共识节点完成区块同步。

#### 源码构建sao-node
    git clone https://github.com/SAONetwork/sao-node.git
    cd sao-node/
    git checkout testnet1
    make
    sudo cp $HOME/sao-node/saoclient $HOME/sao-node/saonode /usr/local/bin/

#### 创建存储节点账户
    chain-address可以用官方公开的rpc endpoint，也可使用本地共识节点的endpoint，分别为：
    https://rpc-testnet-node0.sao.network:443
    http://127.0.0.1:26657
    
##### 创建账户
    saonode --chain-address <chain-address> --keyring <keyring-dir> account create --key-name <key-name>
<img width="715" alt="微信截图_20230317155905" src="https://user-images.githubusercontent.com/100336530/225847552-30f09d0b-1238-4e6a-bf04-8bd9a16786bd.png">


##### 给存储账户转账
    saod tx bank send <from> sao18a4d7t602zfgtpk2m4muyahcguyqr6hsc58taw 100000000stake
    saod query bank balances sao18a4d7t602zfgtpk2m4muyahcguyqr6hsc58taw
<img width="542" alt="微信截图_20230317155922" src="https://user-images.githubusercontent.com/100336530/225847796-17b136ff-cc9e-45f7-830d-ab4ccc780406.png">


#### 初始化和启动节点
    默认的工作目录为~/.sao-node，可以通过--repo参数改变工作目录
    saonode --repo <repo> --chain-address <chain-address> init --creator <node-account>
    
    比如：
    saonode --chain-address http://120.226.39.113:26657 init --creator sao18a4d7t602zfgtpk2m4muyahcguyqr6hsc58taw
<img width="754" alt="微信截图_20230317161601" src="https://user-images.githubusercontent.com/100336530/225849991-d8813e33-6d01-4bf1-abbb-0be550d86e8a.png">

##### 运行节点
    screen -S sao-storage
    saonode --vv run

#### 检查节点是否正常
    netstat -antp | grep 203.23.128.181 | grep ESTABLISHED | grep 5153 
<img width="581" alt="微信截图_20230317174508" src="https://user-images.githubusercontent.com/100336530/225869806-518e7b16-e145-4134-a1db-8917ef6a4d29.png">












