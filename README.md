
# Pryzm

![pryzm](https://github.com/Core-Node-Team/Services/assets/108215275/c80e8403-2823-4b8e-998b-ff8117bb82f6)

### Getiri tokenizasyonu ve ticareti için oluşturulmuş Katman 1.

#### Pryzm, getirilerin toplanması, ticareti ve kullanımına adanmış ilk katman-1 blok zinciridir. Hisse Kanıtı (PoS) ve DeFi varlıklarının nakit akışlarını takas etmek için kullanıcıların bir araya geldiği çapraz zincir merkezidir. Zincir düzeyinde yerleşik çekirdek özellikler, getirilerin ayrılmasını sağlar ve getiri getiren PoS ve DeFi varlıkları tarafından üretilen nakit akışlarını kullanan yenilikçi dApp ekosisteminin oluşmasına öncülük eder. Pryzmm, Cosmos SDK ve Tendermint konsensüsü kullanılarak inşa edilmiştir.

* [Twitter](https://twitter.com/Pryzm\_Zone)
* [Discord](https://discord.gg/cf7TpMGJ)
* [Website](https://pryzm.zone/)
* [Docs](https://docs.pryzm.zone/)
* [Medium](https://pryzm.medium.com/)
* [Telegram](https://t.me/pryzm\_zone)



# Manuel Kurulum

## Sunucuyu Hazırlayın
```bash
sudo apt-get update && sudo apt-get upgrade -y

sudo apt install curl tar wget tmux htop net-tools clang pkg-config libssl-dev jq build-essential git screen make ncdu -y

cd $HOME
wget https://go.dev/dl/go1.20.4.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.20.4.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && source $HOME/.bash_profile
rm -rf go1.20.4.linux-amd64.tar.gz
```
## Binary Kurulumu
```bash
wget https://storage.googleapis.com/pryzm-resources/pryzmd-0.9.0-linux-amd64.tar.gz
tar -xzvf pryzmd-0.9.0-linux-amd64.tar.gz
mv pryzmd $HOME/go/bin
```
## İnitalize
```bash
pryzmd config chain-id indigo-1
pryzmd config keyring-backend test
pryzmd config node tcp://localhost:31657
pryzmd init $MONIKER --chain-id indigo-1
```

## Yapılandırma
```bash
DirectName=".pryzm" #database directory
CustomPort="316"

curl -Ls https://raw.githubusercontent.com/Core-Node-Team/scripts/main/pryzm/addrbook.json > $HOME/$DirectName/config/addrbook.json
curl -Ls https://raw.githubusercontent.com/Core-Node-Team/scripts/main/pryzm/genesis.json > $HOME/$DirectName/config/genesis.json

peers="713307ce72306d9e86b436fc69a03a0ab96b678f@pryzm-testnet-peer.itrocket.net:41656,4af5be7666e9ee756a9a588c181f9631064b9cf8@37.27.55.69:26656,5d9bcb33eef94e045fe51105c89f5d77709b3183@144.76.101.167:5000,9515a13bbdeb233eb59efd6e8db892ac46e5bac5@142.132.153.6:56656,f9ade689abb3c59d3e3d8edf26c65bde3db58676@116.202.85.52:35656,7397a1bcbf413b76bd710fcf363f8259acdc4d29@144.91.84.168:23256,db0e0cff276b3292804474eb8beb83538acf77f5@195.14.6.192:26656,794b538577a59f789ce942fd393730da3e8c0ffe@34.65.224.175:26656,565e54f6b12672fba48861fc72654c39dc0f2d97@195.3.223.138:36656,cdcd86ca01858275d0e78ee66b82109ee06df454@65.108.72.253:40656,2c7bb6ad931b0b2b24a0d8e6b7b5e0636b8bafb0@38.242.230.118:48656"
seeds="fbfd48af73cd1f6de7f9102a0086ac63f46fb911@pryzm-testnet-seed.itrocket.net:41656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/$DirectName/config/config.toml

sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.015upryzm\"|" $HOME/.pryzm/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.pryzm/config/config.toml

# puruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
  $HOME/$DirectName/config/app.toml
# indexer
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/$DirectName/config/config.toml
# custom port
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CustomPort}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CustomPort}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CustomPort}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CustomPort}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CustomPort}66\"%" $HOME/$DirectName/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CustomPort}17\"%; s%^address = \":8080\"%address = \":${CustomPort}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CustomPort}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CustomPort}91\"%; s%:8545%:${CustomPort}45%; s%:8546%:${CustomPort}46%; s%:6065%:${CustomPort}65%" $HOME/$DirectName/config/app.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://localhost:${CustomPort}17\"%; s%^address = \":8080\"%address = \":${CustomPort}80\"%; s%^address = \"localhost:9090\"%address = \"localhost:${CustomPort}90\"%; s%^address = \"localhost:9091\"%address = \"localhost:${CustomPort}91\"%; s%:8545%:${CustomPort}45%; s%:8546%:${CustomPort}46%; s%:6065%:${CustomPort}65%" $HOME/$DirectName/config/app.toml
```
## Snapshot
```bash
curl -L http://37.120.189.81/pryzm_testnet/pryzm_snap.tar.lz4 | tar -I lz4 -xf - -C $HOME/.pryzm
```
## Service
```bash
sudo tee /etc/systemd/system/pryzmd.service > /dev/null <<EOF
[Unit]
Description=Pryzm Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which pryzmd) start --home $HOME/.pryzm
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable pryzmd
```
## Nodu Başlatın ve Logları Görüntüleyin
```bash
sudo systemctl start pryzmd && sudo journalctl -u pryzmd -fo cat
```

 
#
<h1 align=center> Cüzdan Yönetimi </h1>
 
## Cüzdan Oluştur
```
pryzmd keys add wallet
```
## Cüzdan Recover Et
```
pryzmd keys add wallet --recover
```
## Cüzdanları Listele
```
pryzmd keys list
```
## Cüzdan Sil
```
pryzmd keys delete wallet
```
## Cüzdan Bakiyesini Sorgula
```
pryzmd q bank balances $(pryzmd keys show wallet -a)
```
#
<h1 align=center> Validatör Yönetimi </h1>
 
## Validatör Oluştur
```
pryzmd tx staking create-validator \
--amount 1000000upryzm \
--pubkey $(pryzmd tendermint show-validator) \
--moniker "MONİKER_İSMİNİZ" \
--identity "KEYBASE.İO_İD" \
--details "DETAYLAR" \
--website "WEBSİTE_LİNKİNİZ" \
--chain-id indigo-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.015upryzm \
-y
```
## Validatörü Düzenle
```
pryzmd tx staking edit-validator \
--new-moniker "MONİKER_İSMİNİZ" \
--identity "KEYBASE.İO_İD" \
--details "DETAYLAR" \
--website "WEBSİTE_LİNKİNİZ" \
--chain-id indigo-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.015upryzm \
-y
```
## Validatör Detayları
```
pryzmd q staking validator $(pryzmd keys show wallet --bech val -a)
```
## Validatör Unjail
```
pryzmd tx slashing unjail --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Jail Olma Sebebi
```
pryzmd query slashing signing-info $(pryzmd tendermint show-validator)
```
## Tüm Aktif Validatörleri Listele
```
pryzmd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status==BOND_STATUS_BONDED)' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) +  t  + .description.moniker' | sort -gr | nl
```
## Tüm İnaktif Validatörleri Listele
```
pryzmd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status==BOND_STATUS_UNBONDED)' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) +  t  + .description.moniker' | sort -gr | nl
```
<h1 align=center> Token </h1>
 
## Token Gönder
```
pryzmd tx bank send wallet <HEDEF_CÜZDAN_ADRESİ> 1000000upryzm --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Delegate
```
pryzmd tx staking delegate <VALOPER_ADRESİ> 1000000upryzm --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Kendi Validatörüne Delegate
```
pryzmd tx staking delegate $(pryzmd keys show wallet --bech val -a) 1000000upryzm --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto ---gas-prices 0.015upryzm -y
```
## Redelegate
```
pryzmd tx staking redelegate <İLK_VALOPER_ADRESİ> <HEDEF_VALOPER_ADRESİ> 1000000upryzm --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Kendi Validatöründen Başka Validatöre Redelegate
```
pryzmd tx staking redelegate $(pryzmd keys show wallet --bech val -a) <VALOPER_ADRESİ> 1000000upryzm --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Unbond
```
pryzmd tx staking unbond $(pryzmd keys show wallet --bech val -a) 1000000upryzm --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Tüm Validatörlerden Komisyon ve Ödülleri Çekme
```
pryzmd tx distribution withdraw-all-rewards --commission --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Kendi Validatörünüze Ait Komisyon ve Ödülleri Çekme
```
pryzmd tx distribution withdraw-rewards $(pryzmd keys show wallet --bech val -a) --commission --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
<h1 align=center> Yönetim </h1>
 
## Tüm Oylamaları Görüntüle
```
pryzmd query gov proposals
```
## Oylama Detaylarını Görüntüle
```
pryzmd query gov proposal <ID>
```
## Evet Oyu Ver
```
pryzmd tx gov vote <ID> yes --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Hayır Oyu Ver
```
pryzmd tx gov vote <ID> no --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Çekimser Oyu Ver
```
pryzmd tx gov vote <ID> abstain --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
## Hayır Oyu ve Veto Et
```
pryzmd tx gov vote <ID> no_with_veto --from wallet --chain-id indigo-1 --gas-adjustment 1.5 --gas auto --gas-prices 0.015upryzm -y
```
<h1 align=center> Yapılandırma Ayarları </h1>
 
 ## Pruning
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
  $HOME/.pryzm/config/app.toml
```
## İndexer Aç
```
sed -i -e 's|^indexer *=.*|indexer = kv|' $HOME/.pryzm/config/config.toml
```
## İndexer Kapat
```
sed -i -e 's|^indexer *=.*|indexer = null|' $HOME/.pryzm/config/config.toml
```
## Port Değiştir
> ### Port=316
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:31658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:31657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:31660\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:31656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":31666\"%" $HOME/.pryzm/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://localhost:31617\"%; s%^address = \":8080\"%address = \":31680\"%; s%^address = \"localhost:9090\"%address = \"localhost:31690\"%; s%^address = \"localhost:9091\"%address = \"localhost:31691\"%; s%:8545%:31645%; s%:8546%:31646%; s%:6065%:31665%" $HOME/.pryzm/config/app.toml
```
## Min Gas Price Ayarla
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.015upryzm\"|" $HOME/.pryzm/config/app.toml

```
## Prometheus Aktif Et
```
sed -i -e s/prometheus = false/prometheus = true/ $HOME/.pryzm/config/config.toml
```
## Zincir Verilerini Sıfırla
```
pryzmd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.pryzm --keep-addr-book
```
<h1 align=center> Durum Sorgulama ve Kontrol </h1>
 
## Senkronizasyon Durumu
```
pryzmd status 2>&1 | jq .SyncInfo
```
## Validatör Durumu
```
pryzmd status 2>&1 | jq .ValidatorInfo
```
## Node Durumu
```
pryzmd status 2>&1 | jq .NodeInfo
```
## Validatör Key Kontrol
```
[[ $(pryzmd q staking validator $(pryzmd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(pryzmd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```
## TX Sorgulama
```
pryzmd query tx <TX_ID>
```
## Peer Adresini Öğren
```
echo $(pryzmd tendermint show-node-id)@$(curl -s ifconfig.me):$(cat $HOME/.pryzm/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```
## Bağlı Peerleri Öğren
```
curl -sS http://localhost:31657/net_info | jq -r ".result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"" | awk -F ":" "{print $1":"$NF}"
```
<h1 align=center> Service Yönetimi </h1>
 
Servisi Etkinleştir
```
sudo systemctl enable pryzmd
```
Servisi Devre Dışı Bırak
```
sudo systemctl disable pryzmd
```
Servisi Başlat
```
sudo systemctl start pryzmd
```
Servisi Durdur
```
sudo systemctl stop pryzmd
```
Servisi Yeniden Başlat
```
sudo systemctl restart pryzmd
```
Servis Durumunu Kontrol Et
```
sudo systemctl status pryzmd
```
Servis Loglarını Kontrol Et
```
sudo journalctl -u pryzmd -f --no-hostname -o cat
```
<h1 align=center> Node Silmek </h1>
 
```
sudo systemctl stop pryzmd && sudo systemctl disable pryzmd && sudo rm /etc/systemd/system/pryzmd.service && sudo systemctl daemon-reload && rm -rf $HOME/.pryzm && rm -rf $HOME/pryzm && sudo rm -rf $(which pryzmd)
```




