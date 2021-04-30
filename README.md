## Hyperledger Fabric on AKS using ARM Deployment🥁


- Microsoft provides ARM template for creating HLF network on AKS via `https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service`.
Since this repo only covers HLF 1.4 and there is a need for HL 2.x in the market due it's widely popular features and improvments, we have created this repository with HLF 2.2. support following the same order as the Microsoft Sample.
  
- In this repo we create custom ARM template for resource utilization on Azure and provide commands for executing the whole lifecycle.
This repository carries custom built fabric-go-cli as added feature to enhance developer experience.
The whole process is explained in stages through different Readme files, each describing a stage in the lifecycle.


## Follow 🛠
- [ARM Template Deployment ](fabric-part-1.md)
- [Generating Profiles ](fabric-part-2.md)
- [Fabric CA Operations ](fabric-ca.md)
- [Consortium Creation ](fabric-part-3.md)
- [Setup Fabric Cli Go ](fabric-part-4.md)
- [Channel Operations ](fabric-part-5.md)
- [Chaincode Operations ](fabric-part-6.md)
- [External Chaincode ](fabric-part-7.md)
- Example chaincode `asset-transfer-basic` and sample commands are provided in `chaincode-sample` folder.
