# Prerequisites
- NodeJS 10.14.1
- zip

# Generating the docker image and the marketplace artifacts
## Clone the git repo
```
git clone https://github.com/citizenkarthik/Hyperledger-Fabric-On-AKS.git

```

## Navigate to the scripts directory
```
cd Hyperledger-Fabric-On-AKS/scripts

```

## Execute the generateArtifacts.sh script
```
bash generateArtifacts.sh <DockerImageNameWithTag>
```

An example to this command would be `bash generateArtifacts.sh yourrepo/fabrictools:3.1`

## Push the image to the registry
The image that is built in the previous step needs to be uploaded in a docker registry to be used by the ARM template.

The image can be pushed to a docker registry of your choice by using the following command:

```
docker push <DockerImageWithTag>
```

Please note that you may need to do docker login to your registry to successfully push the image.

## Upload the generated artifacts
The artifacts are generated at `Hyperledger-Fabric-On-AKS/out`. The contents in the `out` directory should be uploaded to a location that can be accessed by the template. While uploading, the directory structure should not be changed.

For example, if we are uploading the artifacts to the Azure Blob using Azure CLI, the following commands can be used.

```
az storage blob upload --account-name <storageAccountName> --container-name mktplace --file <rootDir>/Hyperledger-Fabric-On-AKS/out/hlf-marketplace.zip --name hlf-marketplace.zip --account-key $ACCOUNT_KEY

az storage blob upload --account-name <storageAccountName> --container-name mktplace --file <rootDir>/Hyperledger-Fabric-On-AKS/out/artifacts/funcNodeJS.zip --name artifacts/funcNodeJS.zip --account-key $ACCOUNT_KEY

az storage blob upload --account-name <storageAccountName> --container-name mktplace --file <rootDir>/Hyperledger-Fabric-On-AKS/out/nestedtemplates/publicIpTemplate.json --name nestedtemplates/publicIpTemplate.json --account-key $ACCOUNT_KEY

az storage blob upload --account-name <storageAccountName> --container-name mktplace --file <rootDir>/Hyperledger-Fabric-On-AKS/out/mainTemplate.json --name mainTemplate.json --account-key $ACCOUNT_KEY
```

# Deploying the template
The template can be deployed using Azure CLI or Powershell.

Sample command to deploy from Azure CLI is as follows:
```
az deployment group create --name <deployment> --resource-group <resourceGroup> --subscription <subscriptionId> --template-uri https://<baseURI>/mainTemplate.json
```


#After Deployment 

##Navigate
- Once Peer and Orderer are deployed open application folder
  ```
  cd <rootDir>/Hyperledger-Fabric-On-AKS/application
  ```
- Follow instructions from the Readme.MD in application folder
