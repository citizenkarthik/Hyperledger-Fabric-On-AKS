##Setup Fabric Cli

Open link below to set up fabric cli by following instructions provided on its readme.

    https://github.com/hyperledger/fabric-cli

set up fabric cli environment variable


    export FABRIC_CLI_PATH =<path_to_fabric_binary>


    export PEER_CONNECTION_PROFILE_PATH=<path_to_peer_connection_profile.json>
    export ORDERER_CONNECTION_PROFILE_PATH=<path_to_orderer_connection_profile.json>

    cd genFabricCliGO/main/
    go build -o ../../yamlconversion


    mv yamlconversion ../../
    ./yamlconversion

### Channel operations

Use the following command to pull binaries of HLF 2.2

    curl -sSL https://goo.gl/6wtTN5 | bash -s 2.2.0

    bin/configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channelone.tx -channelID channelone


### Chaincode operations

Content to be added

#### Package chaincode

Content to be added

#### Install chaincode

Content to be added

#### Approve chaincode

Content to be added

#### Commit chaincode

Content to be added


#### Invoke chaincode

Content to be added

#### Query chaincode

Content to be added

