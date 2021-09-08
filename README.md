# IBC-relayer-Task-for-Kichain
We are faced with the task of performing cross-chain transactions on the KiChain network.
For this we need a Cosmos IBC relayer and a second network that supports IBC transactions. For example, I chose Crypto.org Croeseid Testnet.
***
#### 1.Download and install the relayer:
git clone https://github.com/cosmos/relayer.git  
cd relayer  
make install  
cd
rly version
version: 1.0.0-rc1–152-g112205b

#### 2.Initialize the configuration of the relayer:  
rly config init  

#### 3.Create network configuration files for the relayer  

##### for Kichain network:  

sudo nano kichain-t-4_config.json  
{  
  "chain-id": "kichain-t-4",  
  "rpc-addr": "http://127.0.0.1:26657",  
  "account-prefix": "tki",  
  "gas": 20000,  
  "gas-prices": "0.025utki",  
  "trusting-period": "48h"  
}

#### for Croeseid Testnet:

sudo nano testnet-croeseid-4_config.json  
{  
  "chain-id": "testnet-croeseid-4",  
  "rpc-addr": "http://127.0.0.1:26652",  
  "account-prefix": "tcro",  
  "gas-adjustment": 1.5,  
  "gas-prices": "0.025basetcro",  
  "trusting-period": "48h"  
}

#### 4.Add the created files *.json to the relayer configuration:  
rly chains add -f testnet-croeseid-4_config.json  
rly chains add -f kichain-t-4_config.json

#### 5. Create new wallets to interact with the relayer or restore existing ones in networks:  
rly keys restore kichain-t-4 name of your wallet "mnemonic phrase from wallet"  
rly keys add testnet-croeseid-4 name of your wallet

#### 6. Add the newly created keys to the config of the relayer:  
rly chains edit kichain-t-4 key name of your wallet  
rly chains edit testnet-croeseid-4 key name of your wallet

#### 7. Top up the wallets of the relayers - for Croeseid Testnet we use the faucet https://crypto.org/faucet

#### 8. Checking the wallet balance of the relayers:  
rly q balance testnet-croeseid-4
rly q balance kichain-t-4  

*Example:  
rly q bal testnet-croeseid-4  
189282977basetcro  
rly q bal kichain-t-4  
1600673060utki* 

#### 9. initialize the light clients on each network:  
rly light init testnet-croeseid-4 -f  
rly light init kichain-t-4 -f

#### 10. Let's check all the settings at this stage:  
rly chains list  
0: testnet-croeseid-4   -> key(✔) bal(✔) light(✔) path(✔)  
1: kichain-t-4          -> key(✔) bal(✔) light(✔) path(✔)  

#### 11. Create a tunnel between the two networks:  
rly paths generate testnet-croeseid-4 kichain-t-3 transfer --port=transfer

#### 12. Checking the created channel:
rly paths list -d  
0: transfer -> chns(✔) clnts(✔) conn(✔) chan(✔) (testnet-croeseid-4:transfer<>kichain-t-4:transfer)  
*If you are lucky and you see this conclusion, then skip step 13  
If your output looks something like this -   
0: transfer             -> chns(✔) clnts(x) conn(x) chan(x)  
then we perform step 13*

#### 13. Trying to open channels relayers:  
rly tx link transfer  
*Example:  
★ Channel created: [testnet-croeseid-4]chan{channel-13}port{transfer} -> [kichain-t-4]chan{channel-37}port{transfer}* 

#### 14. Try send tokens from Croeseid to KiChain and back:  
rly tx transfer [src-chain-id] [dst-chain-id] [amount] [dst-addr] [flags]  

rly tx transfer testnet-croeseid-4 kichain-t-4 10000basetcro tki1neuk5fhrv95wx8lld8lpf0cunxjukl59q2y3kt --path transfer -d  
hash(135E8770D3F54B6ADBB7E8153D96769EBE4851B68805CA5EC445BAFA6189E509)  
https://crypto.org/explorer/croeseid4/tx/135E8770D3F54B6ADBB7E8153D96769EBE4851B68805CA5EC445BAFA6189E509

rly tx transfer kichain-t-4 testnet-croeseid-4 100000utki tcro1fes4lqj23vnrkqqu6jc0qu3ujr7unc39l4x6vu --path transfer -d  
hash(712064649D270627FF7EFC2A9E3F856792C56B5647F05AC4C8A268CF07BFF680)  
https://ki.thecodes.dev/tx/712064649D270627FF7EFC2A9E3F856792C56B5647F05AC4C8A268CF07BFF680  

#### 15. To maintain the operation of the relayer, we will create a service file:  
sudo tee /etc/systemd/system/rlyd.service > /dev/null <<EOF  
[Unit]  
Description=relayer client  
After=network-online.target, KiChain.service  
                                                            [Service]  
                                                            User=$USER  
                                                            ExecStart=$(which rly) start transfer  
                                                            Restart=always  
                                                            RestartSec=3  
                                                            LimitNOFILE=65535  
                                                            [Install]  
                                                            WantedBy=multi-user.target  
                                                            EOF

#### 16. We start the service file                                              
sudo systemctl daemon-reload  
sudo systemctl enable rlyd  
sudo systemctl start rlyd 

#### 17. To check the logs of the relay, use:  
journalctl -u rlyd -f



