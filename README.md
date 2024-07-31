# Warden-How-to-install-node

![Warden-Protocol](https://github.com/user-attachments/assets/1b690bb4-ccb8-4a25-b3e6-71f06a095165)

## Hardware Requirements
```bash
- CPU: 8 cores
- RAM: 32GB
- DISK: 300GB
```

## Install use the source code
```bash
git clone --depth 1 --branch v0.3.0 https://github.com/warden-protocol/wardenprotocol/
just build

build/wardend init <custom_moniker>
```

## Configure
### Prepare the genesis file
```bash
cd $HOME/.warden/config
rm genesis.json
wget https://raw.githubusercontent.com/warden-protocol/networks/main/testnets/buenavista/genesis.json
```

### Set the mandatory configuration options:
```bash
sed -i 's/minimum-gas-prices = ""/minimum-gas-prices = "0.0025uward"/' app.toml
sed -i 's/persistent_peers = ""/persistent_peers = "92ba004ac4bcd5afbd46bc494ec906579d1f5c1d@54.171.21.98:26656,ed5781ea586d802b580fdc3515d75026262f4b9d@52.30.124.80:26656"/' config.toml
```

### Set up the state sync
```bash
export SNAP_RPC_SERVERS="    https://rpc.buenavista.wardenprotocol.org:443,https://rpc.buenavista.wardenprotocol.org:443    "
export LATEST_HEIGHT=$(curl -s "https://rpc.buenavista.wardenprotocol.org/block" | jq -r     .result.block.header.height)
export BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000))
export TRUST_HASH=$(curl -s "https://rpc.buenavista.wardenprotocol.org/block?height=$    BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC_SERVERS\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.warden/config/config.toml
```

## Start the node
```bash
wardend start
```
