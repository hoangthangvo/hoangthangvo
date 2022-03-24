- 👋 Hi, I’m @hoangthangvo
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
hoangthangvo/hoangthangvo is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
Website
https://celestia.org/

Concept briefly
https://github.com/celestiaorg/networks/blob/master/README.md

Explorer
http://celestia.observer

Manuals
https://github.com/celestiaorg/networks/blob/master/README.md

https://mzonder.notion.site/Celestia-Application-Validator-node-Install-devnet-2-3a3087806d8b492ca937133c1fbca947

https://mzonder.notion.site/Celestia-FAQ-devnet-2-cba99700fd73480da493df0a027aadfa

NEW VERSION (Bridge (ex-fullnode) 0.2.0 (Feb 2022)
https://mzonder.notion.site/Celestia-Bridge-0-2-0-ex-fullnode-install-devnet-2-ea36c6e5cc0942dcaabc842d65343bd7
NEW VERSION (Light node) 0.2.0 (Feb 2022)
https://mzonder.notion.site/Celestia-Light-0-2-0-install-devnet-2-4a04076f8893476b920e60e382a5b150
###################################################
Install Light Node
Install dependencies
# Update if needed
sudo apt update && sudo apt upgrade -y

# Insall packages
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu -y

# Install GO 1.17.2
cd $HOME
ver="1.17.2"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version

# OUTPUT 
# go version go1.17.2 linux/amd64
###################################################

Build celestia

cd $HOME
rm -rf celestia-node
git clone https://github.com/celestiaorg/celestia-node.git
cd celestia-node
git checkout v0.2.0
make install

celestia version
# Semantic version: v0.2.0
###################################################
# **Light Node Start**

### Init client

# init 
celestia light init

# add bootstrap peers
BootstrapPeers="[\"/dns4/andromeda.celestia-devops.dev/tcp/2121/p2p/12D3KooWKvPXtV1yaQ6e3BRNUHa5Phh8daBwBi3KkGaSSkUPys6D\", \"/dns4/libra.celestia-devops.dev/tcp/2121/p2p/12D3KooWK5aDotDcLsabBmWDazehQLMsDkRyARm1k7f1zGAXqbt4\", \"/dns4/norma.celestia-devops.dev/tcp/2121/p2p/12D3KooWHYczJDVNfYVkLcNHPTDKCeiVvRhg8Q9JU3bE3m9eEVyY\"]"

sed -i -e "s|BootstrapPeers *=.*|BootstrapPeers = $BootstrapPeers|" $HOME/.celestia-light/config.toml
#####################################################################
##Create and run service
sudo tee /etc/systemd/system/celestia-light.service > /dev/null <<EOF
[Unit]
  Description=celestia-light
  After=network-online.target
[Service]
  User=$USER
  ExecStart=$(which celestia) light start
  Restart=on-failure
  RestartSec=10
  LimitNOFILE=4096
[Install]
  WantedBy=multi-user.target
EOF

sudo systemctl enable celestia-light
sudo systemctl daemon-reload
sudo systemctl restart celestia-light && journalctl -u celestia-light -f -o cat 

# Press CTRL+C to interrupt logs output

########################################################################
