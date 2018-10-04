### lab4

## Prerequisite

### Install cURL
```
sudo apt-get install curl
```
### Install docker
```
sudo apt-get install docker-compose
```
### Install Go

### Install nodejs
```
sudo apt-get install nodejs
sudo apt install npm
npm install npm@5.6.0 -g
```

### Install python
```
sudo apt-get install python
```

## Download docker image

### Add user to group
This fixs permission denied
```
sudo usermod -a -G docker $USER
```
restart your VM

### Download docker image
```
sudo curl -sSL http://bit.ly/2ysbOFE | bash -s 1.3.0-rc1
sudo curl -sSL http://bit.ly/2ysbOFE | bash -s 1.3.0-rc1 1.3.0-rc1 0.4.12
```
## Network exploration

### Network activation
```
cd fabric-samples/first-network
sudo ./byfn.sh generate
sudo ./byfn.sh up
sudo ./byfn.sh down
```

### Add new organization
First, delete previous container
```
sudo ./byfn.sh down
sudo ./byfn.sh generate
sudo ./byfn.sh up
```
Add by script
```
./eyfn.sh up
```
## Application exploration
Delete all container and network
```
./byfn.sh down
cd fabric-samples/fabcar  && ls
docker rm -vf $(docker ps -a -q)
docker rmi -f $(docker image -a -q)
docker container prune
docker network prune
```

if this is not the first time you play with this tutorial:
```
docker rmi dev-peer0.org1.example.com-fabcar-1.0-5c906e402ed29f20260ae42283216aa75549c571e2e380f3615826365d8269ba
```
Install the dependency
```
npm install -g
```
enroll the admin user and use this credential to register the first user 
```
node enrollAdmin.js
node registerUser.js
```
Query the channel
```
node query.js
```
Try using different function, change fcn from queryAll to query car
```
const request = {
  //targets : --- letting this default to the peers assigned to the channel
  chaincodeId: 'fabcar',
  fcn: 'queryCar',
  args: ['CAR4']
};
```
Now try query
```
node query.js
```
Try to create a car, change the code
```
var request = {
  //targets: let default to the peer assigned to the client
  chaincodeId: 'fabcar',
  fcn: 'createCar',
  args: ['CAR10', 'Chevy', 'Volt', 'Red', 'Nick'],
  chainId: 'mychannel',
  txId: tx_id
};
```
Some of the class has been modified, but the sample code wasn't change. Fix this on line 105:
```
let event_hub = channel.newChannelEventHub('localhost:7051');
// event_hub.setPeerAddr('grpc://localhost:7053');
```
and line 130:
```
console.log('The transaction has been committed on peer ' + event_hub.getPeerAddr());
```
Use invoke to create the car.
```
node invoke.js
```
Now, try to change the owner.
```
var request = {
  //targets: let default to the peer assigned to the client
  chaincodeId: 'fabcar',
  fcn: 'changeCarOwner',
  args: ['CAR10', 'Dave'],
  chainId: 'mychannel',
  txId: tx_id
};
```
run invoke to change the car owner
```
node invoke.js
```
use query to see if the owner is changed
```
node query.js
```

