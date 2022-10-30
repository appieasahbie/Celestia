# Install Validator on Celestia


![dss](https://user-images.githubusercontent.com/108979536/198891828-c8ffa1ef-9198-4e19-8055-f2c5d4b16317.jpg)


# Technology

### Celestia is pioneering a new paradigm in blockchain design. A minimal, modular consensus layer for rollups.


# Hardware requirements

 ### The following hardware minimum requirements are recommended for running the validator node:

   + Memory: 8 GB RAM
   
   + CPU: Quad-Core
   
   + Disk: 250 GB SSD Storage
   
   + Bandwidth: 1 Gbps for Download/100 Mbps for Upload
   
 # 1-ɪɴᴛᴀʟʟᴀᴛɪᴏɴ ᴏɴᴇ ᴛɪᴍᴇ (ꜱᴄʀɪᴘᴛ ᴀᴜᴛᴏᴍᴀᴛɪᴄ ɪɴꜱᴛᴀʟʟᴀᴛɪᴏɴ)

       wget -O Celestia.sh https://raw.githubusercontent.com/appieasahbie/Celestia/main/Celestia.sh && chmod +x Celestia.sh && ./Celestia.sh 
       

### Post installation

      source $HOME/.bash_profile

+ (Check the status of your node)

      celestia-appd status 2>&1 | jq .SyncInfo

### open ports and active the firewall

     sudo ufw default allow outgoing
     sudo ufw default deny incoming
     sudo ufw allow ssh/tcp
     sudo ufw limit ssh/tcp
     sudo ufw allow ${CELESTIA_PORT}656,${CELESTIA_PORT}660/tcp
     sudo ufw enable
     
### Create wallet

 + (Please save all keys on your notepad)  
 
       celestia-appd keys add $WALLET
     
 + To recover your old wallet use this command

       celestia-appd keys add $WALLET --recover
     
 + show keys

       celestia-appd keys list
     
     
 ### Add wallet and valoper address and load variables into the system
    
     CELESTIA_WALLET_ADDRESS=$(celestia-appd keys show $WALLET -a)
     CELESTIA_VALOPER_ADDRESS=$(celestia-appd keys show $WALLET --bech val -a)
     echo 'export CELESTIA_WALLET_ADDRESS='${CELESTIA_WALLET_ADDRESS} >> $HOME/.bash_profile
     echo 'export CELESTIA_VALOPER_ADDRESS='${CELESTIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
     source $HOME/.bash_profile
     
 ### Fund your wallet
  + use faucet chain on celectia discord channel
  
  
 ### After you recive 1 tia and your node is sync (false) we gonna create a validator
 
     celestia-appd tx staking create-validator \
     --amount 1000000utia \
     --from $WALLET \
     --commission-max-change-rate "0.01" \
     --commission-max-rate "0.2" \
     --commission-rate "0.07" \
     --min-self-delegation "1" \
     --pubkey  $(celestia-appd tendermint show-validator) \
     --moniker $NODENAME \
     --chain-id $CELESTIA_CHAIN_ID
     
     
 # Monitoring

   ### Get list of validators
   
      celestia-appd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) 
 
 
# Service management

  ### Check logs

      journalctl -fu celestia-appd -o cat
      
  ### Start service

      sudo systemctl start celestia-appd
      
  ### Stop service

      sudo systemctl stop celestia-appd
      
  ### Restart service

      sudo systemctl restart celestia-appd
      
# Node info
     
  ### Synchronization info

      celestia-appd status 2>&1 | jq .SyncInfo
      
  ### Validator info

      celestia-appd status 2>&1 | jq .ValidatorInfo
      
  ### Node info

      celestia-appd status 2>&1 | jq .NodeInfo
      
  ### Show node id

      celestia-appd tendermint show-node-id
      
 
  ### Delegate stake

      celestia-appd tx staking delegate $CELESTIA_VALOPER_ADDRESS 10000000utia --from=$WALLET --chain-id=$CELESTIA_CHAIN_ID --gas=auto
      
  ### Redelegate stake from validator to another validator

      celestia-appd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000utia --from=$WALLET --chain-id=$CELESTIA_CHAIN_ID --gas=auto
      
# Edit your validator\

      celestia-appd tx staking edit-validator \
      --moniker=$your node name \
      --identity=<your_keybase_id> \
      --website="<your_website>" \
      --details="<your_validator_description>" \
      --chain-id=$CELESTIA_CHAIN_ID \
      --from=$wallet
      
 
 # Delete your node
 
      sudo systemctl stop celestia-appd
      sudo systemctl disable celestia-appd
      sudo rm /etc/systemd/system/celestia* -rf
      sudo rm $(which celestia-appd) -rf
      sudo rm $HOME/.celestia-app* -rf
      sudo rm $HOME/celestia -rf
      sed -i '/CELESTIA_/d' ~/.bash_profile
 
  Done
  
  [buy me cup of cafee](https://www.paypal.com/paypalme/AbdelAkridi?country.x=NL&locale.x=en_US)
