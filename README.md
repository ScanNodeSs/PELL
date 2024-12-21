# PELL
![image](https://github.com/user-attachments/assets/62eaa453-7b3c-442e-9c4e-5b2ef993d10c)




Explorer:https://explorer.corenodehq.com/Pell-Testnet



1.Installation packages and dependencies

```shell
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

2.Go Installation
```shell
cd $HOME
VER="1.23.0"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

3.Install node
```shell
echo "export PELL_PORT="57"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```shell
mkdir -p $HOME/.pellcored/cosmovisor/genesis/bin/
mkdir -p $HOME/.pellcored/cosmovisor/upgrades/v1.0.20/bin
```
```shell
rm -rf ./pellcored-v1.0.0-linux-amd64
wget https://github.com/0xPellNetwork/network-config/releases/download/v1.0.0-ignite-186-genesis/pellcored-v1.0.0-linux-amd64
chmod +x ./pellcored-v1.0.0-linux-amd64
sudo mv ./pellcored-v1.0.0-linux-amd64 /root/.pellcored/cosmovisor/genesis/bin/pellcored
```
```shell
rm -rf ./pellcored-v1.0.20-linux-amd64
wget https://github.com/0xPellNetwork/network-config/releases/download/v1.0.20-ignite/pellcored-v1.0.20-linux-amd64
chmod +x ./pellcored-v1.0.20-linux-amd64
sudo mv ./pellcored-v1.0.20-linux-amd64 /root/.pellcored/cosmovisor/upgrades/v1.0.20/bin/pellcored
```

```shell
sudo ln -sfn $HOME/.pellcored/cosmovisor/upgrades/v1.0.20 $HOME/.pellcored/cosmovisor/current
sudo ln -sfn $HOME/.pellcored/cosmovisor/current/bin/pellcored /usr/local/bin/pellcored
```
```shell
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.7.0
cd
```

```shell
mkdir -p /root/.pellcored/lib
wget "https://github.com/CosmWasm/wasmvm/releases/download/v2.1.2/libwasmvm.$(uname -m).so" -O "/root/.pellcored/lib/libwasmvm.$(uname -m).so"
```
```shell
echo 'export LD_LIBRARY_PATH=/root/.pellcored/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```
```shell
echo "/root/.pellcored/lib/" | sudo tee /etc/ld.so.conf.d/pellcored-libs.conf
sudo ldconfig
```


check
```shell
sudo ldconfig -p | grep libwasmvm
```

!!!Create a service

```shell
sudo tee /etc/systemd/system/pellcored.service > /dev/null <<EOF
[Unit]
Description=pellcore node service
After=network-online.target

[Service]
User=root
ExecStart=/root/go/bin/cosmovisor run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=/root/.pellcored"
Environment="DAEMON_NAME=pellcored"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/root/.pellcored/cosmovisor/current/bin"
Environment="LD_LIBRARY_PATH=/root/.pellcored/lib:$LD_LIBRARY_PATH"

[Install]
WantedBy=multi-user.target
EOF

```


Let's activate it
```shell
sudo systemctl daemon-reload
sudo systemctl enable pellcored
```

 Initialize the node
 
```shell
 pellcored config set client chain-id ignite_186-1
pellcored config set client keyring-backend test
pellcored config set client node tcp://localhost:${PELL_PORT}657
pellcored init "Moniker-Name" --chain-id ignite_186-1
```



 Genesis addrbook and others
```shell
 rm -rf /root/.pellcored/config/app.toml
wget https://raw.githubusercontent.com/0xPellNetwork/network-config/refs/heads/main/testnet/app.toml -O /root/.pellcored/config/app.toml
```
```shell
rm -rf /root/.pellcored/config/config.toml
wget https://raw.githubusercontent.com/0xPellNetwork/network-config/refs/heads/main/testnet/config.toml -O /root/.pellcored/config/config.toml
```
```shell
sed -i 's/^moniker = .*/moniker = "Moniker-Name"/' /root/.pellcored/config/config.toml
```

```shell
rm -rf /root/.pellcored/config/genesis.json
wget https://raw.githubusercontent.com/0xPellNetwork/network-config/refs/heads/main/testnet/genesis.json -O /root/.pellcored/config/genesis.json
```


```shell
curl https://raw.githubusercontent.com/MictoNode/pellnetwork/refs/heads/main/addrbook.json -o ~/.pellcored/config/addrbook.json
```

➡️ Indexer
```shell
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.pellcored/config/config.toml
```
➡️ Gas Settings
```shell
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0apell"|g' $HOME/.pellcored/config/app.toml
```

➡️ Prometheus
```shell
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.pellcored/config/config.toml
```

➡️ Starter Snap (thx josephtran)

```shell
pellcored tendermint unsafe-reset-all --home $HOME/.pellcored
if curl -s --head curl http://37.120.189.81/pell_testnet/pell_snap.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl http://37.120.189.81/pell_testnet/pell_snap.lz4 | lz4 -dc - | tar -xf - -C $HOME/.pellcored
    else
  echo no have snap
fi
```
➡️ Let's get started

```shell
sudo systemctl restart pellcored
journalctl -fu pellcored -o cat
```

➡️ Log Command

```shell
sudo journalctl -fu pellcored -o cat
```


➡️ Create wallet

```shell
pellcored keys add wallet-name --keyring-backend=test
```
➡️ don't forget to backup the wallet words!

➡️ Import wallet
```shell
pellcored keys add wallet-name --recover --keyring-backend=test
```
➡️ Create Validator
Reminder: You can't create a validator without Sync. You must have to catch the latest block.

Save your pubkey
```shell
rm -rf /root/validator.json
sudo tee ./validator.json > /dev/null << EOF
{
	"pubkey": $(pellcored tendermint show-validator),
	"amount": "1000000000000000000apell",
	"moniker": "<your_node_name>",
	"identity": "optional identity signature (ex. UPort or Keybase)",
	"website": "validator's (optional) website",
	"security": "validator's (optional) security contact email",
	"details": "validator's (optional) details",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
EOF
```
```shell
pellcored tx staking create-validator ./validator.json --chain-id=ignite_186-1 --fees=0.000001pell --gas=1000000 --from=wallet-name --keyring-backend=test -y
```
➡️ Delegate to Yourself
```shell
pellcored tx staking delegate $(pellcored keys show wallet-name --bech val -a) 1000000000000000000apell --from wallet-name --chain-id ignite_186-1 --fees=0.000001pell --gas=1000000 --keyring-backend=test -y
```
➡️ Edit Validator
```shell
pellcored tx staking edit-validator \
--chain-id ignite_186-1 \
--commission-rate 0.05 \
--new-moniker "validator-name" \
--identity "" \
--details "" \
--website "" \
--security-contact "" \
--from "wallet-name" \
--node http://localhost:${PELL_PORT}657 \
--fees=0.000001pell \
--gas=1000000 \
--keyring-backend=test
-y
```
➡️ Complete deletion
```shell
cd $HOME
sudo systemctl stop pellcored
sudo systemctl disable pellcored
sudo rm -rf /etc/systemd/system/pellcored.service
sudo systemctl daemon-reload
sudo rm -f /usr/local/bin/pellcored
sudo rm -f $(which pellcored)
sudo rm -rf $HOME/.pellcored
sed -i "/PELL_PORT_/d" $HOME/.bash_profile
```
➡️ Block check
```shell
local_height=$(curl -s localhost:${PELL_PORT}657/status | jq -r .result.sync_info.latest_block_height); network_height=$(curl -s https://pell-testnet-rpc.mictonode.com/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```
