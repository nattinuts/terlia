<p align="center">
  <img width="600" src="https://miro.medium.com/v2/resize:fit:700/0*C45YwVu4G3lqCsbp">
</p>
<h2><p align="center">Nolus Protocol</p></h2>


## Node Installation

#### Update system and install build tools
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential pkg-config libssl-dev git wget jq make gcc tmux chrony -y
```
#### Install Go
```bash
wget https://go.dev/dl/go1.18.4.linux-amd64.tar.gz; \
rm -rv /usr/local/go; \
tar -C /usr/local -xzf go1.18.4.linux-amd64.tar.gz && \
rm -v go1.18.4.linux-amd64.tar.gz && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```
#### Download and build binaries
```bash
cd
git clone https://github.com/Nolus-Protocol/nolus-core.git
cd nolus-core
git checkout v0.1.43
make install
```
#### Check version
```bash
nolusd version
```
#### Set node configuration
```bash
nolusd config chain-id nolus-rila
nolusd config keyring-backend test
nolusd config node tcp://localhost:26657
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025unls\"/" $HOME/.nolus/config/app.toml
```
#### Download genesis and addrbook
```bash
curl -s https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/genesis.json > $HOME/.nolus/config/genesis.json
wget -O $HOME/.nolus/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Nolus/addrbook.json"
```
#### Add seeds & peers 
```bash
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@okp4-testnet.rpc.kjnodes.com:36659\"|" $HOME/.okp4d/config/config.toml
```
#### Create service
```bash
sudo tee /etc/systemd/system/nolusd.service > /dev/null << EOF
[Unit]
Description=Nolus Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which nolusd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```
## Snapshot
#### Download snapshot
```bash
wget http://136.243.147.235:8100/nolus_snapshot.tar.gz
tar -xf nolus_snapshot.tar.gz -C $HOME/.nolus
rm -rf nolus_snapshot.tar.gz
```
#### Start the node
```bash
sudo systemctl daemon-reload
sudo systemctl enable nolusd
sudo systemctl start nolusd
```
#### Check logs
```bash
sudo journalctl -u nolusd -f --no-hostname -o cat|grep indexed
```
## Dashboard
You can check network health in my [Nolus Grafana Dashboard](http://136.243.147.235:3000/d/ZP7fnmaVz/terlia-nolus-dasboard?orgId=1&var-DS=Prometheus&var-chain_id=nolus-rila&var-instance=localhost:37660)
