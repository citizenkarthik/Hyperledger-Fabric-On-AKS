## Chaincode Lifecycle And Operations

Once the setup is us and running, you bash into the cli container and execute the commands for chaincode related operations.

Below are the chaincode related operations to be performed:

	* Package Chaincode
	* Install Chaincode
	* Approve Chaincode 
	* Commit Chaincode 
	* Invoke Chaincode
	* Query Chaincode

The chaincode we are going to use is developed using GO, so all the above processes will be executed from Go chaincode perspective.

### Package Chaincode

First of all we need to resolve the dependencies of the chaincode before packaging it. As for the new lifecycle flow the chaincode dependecies are managed using go mod, we need to navigate to the projects base folder and run the below command to install all the chaincode dependencies.

	GO111MODULE=on go mod vendor

Steps for installing the chaincode will vary for other language chaincode like javascript,typescript.

Now you can package the chaincode by running the below peer command.

	peer lifecycle chaincode package chaincode.tar.gz --path ../path/to/chaincode --lang golang --label chaincode_1.0


### Install Chaincode

Once the packaging of the smart contract is done, we need to install the smart contract on the peers of the organization. For installing the chaincode onto a specific peer we will point to that peer from cli by changing the peer environment variables as per that peer using the below export commands.

	export CORE_PEER_TLS_ENABLED=true
	export CORE_PEER_LOCALMSPID="Org1MSP"
	export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
	export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
	export CORE_PEER_ADDRESS=localhost:7051

Once you have pointed to that specific peer using the export commands, execute the below command to install the chaincode onto that peer.

	peer lifecycle chaincode install basic.tar.gz

The chaincode image is build by the peer when the chaincode is installed.If the build fails the command will return the error for that else if the chaincode gets successfully installed onto the peer it will return the package identifier for the installed chaincode which will get printed in the logs like below.

	2020-07-16 10:09:57.534 CDT [cli.lifecycle.chaincode] submitInstallProposal -> INFO 001 Installed remotely: response:<status:200 	payload:"\nJbasic_1.0:e2db7f693d4aa6156e652741d5606e9c5f0de9ebb88c5721cb8248c3aead8123\022\tbasic_1.0" >
	2020-07-16 10:09:57.534 CDT [cli.lifecycle.chaincode] submitInstallProposal -> INFO 002 Chaincode code package identifier: basic_1.0:e2db7f693d4aa6156e652741d5606e9c5f0de9ebb88c5721cb8248c3aead8123

This package id will be used in the approve and commit process of the chaincode.

### Approve Chaincode

After installation of the chaincode, it needs to be approved by the organizations based on the defined lifecycle endorsement policy. To approve the chaincode for the organization execute the below command.

	export CC_PACKAGE_ID=basic_1.0:69de748301770f6ef64b42aa6bb6cb291df20aa39542c3ef94008615704007f3
	peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --channelID mychannel --name basic --version 1.0 --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

Replace the CC_PACKAGE_ID value in the export command with the package id you have received while installing the chaincode.

### Commit Chaincode

Once the succifient number of organizations have approved the chaincode based on the lifecycle endorsement policy. One of the organization can commit the chaincode. You can execute the below command before committing the chaincode to verify if the chaincode have the sufficient approvals to be committed.

	peer lifecycle chaincode checkcommitreadiness --channelID mychannel --name basic --version 1.0 --sequence 1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --output json

	 {
            "Approvals": {
                    "Org1MSP": true
            }
    }

The json above is the response you will receive. By default the lifecycle endorsement policy is Majority, and having a network of single org the approvals in the response here is sufficient to proceed for the commit.

Use below command to commit the chaincode onto the channel.

	peer lifecycle chaincode commit -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --channelID mychannel --name basic --version 1.0 --sequence 1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

