# Using Blockchain to Manage CA Certificates

## Content
- [Introduction to smart contracts](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab4/README.md#introduction-to-smart-contracts)
- [Introduction to blockchain based PKI](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab4/README.md#introduction-to-blockchain-based-pki)
- [Environment Setup](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab4/README.md#environment-setup)
- [Fabcar example](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab4/README.md#fabcar-example)
- [Using Blockchain to Manage CA Certificates](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab4/README.md#manage-certificates-using-smart-contract)
- [Exercises](https://github.com/Yu-Tsern/DigitalCertificateLab/blob/master/lab4/README.md#exercises)

## Introduction to smart contracts:
 
Contracts are written or spoken agreements. They are intended to be enforceable by law. Thus, all trust in contracts are inherited from the trust in legal system. However, with the advent of blockchains and cryptocurrencies, people started exploring whether this centralized trust model can be decentralized or anonymized so that the idea of contract can be incorporated into blockchains. Currently, there is a prevailing solution called smart contracts. Terms of clauses are programed and stored on the blockchains instead of being written on papers. With blockchain's help, they will be transparent and immutable after they were posted. In this tutorial, we demonstrate an usage of smart contracts, maintaining the validities of Certificate Authorities. 

## Introduction to blockchain based PKI

Assume each browser has installed a set of trusted Certificate Authority (CA) certificates. CAs (like Verisign, Symantec, etc.) create root certificates and sign Certificate Signing Requests (CSRs) for other certificates. 

An organization that owns a domain that wishes to use ssl/tls needs a certificate, as per the protocol. However, if the client does not trust that certificate, then he can refuse the connection. A client verifies certificates sent to him by looking at which certificate signed that certificate. If it was signed by a trusted certificate, normally a CA certificate, then the communication continues.

If, for some reason, CA certificates are compromised, then the attacker will be able to establish trusted channels of communication between himself and a user. At this point, there must be some kind of update to tell browsers not to trust a certain certificate. But there may be a lag between malicious certificates already in the wild and when a client is notified of a revoked certificate.

The blockchain keeps this certificate list live. Essentially, if a CA comes out to say that a certain certificate is being revoked and should no longer be trusted, the CA will simply say so on the blockchain and insert a new trusted certificate (this is assumed to be valid, since we are still trusting this CA). 

This blockchain will be a private blockchain, meaning that only those with the right permissions can write to the blockchain. We allow anyone to act as a node on the blockchain to listen, but not write. 


## Environment setup
There are several software packages needed to be installed and some configurations should be made before you can run hyperledger fabric. Follow the instructions below to setup the environment. 

### Install cURL
```
sudo apt-get install curl
```

### Install docker
```
sudo apt-get install docker-compose
```
### Install Go
The installation process of Go can be found on its [website](https://golang.org/doc/install). First, download the [file](https://golang.org/dl/), and extract it to "/usr/local".
```
tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
```

Export "/usr/local/go/bin" to your PATH environment variable. 

```
export PATH=$PATH:/usr/local/go/bin
```

If you would like to make this change permenent, you can add this line to your "/etc/profile" (for a system-wide installation) or "$HOME/.profile". Those scripts will be executed when you log into your computer, thus, you don't have to run this command manually next time. It is recommended that you write a program in go to test if it is installed ssuccesfully. The following code is a simple helloworld program:
```
package main

import "fmt"

func main() {
	fmt.Printf("hello, world\n")
}
```
Create your workspace directory, "$HOME/go", which should be consistent to your GOPATH environment variable, and make the directory "src/hello" inside your workspace, and in that directory create a file named hello.go containing the code above. Build the program using:
```
cd $HOME/go/src/hello
go build
```
You will see a executable file "hello" being generated. Execute that file using:
```
./hello
```
If your go was installed successfully, you can see the "hello, world" message.

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

### Add user to group
To enable non-root user to run docker commands, run the command:
```
sudo usermod -aG docker $USER
```
This will take effect after restarting your VM

### Download docker image
```
sudo curl -sSL http://bit.ly/2ysbOFE | bash -s 1.3.0-rc1
sudo curl -sSL http://bit.ly/2ysbOFE | bash -s 1.3.0-rc1 1.3.0-rc1 0.4.12
```

## Fabcar example
In order to get more familiar with Hyperledger fabric, you will go through an sample application that manages car ownerships in this section.

### Network exploration
First, try to bring up the blockchain network using scripts "byfn.sh". 

#### Network activation
First, get into "fabric-samples/first-network" directory and then run the script. Take a look at the logs it generated. They shows details of how the network was brought up. 
```
cd fabric-samples/first-network
sudo ./byfn.sh generate
sudo ./byfn.sh up
```
After you're done, shut down the network by running
```
sudo ./byfn.sh down
```

#### Add new organization
Next, try to add a new organization using the script "eyfn.sh". Before you do so, make sure previous containers are all deleted. 
```
sudo ./byfn.sh down
```
Bring the network up again.
```
sudo ./byfn.sh generate
sudo ./byfn.sh up
```
Add a new organization through "eyfn.sh".
```
sudo ./eyfn.sh up
```
Skim through the log it generated, then bring the network down again.
```
sudo ./eyfn.sh down
sudo ./byfn.sh down
```

### The Fabcar application
To get more familiar with Hyperledger Fabric applications, you will play with the sample code Fabcar in this section.

#### Delete all container and network
Similar to every other steps in this lab, bring down the network.
```
./byfn.sh down
```
In addition, delete stale containers and network so that it won't mess up with the new one.
```
cd fabric-samples/fabcar  && ls
docker rm -vf $(docker ps -a -q)
docker rmi -f $(docker image -a -q)
docker container prune
docker network prune
```
if this is not the first time you play with this tutorial, you also need to delete the underlying chaincode image for the fabcar smart contract:
```
docker rmi dev-peer0.org1.example.com-fabcar-1.0-5c906e402ed29f20260ae42283216aa75549c571e2e380f3615826365d8269ba
```

#### Install the dependency
```
npm install -g
```

#### enroll the admin user and use this credential to register the first user 
```
node enrollAdmin.js
node registerUser.js
```
#### Query the channel
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
#### Invoke the channel
Run the following command to create a car.
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

## Manage certificates using smart contract
Obviously, managing car ownership is similar to managing digital certificates. Slightly modifying the sample code yieds a decent blockchain application handling digital certificates. In this section, we will show you the modification process. 

### Reset the environment
In order not to mess up with the older environment, reset the network and container using following commands. If at any point, something has been messed up. Feel free to reset them all and start from the beginning of execution.

```
cd fabric-samples/fabcar  && ls
docker rm -vf $(docker ps -a -q)
docker rmi -f $(docker image -a -q)
docker container prune
docker network prune
sudo docker rmi dev-peer0.org1.example.com-fabcar-1.0-5c906e402ed29f20260ae42283216aa75549c571e2e380f3615826365d8269ba
```
### Modify the code

In this section, you are encouraged to implement a certificate management application by yourself. However, you can still use the code provided in this directory. In order to write this application, you have to first define what attributes should be included in a certificates, and how those information can be retrieve from the blockchain. These can all be specified in three files, "chaincode/fabcar/go/Fabcar.go", "fabcar/query.js", and "fabcar/invoke.js". Details are described as follows.

#### Fabcar.go
Let’s look at the smart contract code, which is the core logic of our application. You can see that we construct our ‘Cert’ structure to contain 3 parameters: Name, Raw, Revoked. Name is the ‘Subject Name’ in a certificate i.e. Verisign; Raw is the raw bytes of a certificate; Revoked is a boolean of whether this certificate has been revoked. When we make a request to this smart contract, we call the function ‘invoke’. ‘Invoke’ then reroutes that request to the appropriate function. We have already written some of these functions, namely ‘queryCert’, ‘createCert’, and ‘revokeCert’. We have also defined ‘initLedger’, which is called once upon initialization of the smart contract. We have defined some root certificates to jumpstart this blockchain. Go through the functions to make sure you understand how each function works.

#### Query.js
Let’s look at the code to see how query.js makes actual queries. Notice how we are communicating with the blockchain at grpc://localhost:7051. This is the port at which we can communicate with our chain. Notice in line 43, we are interacting as ‘user1’. In line 52, we are constructing our query request. The chaincode is named ‘fabcar’ and we are calling function ‘queryCert’ from our smart contract. We pass the necessary args required in our function.

We will modify this request for different purposes as we continue this module. 

#### Invoke.js
Let’s look at the code to see how invoke.js modifies the blockchain. You may notice this invoke.js is very similar to query.js, but they are inherently different as invoke.js creates transactions, which modifies the blockchain either by adding a certificate or modifying an existing one. In line 61, we are creating a request very similar to query.js. We want to do the same thing. Call a certain ‘fcn’ with certain ‘args’ will be called against our blockchain smart contract.



### Code Test
Just like the car example above, run the script startFabric.sh to start up the blockchain, 
```
sudo ./startFabric.sh
```

Then, enroll an admin onto the blockchain since this is a permissioned ledger.

```
node enrollAdmin.js
```

Register users to query blockchain.

```
node registerUser.js
```

Now, you can interact with the blockchain as a user using:
```
node query.js
```
or
```
node invoke.js
```

Similar to the example above, "query.js" is used to get data out of the blockchain, while "invoke.js" is used to modify data on the blockchain. Either as a new object or redefining a previously made object.


## Exercises
1. As an exercise, shall we be using query.js or invoke.js to create requests for ‘queryCert’, ‘createCert’, and ‘revokeCert’?
2. As an exercise, create a new certificate and demonstrate that you can retrieve that certificate.
3. As an exercise, revoke your previously created certificate.
4. Consider a scenario in which you rely on this blockchain to manage all your trusted root certificates. You have shut off your computer and in this time, a certificate has been revoked and a new one has been created and placed on the blockchain in its place. Now, you restart your computer. Will you be able to connect to this server immediately upon restart? Why or why not? 
