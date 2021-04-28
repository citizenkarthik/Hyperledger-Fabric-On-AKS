# External Chaincode

- Users can manage a chaincode runtime independently of the peer.
- This facilitates deployment of chaincode on Fabric cloud deployments such as Kubernetes.
- Instead of building and launching the chaincode on every peer, chaincode can now run as a service whose lifecycle is managed outside of Fabric.
- This capability leverages the Fabric v2.0 external builder and launcher functionality which enables operators to extend a peer with programs to build, launch, and discover chaincode.



![stagesofcaas.png](images/stagesofcaas.png)



## No Official Image Support

- This is an advanced feature that will likely require custom
  packaging of the peer image.

- For example, the following samples use jq and bash, which
  are not included in the current official fabric-peer image.


## Packaging Chaincode


- With the Fabric v2.0 chaincode lifecycle, chaincode is packaged and installed in a .tar.gz
  format.
- The following myccpackage.tgz archive demonstrates the required structure:

```
$ tar xvfz myccpackage.tgz
metadata.json
code.tar.gz
```

## File code.tar.gz

- External chaincode uses connection.json and metadata.json files.

- Connection.json will have things like address of the chaincode, port etc.
- Metadata will have type and label of chaincode, type has to be external in order to use CaaS.



- Connection.json is filled with information first ,we package this and create tar.


## Chaincode Image

- Create docker image of chaincode.
- Deploy chaincode on cluster.
- Package the chaincode and install to get peer identifier.


- Run chaincode using identifier.
- Certs and key are needed, mount them to container.
- Once its deployed and running, we approve chaincode.


>   Note: You can now invoke and query the chaincode.


## TLS

>   Note: This is done without Client_Auth On


- Convert the certificate to string using `awk` or any other method.

Add this data to connection.json as root.pem value.



```
{
  "address": "your.chaincode.host.com:9999",
  "dial_timeout": "10s",
  "tls_required": "true",
  "client_auth_required": "false",
  "client_key": "-----BEGIN EC PRIVATE KEY----- ... -----END EC PRIVATE KEY-----",
  "client_cert": "-----BEGIN CERTIFICATE----- ... -----END CERTIFICATE-----",
  "root_cert": "-----BEGIN CERTIFICATE---- ... -----END CERTIFICATE-----"
}
```


- Create a seperate namespace.
- After installation we take chaincode id and create secrets.

- Create secret with certificate and now run chaincode.
- Connect peer to chaincode using commands.
- Package, install, approve and commit.