Once the commit succeeds the information is written onto the channel and chaincode is ready to be used for invoke and query calls. If you want to check the committed chaincodes onto the channel you can execute the below command and retrieve the information for all the committed chaincodes.

	peer lifecycle chaincode querycommitted --channelID mychannel --name basic --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

Provided below is the sample response you will receive for this command.

	Committed chaincode definition for chaincode 'basic' on channel 'mychannel':
	Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]

### Invoke Chaincode

Once the chaincode is committed we can execute the invoke command to perform action on a channel using that chaincode. Below is a sample invoke command.

	peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"function":"InitLedger","Args":[]}'

Invoke command will write the transaction onto the channel with the values passed in the arguments as per the chaincode logic.

### Query Chaincode

You can query the chaincode to get the updated values from the chaincodes. Below is a sample command for that.

	peer chaincode query -C mychannel -n basic -c '{"Args":["queryAllCars"]}'

Below is a sample chaincode with the whole lifecycle operation. We have used fabcar example in the repo for this.


### Sample Chaincode (Go)
```
func (s *SmartContract) Init(APIstub shim.ChaincodeStubInterface) sc.Response {
          return shim.Success(nil)
}
 
func (s *SmartContract) Invoke(APIstub shim.ChaincodeStubInterface) sc.Response {
 
          function, args := APIstub.GetFunctionAndParameters()
 
          if function == "queryCar" {
                   return s.queryCar(APIstub, args)
          } else if function == "initLedger" {
                   return s.initLedger(APIstub)
          } else if function == "createCar" {
                   return s.createCar(APIstub, args)
          } else if function == "queryAllCars" {
                   return s.queryAllCars(APIstub)
          } else if function == "changeCarOwner" {
                   return s.changeCarOwner(APIstub, args)
          }
 
          return shim.Error("Invalid Smart Contract function name.")
}
```

- This is the fabcar chaincode implementation. It has two functions Init and Invoke. Init is called when the chaincode is first installed or when an existing chaincode is upgraded. For example, creating an asset when the chaincode is first installed. Invoke is called when we need the chaincode to query or modify the shared leger. Both functions have the reference to the shim API (shim.ChaincodeStubInterface) that is actually used to query or update the shared ledger. The state change to the shared ledger takes effect only after transaction is validated and successfully committed.

### Chaincode Operations

#### Package Chaincode

    GO111MODULE=on go mod vendor
    peer lifecycle chaincode package fabcar.tar.gz --path ../fabcar --lang golang --label fabcar_1.0

#### Install Chaincode

    export CORE_PEER_TLS_ENABLED=true
    export CORE_PEER_LOCALMSPID="Org1MSP"
    export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    export CORE_PEER_ADDRESS=localhost:7051
    
    peer lifecycle chaincode install fabcar.tar.gz
    
    export CC_PACKAGE_ID=fabcar_1.0:69de748301770f6ef64b42aa6bb6cb291df20aa39542c3ef94008615704007f3
    
#### Approve Chaincode

    peer lifecycle chaincode approveformyorg -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --channelID mychannel --name fabcar --version 1.0 --package-id $CC_PACKAGE_ID --sequence 1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
    
#### Commit Chaincode

    peer lifecycle chaincode commit -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --channelID mychannel --name fabcar --version 1.0 --sequence 1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        
        
#### Invoke Chaincode

##### Add a new car
    
    peer chaincode invoke -C mychannel -n fabcar -c '{"Args":["createCar", "CAR12", "Honda", "Accord", "black", "Tom"]}'

##### Change owner of CAR4

    peer chaincode invoke -C mychannel -n fabcar -c ‘{“Args”:[“changeCarOwner”, “CAR4”, “KC”]}’

    
#### Query Chaincode

#####  Query all car records

    peer chaincode invoke -C mychannel -n fabcar -c '{"Args":["queryAllCars"]}'

##### Query a specific car by CarID

    peer chaincode invoke -C mychannel -n fabcar -c '{"Args":["queryCar","CAR4"]}'
