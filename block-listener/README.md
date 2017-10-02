# What is block-listener
block-listener.go connects to a peer in order to receive block, chaincode
events, events from a specified channel and invalid events (if there are such
events being sent), in any combination the user requests.

# Usage
```sh
1. go build

2. Usage of ./block-listener:
  -events-address string
    	address of events server (default "0.0.0.0:7053")
  -events-block
    	listen to block events
  -events-chaincode-event string
    	listen to events from a given chaincode with a given event name - accepts comma separated pairs: <chaincodeID1,event-name1,...>
  -events-from-channel string
    	listen to events from a given channel - accepts comma separated values: <channelID1,channelID2,...> - default is all
  -events-invalid
    	listen to invalid events
  -events-mspdir string
    	set up the msp direction
  -events-mspid string
    	set up the mspid
  -events-txid string
    	listen to events from a given transaction - accepts comma separated values: <transactionID1,transactionID2,...>
```
Please note that the default MSP under fabric/sampleconfig will be used if no
MSP parameters are provided.

# Example with the hyperledger/fabric-samples/chaincode-docker-devmode example

Bring the network up with the "up" command:

```sh
bash ./up
```

Build the example_02 chaincode:

```sh
docker exec chaincode bash -c "cd chaincode_example02;go build"
```

Run the chaincode in the chaincode container with the following command:
```sh
docker exec chaincode bash -c "cd chaincode_example02;CORE_PEER_ADDRESS=peer:7051 CORE_CHAINCODE_ID_NAME=mycc:0 ./chaincode_example02"
```

Install and instantiate the chaincode using the following commands:
```sh
docker exec cli bash -c "peer chaincode install -p chaincodedev/chaincode/chaincode_example02 -n mycc -v 0"

docker exec cli bash -c "peer chaincode instantiate -n mycc -v 0 -c '{\"Args\":[\"init\",\"a\",\"100\",\"b\",\"200    \"]}' -C myc"
```

Start the block listener:
```sh
./block-listener -events-address=0.0.0.0:7053 -events-mspdir=$GOPATH/src/github.com/hyperledger/fabric-samples/chaincode-docker-devmode/msp -events-mspid=DEFAULT -events-block=TRUE
```

The event client should output "Event Address: 0.0.0.0:7053"
and wait for events.

Invoke a transaction:
```sh
docker exec cli bash -c "peer chaincode invoke -n mycc -c '{\"Args\":[\"invoke\",\"a\",\"b\",\"10\"]}' -C myc"
```
Now you should see the block content displayed in the terminal running the block
listener.


<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.
