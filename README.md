# Certificate Revocation With Fabric CA

[![IMAGE ALT TEXT](http://img.youtube.com/vi/cjXJrlP2owc/0.jpg)](http://www.youtube.com/watch?v=cjXJrlP2owc "Video Tutorial")


### Spinning up the network

1. Network UP

```bash
./network.sh up createChannel -ca -c mychannel -s couchdb
```

1. Deploy CC

```bash
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-go -ccl go
```

1. Setup Environment Variable

```bash
export PATH=${PWD}/../bin:$PATH
export FABRIC_CFG_PATH=${PWD}/../config

export CORE_PEER_TLS_ENABLED=true

export ORDERER_CA=${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp

export CORE_PEER_ADDRESS=localhost:7051

export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export CORE_PEER_LOCALMSPID=Org1MSP
```

1. Invoke CC

```bash
peer chaincode invoke -n basic -C mychannel -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com  --tls --cafile "$ORDERER_CA" --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt  -c '{"Args":["CreateAsset", "100","red", "20","aditya","100"]}'
```

1. Query CC

```bash
peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","100"]}'
```

### Block decoding

1. Fetch the block and Decode Block

```bash
peer channel getinfo -c mychannel
peer channel fetch -c mychannel blockNum

# decoding block to see who made the tx
configtxgen -inspectBlock mychannel_newest.block | jq .
```

1. Invoke CC

```bash
CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp peer chaincode invoke -n basic -C mychannel -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com  --tls --cafile "$ORDERER_CA" --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt  -c '{"Args":["CreateAsset", "100","red", "20","aditya","100"]}'
```

1. Query CC

```bash
CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","100"]}'
```

1. Fetch the block and Decode Block

```bash
peer channel getinfo -c mychannel
peer channel fetch -c mychannel blockNum

configtxgen -inspectBlock mychannel_newest.block | jq .
```

1. Decode Certificate

```bash
echo "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNxRENDQWsrZ0F3SUJBZ0lVY3NRSlhPV1V3TDJFbUVjNU0xdk56d3VNNkVFd0NnWUlLb1pJemowRUF3SXcKY0RFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1ROHdEUVlEVlFRSApFd1pFZFhKb1lXMHhHVEFYQmdOVkJBb1RFRzl5WnpFdVpYaGhiWEJzWlM1amIyMHhIREFhQmdOVkJBTVRFMk5oCkxtOXlaekV1WlhoaGJYQnNaUzVqYjIwd0hoY05NakV4TVRFeU1UWXhNakF3V2hjTk1qSXhNVEV5TVRZeE56QXcKV2pCZE1Rc3dDUVlEVlFRR0V3SlZVekVYTUJVR0ExVUVDQk1PVG05eWRHZ2dRMkZ5YjJ4cGJtRXhGREFTQmdOVgpCQW9UQzBoNWNHVnliR1ZrWjJWeU1ROHdEUVlEVlFRTEV3WmpiR2xsYm5ReERqQU1CZ05WQkFNVEJYVnpaWEl4Ck1Ga3dFd1lIS29aSXpqMENBUVlJS29aSXpqMERBUWNEUWdBRUZIazc0V2IzMGRyemsvSm90aUpTMzJubFNwUlAKdU5nckJKa2NxeXgvNTNCVHV5VTliUUhHMlhVVm10SzMvUytWL0lSNnpuTFdxc05JcWk0VXhYRDJhcU9CMlRDQgoxakFPQmdOVkhROEJBZjhFQkFNQ0I0QXdEQVlEVlIwVEFRSC9CQUl3QURBZEJnTlZIUTRFRmdRVWpHSFpLc1h0CjhrZHF2UTF4cGRwYStoZW9uTE13SHdZRFZSMGpCQmd3Rm9BVW9oM0hpV2EvbjhuaTdjTnR4VnhxTUxuWUdERXcKSEFZRFZSMFJCQlV3RTRJUmFIQXRjSEp2WW05dmF5MDBOREF0WnpVd1dBWUlLZ01FQlFZSENBRUVUSHNpWVhSMApjbk1pT25zaWFHWXVRV1ptYVd4cFlYUnBiMjRpT2lJaUxDSm9aaTVGYm5KdmJHeHRaVzUwU1VRaU9pSjFjMlZ5Ck1TSXNJbWhtTGxSNWNHVWlPaUpqYkdsbGJuUWlmWDB3Q2dZSUtvWkl6ajBFQXdJRFJ3QXdSQUlnZEVFM2VqMU0KSm9iNzBWTklyUlZYZ21OYUd6Rk9tK0NheGpVSUcwWU55K1VDSUFyL1lONnRYSTJRMTJ1VktLZkltN1BEU1JaSgowcm90dWhvbzhXUGhnL2pPCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K" | base64 -d | openssl x509 -text
```

### Certificate Revocation

1. Revoke certificate

```bash
export FABRIC_CA_CLIENT_HOME=${PWD}/organizations/peerOrganizations/org1.example.com/

fabric-ca-client revoke -e user1 --gencrl --tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem
```

1. Decode CRL

```bash
openssl crl -in ${PWD}/organizations/peerOrganizations/org1.example.com/msp/crls/crl.pem -text
```

1. Decode User certificate and compare serial no

```bash
openssl x509 -serial -in ${PWD}/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/signcerts/cert.pem
```

### Updating channel

1. Fetch config block

```bash
peer channel fetch -c mychannel config
```

1. CRL to base64

```bash
base64 -i ${PWD}/organizations/peerOrganizations/org1.example.com/msp/crls/crl.pem > crl
```

1. Convert the protobuf version of the channel config into a JSON

```bash
configtxlator proto_decode --input mychannel_config.block --type common.Block --output mychannel_config.json
```

1. extract config part from the channel json or remove out all of the unnecessary metadata from the config (we can remove the **mychannel_config**),

```bash
jq '.data.data[0].payload.data.config' mychannel_config.json > config.json
```

1. encode config

```bash
configtxlator proto_encode --input config.json --type common.Config --output config.pb
```

1. Modify the config and Adding CRL to modified config json

```bash
jq --arg new "$(cat crl)" '.channel_group.groups.Application.groups.Org1MSP.values.MSP.value.config.revocation_list? += [$new]' config.json > modified_config.json
```

1. Encode Modified Config

```bash
configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb
```

1. Compute Delta between the original config and modified config

```bash
configtxlator compute_update --channel_id mychannel --original config.pb --updated modified_config.pb --output crl_update.pb
```

1. Decode Delta

```bash
configtxlator proto_decode --input crl_update.pb --type common.ConfigUpdate --output crl_update.json
# adding header in the delta
echo '{"payload":{"header":{"channel_header":{"channel_id":"mychannel", "type":2}},"data":{"config_update":'$(cat crl_update.json)'}}}' | jq . > crl_update_in_envelope.json
```

1. Encode the delta

```bash
configtxlator proto_encode --input crl_update_in_envelope.json --type common.Envelope --output crl_update_in_envelope.pb
```

1. Update the channel using admin

```bash
peer channel update -f crl_update_in_envelope.pb -c mychannel -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls true --cafile $ORDERER_CA
```

### Verification

1. Query using the user

```bash
CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","100"]}'
```

1. Query with admin

```bash
peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","100"]}'
```