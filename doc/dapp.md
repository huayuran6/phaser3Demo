#### 1.Geth私链环境搭建
1. sudo apt-get install software-properties-common
2. sudo add-apt-repository -y ppa:ethereum/ethereum
3. sudo apt-get update
4. sudo apt-get install ethereum
5. geth version
6. vim genesis.json
    ```json
    {
      "nonce":"0x0000000000000042",
      "difficulty":"0x200",
      "mixhash":"0x0000000000000000000000000000000000000000000000000000000000000000",
      "coinbase":"0x0000000000000000000000000000000000000000",
      "timestamp":"0x00",
      "parentHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
      "extraData":"0x",
      "gasLimit":"0x2CD29C0",
      "alloc":{},
      "config":{
        "chainId":6500,
        "homesteadBlock":0
      }
   }
    ```
7. 初始化`geth --datadir "chain" init genesis.json`
8. 启动geth:geth --identity "myetherum" --rpc --rpccorsdomain "*" --datadir "chain" --port "30303" --rpcapi  "db,eth,net,web3,personal,miner" --networkid 6500 console 2>1.txt
9. geth --identity "myethereum" --rpc --rpccorsdomain "*" --datadir "chain" --port "30303" --rpcapi "db,eth,net,web3,personal,miner" --networkid "6500" console 2>1.txt

