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

