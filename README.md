# IBC-relayer-Task-for-Kichain
We are faced with the task of performing cross-chain transactions on the KiChain network.
For this we need a Cosmos IBC relayer and a second network that supports IBC transactions. For example, I chose Crypto.org Croeseid Testnet.
***
#### 1.Download and install the relayer:
git clone https://github.com/cosmos/relayer.git  
cd relayer  
make install  
cd

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






