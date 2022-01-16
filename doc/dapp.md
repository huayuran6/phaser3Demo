[toc]

### 1. Geth私链环境搭建

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
8. 启动geth:`geth --identity "myetherum" --http --http.corsdomain "*" --datadir "chain" --http.api  "db,eth,net,web3,personal,miner" --networkid 6500 console 2>1.txt`
9. `eth.blockNumber; personal.newAccount("123"); eth.accounts; miner.start();eth.mining`

#### 2. ganache-cli 和 web3

1. `npm install -g ganache-cli; ganache-cli`
2. `mkdir ganache2; cd ganache2; npm install web3 -save`
3. `vim test.js; node test.js`
    ```js
    var web3 = require('web3');
    console.log('test');
    var web3 = new web3(new web3.providers.HttpProvider('http://localhost:8545'));
    console.log(web3.version);
    web3.eth.getAccounts().then(console.log);
    ```
4. npm install express -g; apt install node-express-generator; mkdir dapp; cd dapp;express -e MyDapp; cd MyDapp; npm
   install; npm start
5. 修改routes/index.js
   ```js
   var express = require('express');
   var router = express.Router();
   var Web3 = require('web3');
   var web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));

   web3.eth.getAccounts().then(console.log);
   /* GET home page. */
   router.get('/', function(req, res, next) {
     web3.eth.getAccounts().then(function(accounts){
     var account = accounts[0];
     web3.eth.getBalance(account).then(function(balance){
       var amount = web3.utils.fromWei(balance,'ether');
       res.render('index', { account: account, balance: amount });
       });
     });
   });
   module.exports = router;
   ```
   修改app.js
   ```js
   var app = express();
   var ejs = require('ejs');
   // view engine setup
   app.set('views', path.join(__dirname, 'views'));
   app.engine('.html', ejs.__express);
   app.set('view engine', 'html');
   ```
   views下新建index.html
   ```html
   <!DOCTYPE html>
   <html>
           <head>
                   <title>zhang hu yu e </title>
                   <link rel='stylesheet' href='/stylesheets/style.css'/>
           </head>
           <body>
                   <h1>zhang hu shi : <%= account %></h1>
                   <h1>yu e shi: <%= balance %></h1>
           </body>
   </html>
   ```

#### 3. web3部署智能合约

1. 去https://remix.ethereum.org 编写
   ```
   pragma solidity 0.8.0;
   contract MyCounter{
   uint256 counter;
   
       constructor() public{
           counter = 1;
       }
       function kipCounter(uint step) public {
           counter += step;
       }
   
       function getCounter() public view returns (uint256){
           return counter;
       }
   }
   ```
2. 点击 compilation details 按钮 选择WEB3DEPLOY
   ```JS
   var mycounterContract = new web3.eth.Contract([{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"inputs":[],"name":"getCounter","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"uint256","name":"step","type":"uint256"}],"name":"kipCounter","outputs":[],"stateMutability":"nonpayable","type":"function"}]);
   var mycounter = mycounterContract.deploy({
   data: '0x608060405234801561001057600080fd5b5060016000819055506101dd806100286000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c80638ada066e1461003b578063e24801d814610059575b600080fd5b610043610075565b60405161005091906100e6565b60405180910390f35b610073600480360381019061006e91906100ae565b61007e565b005b60008054905090565b8060008082825461008f9190610101565b9250508190555050565b6000813590506100a881610190565b92915050565b6000602082840312156100c057600080fd5b60006100ce84828501610099565b91505092915050565b6100e081610157565b82525050565b60006020820190506100fb60008301846100d7565b92915050565b600061010c82610157565b915061011783610157565b9250827fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff0382111561014c5761014b610161565b5b828201905092915050565b6000819050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052601160045260246000fd5b61019981610157565b81146101a457600080fd5b5056fea2646970667358221220101cbc14d1e950c10b3caa02fda09428818a9509b461ed413f8c41d65b5548b464736f6c63430008000033',
   arguments: [
   ]
   }).send({
   from: web3.eth.accounts[0],
   gas: '4700000'
   }, function (e, contract){
   console.log(e, contract);
   if (typeof contract.address !== 'undefined') {
   console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
   }
   })
   ```
