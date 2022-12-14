# Alarm tools
![](https://user-images.githubusercontent.com/83868103/190860486-4dc9c7ac-8884-4e85-a643-7c9777e29536.png)
## Server  preparation
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
## Docker installation
```
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/installers/docker.sh)
```
## Install tenderduty
```
mkdir tenderduty && cd tenderduty
docker run --rm ghcr.io/blockpane/tenderduty:latest -example-config >config.yml
```
Download config and edit it
```
wget -O $HOME/tenderduty/config.yml "https://raw.githubusercontent.com/lesnikutsa/lesnik_utsa/main/monitoring/TenderDuty(ru)/config.yml"
```
## Monitoring setup (commands are executed on the server with the node)
```
NODE_FOLDER=.haqq
TIKER=haqqd
WALLET=<wallet>
```
## Getting data to edit config.yml
```
echo -e "\033[0;32mprometheus_listen_port$(grep -A 8 "\[instrumentation\]" ~/$NODE_FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"
echo chain-id:" "$($TIKER status 2>&1 | jq ."NodeInfo" | jq ."network")
echo valoper_address:$($TIKER keys show $WALLET --bech val -a)
echo -e "\033[0;32mhttp://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" ~/$NODE_FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"
```
## On the Server with TenderDuty, you need to enter your data
```
nano $HOME/tenderduty/config.yml
```
## After setting up the config, run
```
docker run -d --name tenderduty -p "8888:8888" -p "28686:28686" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
```
## Get dashboard link
```
echo -e "\033[0;32mhttp://$(wget -qO- eth0.me):8888/\033[0m"
```
## Check logs
```
docker logs -f --tail 20 tenderduty
```
# Setting up Discord Alerts
## Create a channel
### Click on the button
![](https://user-images.githubusercontent.com/83868103/190870811-5a6f4ebe-e20e-47d4-8803-40811adfedde.png)
### Choose
![](https://user-images.githubusercontent.com/83868103/190870865-4ea927f7-3a79-4fda-b199-2730f1191f19.png)
### Choose
![](https://user-images.githubusercontent.com/83868103/190871041-638598e7-42da-40f4-9839-8a18970c32b7.png)
### Change name
![](https://user-images.githubusercontent.com/83868103/190871685-45053949-0cbf-437b-994c-a9887fbbe0c4.png)
### Done
![](https://user-images.githubusercontent.com/83868103/190871249-baf6bf83-c2ed-466c-958b-42313777ab3d.png)
## Creating a Webhook
![](https://img3.teletype.in/files/24/1f/241f4b9f-1f99-429e-87d4-7ca279017c1f.png)
![](https://img2.teletype.in/files/5f/2a/5f2aeb02-68b5-4c72-ad78-d9b0f6622a63.png)
![](https://img3.teletype.in/files/2c/ac/2cac225d-19e6-420e-83c7-2fb425eeba3d.png)
## TenderDuty configuration update
```
nano $HOME/tenderduty/config.yml
```
![](https://img2.teletype.in/files/d9/44/d94411df-cd43-44c9-b7ab-7bf4f6ed40e5.png)
## Restarting with the command
```
docker restart tenderduty
```
Done!
![](https://user-images.githubusercontent.com/85553781/191195229-c5a9b30b-5a76-498a-8bdf-ced37a984c24.png)
