worldBank
blockchain based banking system

We took one scenario to simulate cross border transaction, where L&T located in Atlanta, US wants to request for a stock of steel products to Tata Steel located in Kolkata, India. As this deal involves so many asset transfer (money and product) and their provenance between the countries, they need to rely on banks to handle all the financial and legal transactions. In this solution, we tried to create a blockchain based system to handle such tran sactions and records for the same in a common distributed ledger.

steps to start network
navigate to network directory
 cd network

up the network
docker-compose -f docker-compose-cli.yaml up

steps to install & invoke chaincode
define CHANNEL_NAME
export CHANNEL_NAME=<CHANNEL_NAME>

create channel

peer channel create -o orderer.worldbank.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/worldbank.com/orderers/orderer.worldbank.com/msp/tlscacerts/tlsca.worldbank.com-cert.pem
peer channel create -o orderer.worldbank.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx
join channel
peer channel join -b <CHANNEL_NAME>.block
Update the channel definition to define the anchor peer for Organizations in network
peer channel update -o orderer.worldbank.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/worldbank.com/orderers/orderer.worldbank.com/msp/tlscacerts/tlsca.worldbank.com-cert.pem
peer channel update -o orderer.worldbank.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org1MSPanchors.tx
package the chaincode before installing (for node js chaincode)
peer lifecycle chaincode package mycc.tar.gz --path /opt/gopath/src/github.com/hyperledger/fabric-samples/chaincode/abstore/node/ --lang node --label mycc_1
peer chaincode install -n cbtcc300 -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/cbtContract/
peer chaincode install -n bacc300 -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/bankAccountContract
export channel name
export CHANNEL_NAME=<CHANNEL_NAME>
instantiate chaincode
peer chaincode instantiate -o orderer.worldbank.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/worldbank.com/orderers/orderer.worldbank.com/msp/tlscacerts/tlsca.worldbank.com-cert.pem -C $CHANNEL_NAME -n cbtcc -v 1.0 -c '{"Args": }' -P "OR ('xbankMSP.peer','hdfcMSP.peer')"
peer chaincode instantiate -o orderer.worldbank.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/worldbank.com/orderers/orderer.worldbank.com/msp/tlscacerts/tlsca.worldbank.com-cert.pem -C $CHANNEL_NAME -n cbtcc -v 1.0 -c '{"Args": ["org.worldbank.cbt:instantiate"]}'
peer chaincode instantiate -o orderer.worldbank.com:7050 -C $CHANNEL_NAME -n cbtcc300 -v 1.0 -c '{"Args": ["org.worldbank.cbt:instantiate"]}'
peer chaincode instantiate -o orderer.worldbank.com:7050 -C $CHANNEL_NAME -n bacc300 -v 1.0 -c '{"Args": ["org.worldbank.bankaccount:instantiate"]}'
query chaincode
peer chaincode query -C $CHANNEL_NAME -n <chaincode_name> -c '{"Args":["query","a"]}'
invoke chaincode
peer chaincode invoke -o orderer.worldbank.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/worldbank.com/orderers/orderer.worldbank.com/msp/tlscacerts/tlsca.worldbank.com-cert.pem -C $CHANNEL_NAME -n cbtcc14 -c '{"Args": }'
peer chaincode invoke -o orderer.worldbank.com:7050 -C $CHANNEL_NAME -n cbtcc300 -c '{"Args": }'
peer chaincode invoke -o orderer.worldbank.com:7050  -C $CHANNEL_NAME -n bacc200 -c '{"Args": }'
start REST API service
go to application directory
cd application
start express JS service
node app.js
It will provide REST service to client App through port 3000

use web application
Open the web pages located into client_app2 directory

Through this web pages you can interact with deployed blockchain system!