3. 部署合约 获取合约地址
   ```
   新建一个deploy目录  npm install web3 -save,创建一个deploy.js WEB3DEPLOY粘入
   var dapptestContract = new web3.eth.Contract([{"constant":false,"inputs":[{"name":"num","type":"uint256"}],"name":"test1","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"test2","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"}]);

   //BYTECODE object中
   //dapptestContract.options.data = "608060405234801561001057600080fd5b50600160008190555060e7806100276000396000f3006080604052600436106049576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806319ae899414604e57806366e41cb7146078575b600080fd5b348015605957600080fd5b5060766004803603810190808035906020019092919050505060a0565b005b348015608357600080fd5b50608a60b2565b6040518082815260200191505060405180910390f35b80600080828254019250508190555050565b600080549050905600a165627a7a7230582042fa822fa9daeedbd358fbdb7ab600df0b4024a9a32ca4221e297bec099c2c460029";
   
   var dapptest = dapptestContract.deploy({
   data: '0x608060405234801561001057600080fd5b50600160008190555060e7806100276000396000f3006080604052600436106049576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806319ae899414604e57806366e41cb7146078575b600080fd5b348015605957600080fd5b5060766004803603810190808035906020019092919050505060a0565b005b348015608357600080fd5b50608a60b2565b6040518082815260200191505060405180910390f35b80600080828254019250508190555050565b600080549050905600a165627a7a7230582042fa822fa9daeedbd358fbdb7ab600df0b4024a9a32ca4221e297bec099c2c460029',
   arguments: [  ]
   }).send({
   from:'0x588CD83b1c6856F6375270348C278b3737012EF2',    // 从ganache-cli当中生成的十个账户当中随机选一个
   gas: '4700000'
   }, function (e, contract){
   console.log(e, contract);
   if (typeof contract.address !== 'undefined') {
   console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
   }
   }).then(function(contract){
   console.log("Contract address:",contract.options.address);
   })
   ```
4. 初始化项目express 必须 npm install web3 -save
5. 修改routers/index.js
   ```js
   var express = require('express');
   var router = express.Router();
   
   var Web3 = require('web3');
   var web3 =  new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));
   
   // web3.eth.getAccounts().then(console.log);
   
   
   var myContract = new web3.eth.Contract([
   {
   "constant": false,
   "inputs": [
   {
   "name": "a",
   "type": "uint256"
   }
   ],
   "name": "changeCounter",
   "outputs": [],
   "payable": false,
   "stateMutability": "nonpayable",
   "type": "function"
   },
   {
   "inputs": [],
   "payable": false,
   "stateMutability": "nonpayable",
   "type": "constructor"
   },
   {
   "constant": true,
   "inputs": [],
   "name": "getCounter",
   "outputs": [
   {
   "name": "",
   "type": "uint256"
   }
   ],
   "payable": false,
   "stateMutability": "view",
   "type": "function"
   }
   ], '0x2247fdbC867ab4A6fb85787Daa0c12B9aB27FB60');//1. remix智能合约ABI  2. 上一步中合约部署地址
   
   
   
   //myContract.methods.getCounter().call({from: '0x652945236a3d96e631f95c9373Fc5d0774Ae2e92'}, function(error, result){
   //     console.log("counter:",result);
   //});
   
   var init_counter = 0;
   var current_counter = 0;
   
   myContract.methods.getCounter().call().then(function(counter){
   console.log('init counter:',counter);
   init_counter = counter;
   
   });
   /* GET home page. */
   router.get('/', function(req, res, next) {
   myContract.methods.changeCounter(5).send({from: '0x652945236a3d96e631f95c9373Fc5d0774Ae2e92'})//测试账户
   .then(function(){
   myContract.methods.getCounter().call().then(function(counter){
   console.log('current counter:',counter);
   current_counter = counter;
   res.render('index', {init:init_counter,current:current_counter });
   });
   });
   });
   
   module.exports = router;

   ```
6. 修改app.js 文件
   ```js
   var createError = require('http-errors');
   var express = require('express');
   var path = require('path');
   var cookieParser = require('cookie-parser');
   var logger = require('morgan');
   
   var indexRouter = require('./routes/index');
   var usersRouter = require('./routes/users');
   
   var app = express();
   
   
   var ejs = require('ejs');
   
   // view engine setup
   app.set('views', path.join(__dirname, 'views'));
   app.engine('.html',ejs.__express);
   app.set('view engine', 'html');
   //app.set('view engine', 'ejs');
   
   app.use(logger('dev'));
   app.use(express.json());
   app.use(express.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, 'public')));
   
   app.use('/', indexRouter);
   app.use('/users', usersRouter);
   
   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
   next(createError(404));
   });
   
   // error handler
   app.use(function(err, req, res, next) {
   // set locals, only providing error in development
   res.locals.message = err.message;
   res.locals.error = req.app.get('env') === 'development' ? err : {};
   
   // render the error page
   res.status(err.status || 500);
   res.render('error');
   
   module.exports = app;}
   )
   ```
7. views中创建index.html文件
   ```html
   <!DOCTYPE html>
   <html>
     <head>
       <title>调用智能合约</title>
       <link rel='stylesheet' href='/stylesheets/style.css' />
     </head>
     <body>
       <h1>init:<%= init%></h1>
       <h1>current:<%= current%></h1>
     </body>
   </html>
   ```

#### 4. truffle
1. sudo npm install -g truffle
2. mkdir Voting; truffle unbox webpack; 编译 truffle compile; 部署 truffle migrate; 
   //也可以通过如下方式获取
   git clone https://github.com/truffle-box/webpack-box.git
   修改truffle-config.js networks中 develop注掉
   ```
   development:{
    host: "127.0.0.1",
    port: 8545,
    newtwork_id: "*",
   }
   ```
