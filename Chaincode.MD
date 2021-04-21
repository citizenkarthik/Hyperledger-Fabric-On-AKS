# Chaincode Go
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

## Chaincode Operations

### Step 1: Query all car records

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c '{"Args":["queryAllCars"]}'

### Step 2: Query a specific car by CarID

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c '{"Args":["queryCar","CAR4"]}'

### Step 3: Add a new car

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c '{"Args":["createCar", "CAR12", "Honda", "Accord", "black", "Tom"]}'

### Step 4: Query all cars again

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c ‘{“Args”:[“queryAllCars”]}’

### Step 4: Query all cars again

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c ‘{“Args”:[“queryAllCars”]}’


### Step 5: Change owner of CAR4

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c ‘{“Args”:[“changeCarOwner”, “CAR4”, “KC”]}’


### Step 6: Query CAR4 again

- $ docker exec cli peer chaincode invoke -C mychannel -n fabcar -c ‘{“Args”:[“queryCar”,”CAR4"]}’
