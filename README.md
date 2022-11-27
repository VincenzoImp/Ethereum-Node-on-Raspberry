# Ethereum-Node-on-Raspberry
sudo apt update
sudo apt upgrade
sudo apt install chrony -y
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
sudo apt-get upgrade geth

sudo vim /etc/systemd/system/geth.service

[Unit]
Description=Geth
[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/geth \
       --http \
       --http.api "eth,web3,txpool,net,debug,engine" \
       --datadir /data/ethereum
[Install]
WantedBy=default.target

sudo systemctl enable geth

sudo apt install -y git gcc g++ make cmake pkg-config llvm-dev libclang-dev clang protobuf-compiler
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
git clone https://github.com/sigp/lighthouse.git
cd lighthouse
git checkout stable
make

sudo vim /etc/systemd/system/lighthouse.service

[Service]
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/lighthouse bn \
         --network mainnet \
         --datadir /data/lighthouse \
         --eth1-endpoints http://127.0.0.1:8545 \
         --execution-endpoints http://127.0.0.1:8551 \
         --builder http://127.0.0.1:18550 \
         --http \
         --http-address 127.0.0.1 \
         --jwt-secrets /data/ethereum/geth/jwtsecret
[Install]
WantedBy=multi-user.target

sudo systemctl enable lighthouse

sudo systemctl start geth
sleep 10
sudo systemctl start lighthouse

sudo geth attach --datadir /data/ethereum --exec 'eth.syncing'

curl localhost:5052/lighthouse/syncing