3. 在contracts目录下新增Voting.sol合约
   ```solidity
   pragma solidity >=0.4.21 <0.7.0;

   contract Voting{
   bytes32[] public candidateList;
   mapping(bytes32 => uint8) public votesReceived;

    constructor(bytes32[] memory candidateListName) public {
        candidateList = candidateListName;
    }

    function validCadidiate(bytes32 candidateName) internal view returns(bool){
        for(uint8 i = 0; i < candidateList.length; i++){
            if(candidateName == candidateList[i]){
                return true;
            }
        }
        return false;
    }

    function voteForCanditate(bytes32 candidateName) public{
        require(validCadidiate(candidateName));
        votesReceived[candidateName] += 1;
    }

    function totalVotesFor(bytes32 candidateName) view public returns(uint8){
        require(validCadidiate(candidateName));
        return votesReceived[candidateName];
    }
   }
   ```
   
4. 修改migrations目录下的2_deploy_contracts.js
   ```js
   // const ConvertLib = artifacts.require("ConvertLib");
   // const MetaCoin = artifacts.require("MetaCoin");
   const Voting = artifacts.require("Voting");
   
   module.exports = function(deployer) {
   // deployer.deploy(ConvertLib);
   // deployer.link(ConvertLib, MetaCoin);
   // deployer.deploy(MetaCoin);
   deployer.deploy(Voting,[
    "0x4100000000000000000000000000000000000000000000000000000000000000",
    "0x4200000000000000000000000000000000000000000000000000000000000000"]);
   
   };
   ```
5. 主目录下编译 truffle compile;部署 truffle migrate; 控制台查看 truffle console; let instance = await Voting.deployed(); instance;
6. 修改app目录下的index.html 和 index.js
   ```html
   <!DOCTYPE html>
   <html>
     <head>
       <title>MyVoting DAPP/ Frontend</title>
     </head>
     <body>
       <h1> Voting DAPP</h1>
       <p>Alice: <strong id = "alice">loading...</strong> tickets</p>
       <p>Bob: <strong id = "bob">loading...</strong> tickets</p>
       <label>VoteFor :</label>
       <input type="text" id = "candidate"/>
       <button onclick="App.voteForCandidate()">vote</button>
       <script src="index.js"></script>
     </body>
   </html>
   ```
   ```js
   import Web3 from "web3";
   import votingArtifact from "../../build/contracts/Voting.json";
   
   const aInBytes32 = "0x4100000000000000000000000000000000000000000000000000000000000000";
   const bInBytes32 = "0x4200000000000000000000000000000000000000000000000000000000000000";
   
   const App = {
   web3: null,
   account: null,
   voting: null,
   
   start: async function() {
   const { web3 } = this;
   
       try {
         // get contract instance
         const networkId = await web3.eth.net.getId();
         const deployedNetwork = votingArtifact.networks[networkId];
         this.voting = new web3.eth.Contract(
           votingArtifact.abi,
           deployedNetwork.address,
         );
   
         // get accounts
         const accounts = await web3.eth.getAccounts();
         this.account = accounts[0];
   
         // this.refreshBalance();
         this.ready();
       } catch (error) {
         console.error("Could not connect to contract or chain.",error);
       }
   },
   
   refresh: async function(id, nameInbytes32){
   const {totalVotesFor} = this.voting.methods;
   const tickets = await totalVotesFor(nameInbytes32).call();
   const element = document.getElementById(id);
   element.innerHTML = tickets.toString();
   },
   
   ready: async function(){
   try {
   this.refresh('alice', aInBytes32);
   this.refresh('bob', bInBytes32);
   } catch (error) {
   console.log(error);
   }
   },
   
   voteForCandidate: async function(){
   console.log('vote')
   try{
   const {voteForCanditate} = this.voting.methods;
   const candidateName = document.getElementById("candidate").value;
   if(candidateName === "Alice"){
   await voteForCanditate(aInBytes32).send({from: this.account});
   this.refresh('alice',aInBytes32);
   }else if(candidateName === "Bob"){
   await voteForCanditate(bInBytes32).send({from: this.account});
   this.refresh('bob',bInBytes32);
   console.log('vote bob');
   }
   }catch(err) {
   console.log(err);
   }
   }
   };
   
   window.App = App;
   
   window.addEventListener("load", function() {
   if (window.ethereum) {
   // use MetaMask's provider
   App.web3 = new Web3(window.ethereum);
   window.ethereum.enable(); // get permission to access accounts
   } else {
   console.warn(
   "No web3 detected. Falling back to http://127.0.0.1:7545. You should remove this fallback when you deploy live",
   );
   // fallback - use your fallback strategy (local node / hosted node + in-dapp id mgmt / fail)
   App.web3 = new Web3(
   new Web3.providers.HttpProvider("http://127.0.0.1:7545"),
   );
   }
   
   App.start();
   });

   ```
7. 如果您已经拥有，请确保使用隐身窗口 已安装 MetaMask（或暂时禁用 MetaMask）。 否则，应用程序 将尝试使用 MetaMask 中指定的网络，而不是 localhost:8545 下的开发网络。
