# PELL
![image](https://github.com/user-attachments/assets/62eaa453-7b3c-442e-9c4e-5b2ef993d10c)
https://discord.com/invite/0glabs
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
