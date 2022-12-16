## Nolus-Test Katılım rehberi
# Sistem Gereksinimleri
* 2+ vCPU
* 4+ GB RAM
* 120+ GB SSD
# Go Kurulumu
```
sudo apt-get install -y build-essential
```
# Nolus Kurulumu
```
git clone https://github.com/Nolus-Protocol/nolus-core
cd nolus-core
git checkout
make install
```
# Nolus kurulum kontrolü
```
nolusd version
```
![image](https://user-images.githubusercontent.com/84830960/208183885-06d76575-c1c5-4725-96fa-712d5dd09912.png)

# monikerismi yerine kendi node isminizi girin. Node umuzu initialize ediyoruz.
```
nolusd init [monikerismi]
```
# Genesis dosyası indiriyoruz ve PEER ekliyoruz.
```
wget https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/genesis.json
mv ./genesis.json ~/.nolus/config/genesis.json
PEERS="$(curl -s "https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/persistent_peers.txt")"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.nolus/config/config.toml
```
# Minimmum gas price ayarlıyoruz. ``nano ~/.nolus/config/app.toml`` komutu ile app.toml dosyasını açalım. minimum-gas-prices değerini aşağıdaki ile değiştirelim.
```
minimum-gas-prices = "0.0025unls”
```
![image](https://user-images.githubusercontent.com/84830960/208187773-c43b502e-7a9c-4a79-92ff-2cc46fc6ab86.png)

# Cosmovisor kuruyoruz
```
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@latest
```
# Cosmovisor ayarlıyoruz. monikerismi kısmına kendi node adınızı yazın
```
export DAEMON_NAME=nolusd
export DAEMON_HOME=$HOME/.nolus
export MONIKER_NAME=monikerismi
```
```
source ~/.profile
```
```
mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
mkdir -p $DAEMON_HOME/cosmovisor/upgrades
```
```
cp /root/go/bin/nolusd $DAEMON_HOME/cosmovisor/genesis/bin
```
# Servis dosyası oluşturuyoruz
```
sudo nano /etc/systemd/system/cosmovisor.service
```
Yukarıdaki komuttan sonra boş bir dosya açılacak içine aşağıdaki kodları yapıştırıp Ctrl + X + Y ve Enter ile çıkıyoruz
```
[Unit]
Description=cosmovisor
After=network-online.target

[Service]
User=<your-user>
ExecStart=/home/<your-user>/go/bin/cosmovisor start
Restart=always
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=nolusd"
Environment="DAEMON_HOME=/home/<your-user>/.nolus"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"

[Install]
WantedBy=multi-user.target`
```
# Node umuzu başlatıyoruz
```
nolusd start
```





