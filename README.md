# Use IBM Blockchain Platform extension in VSCode to develop a Smart Contract

In tutorial, we will learn the process of using IBM Blockchain Platform's VSCode extension 
to  streamline the 
process of developing, testing, and deploying a smart contract. The audience for this 
tutorial is a developer that has some basic understanding of blockchain, and wants to 
understand how to deploy a network to a Hyperledger fabric instance. Once you finish the 
tutorial, you will understand how to quickly develop, demo, and deploy your blockchain
application on a multi-organization network using VSCode. 

### Prerequisites

You will need the following installed in order to use the extension:
- [Node v8.x or greater and npm v5.x or greater](https://nodejs.org/en/download/)
- [Yeoman (yo) v2.x](http://yeoman.io/)
- [Docker version v17.06.2-ce or greater](https://www.docker.com/get-docker)
- [Docker Compose v1.14.0 or greater](https://docs.docker.com/compose/install/)

If you are using Windows, you must also ensure the following:
- Your version of Windows supports Hyper-V and Docker:
  - Windows 10 Enterprise, Pro, or Education with 1607 Anniversary Update or later
- Docker for Windows is configured to use Linux containers (this is the default)
- You have installed the C++ Build Tools for Windows from [windows-build-tools](https://github.com/felixrieseberg/windows-build-tools#windows-build-tools)
- You have installed OpenSSL v1.0.2 from [Win32 OpenSSL](http://slproweb.com/products/Win32OpenSSL.html)
  - Install the normal version, not the version marked as "light"
  - Install the Win32 version into `C:\OpenSSL-Win32` on 32-bit systems
  - Install the Win64 version into `C:\OpenSSL-Win64` on 64-bit systems

You can check your installed versions by running the following commands from a terminal:
- `node --version`
- `npm --version`
- `yo --version`
- `docker --version`
- `docker-compose --version`

### Learning Objectives
- Get, install, and troubleshoot your IBM Blockchain Platform VSCode Extension
- Create a new smart contract project using Node.js with the VSCode Extension
- Explore, understand, and package the smart contract
- Deploy the smart contract on both IBM Cloud and local Hyperledger Fabric instance
- Use SDK to interact with deployed Smart Contract package

### Estimated time
- After the prerequisites are installed, this should take <b>approximately 30-45 minutes</b> to complete.

### Let's get started
![packageFile](/docs/installExtension.gif)
First thing we need to do is to install the IBM Blockchain Platform VSCode extension. You will
need to latest version of VSCode to do this. To see if you have the latest VSCode extension, go to
`Code` -> `Check for Updates`. If VSCode crashes at this point (which it did for me), it likely
means you don't have the latest version. Check the troubleshooting section below if your VSCode
crashed. Otherwise, update your VSCode, and once you're done, click on `extensions` on the 
side bar on the left part of your screen. At the top, search the extension marketplace for 
`IBM Blockchain Platform`. Click on `Install`. Then click on
`reload`. Nice, you are all set to use the extension! 🙌🏼

## 1. Create a new smart contract project
![packageFile](/docs/createSmartContract.gif)
To create a smart contract project, click on your newly
downloaded IBM Blockchain Platform extension. It should be the extension all the way at the bottom of the left side bar. Next, use the keyboard shortcut `Shift` + `CMD` + `P` to 
bring up all of the commands for the IBM Blockchain Platform. Choose 
`Create Smart Contract Project` from the dropdown. Next,
click `JavaScript` from the dropdown. Next, click `New Folder`, and name the project what you want. I named mine
`demoContract`. Click `Create` and then `Open` your new folder which you just created. Next, you'll be prompted with a dropdown, and you can click `Add to Workspace`. Once the extension is done packaging your
contract, you can open the `lib/my-contract.js` file to see your smart 
contract code scaffold. Nice job! 👏🏼

## 2. Modify Chaincode 
![packageFile](/docs/addChaincodeCursor.gif)
Inside your `lib/my-contract.js` file, go ahead and copy 
and paste this code: 

```
'use strict';

const { Contract } = require('fabric-contract-api');

class MyContract extends Contract {

    async instantiate(ctx) {
      let greeting = { text: 'Hi' };
      await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
    }

    async transaction1(ctx, arg1) {
        console.info('transaction1', arg1);
        let greeting = { text: arg1 };
        await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
        return greeting;
    }

}

module.exports = MyContract;
```

It's not much, but it will the 
`await ctx.stub.putState('GREETING', Buffer.from
(JSON.stringify(greeting)));` line will update the ledger
with the key-value pair of 
```
{
  "GREETING": {
    "text": "hi" 
  }
}
```
Not much going on here, since this is a hello-world 
tutorial, but it shows up the basics of updating the 
ledger, which is the most important part of being a 
blockchain developer. Also, in `transaction1` you will see that we 
are actually returning an object, so we will be able to see if 
our smart contract is invoked successfully since we can inspect the
object that is returned from this function. Save the file, and proceed! Good job! 🔥

## 3. Package Smart Contract
![packageFile](/docs/packageSmartContract.gif)
Now that we have created our smart contract, it's time to package it so we can 
connect to a Hyperledger Fabric instance. Let's do our favorite keyboard shortcut 
again, `Shift` + `CMD` + `P` and then look for `package smart contract`, and click 
on that. The extension will do the rest for you, so sit back, relax, and drink some ☕️.
In the left sidebar, click on the IBM Blockchain Platform icon. This click will take you
into another dimension - the blockchain dimension. On the top-left corner, you will 
have all of your smart contract packages. You should see `demoContract@0.0.1` if 
everything went well. Nice job! Time to install, and instantiate this contract! 🙌🏼

## 4. Install Smart Contract
Ok, we're more than halfway there. Now for the fun part! Let's install this contract on the peer!
To do this, we must first connect to a Hyperledger Fabric network. The network that comes with
the VSCode extension is perfect for development - it offers the minimal set up to develop, and 
test your contract. The network consists of a peer, an orderer, a certificate authority, and 
a couchdb instance, all running on seperate docker containers. To start our network, look at 
your IBM Blockchain Platform extension, at the bottom-right corner where it says `Blockchain Connections`.
You should see something that says `local_fabric`. Go ahead and click that. That should 
automatically run a script and you should see thee output as follows: 
```
Starting fabricvscodelocalfabric_orderer.example.com_1 ... done

Starting fabricvscodelocalfabric_ca.example.com_1      ... done

Starting fabricvscodelocalfabric_couchdb_1             ... done
Starting fabricvscodelocalfabric_peer0.org1.example.com_1 ... 

Starting fabricvscodelocalfabric_peer0.org1.example.com_1 ... done
``` 
Which shows the docker containers starting up. There is additional output too, such as this:
```
Hyperledger Fabric started in 2 seconds
2018-11-07 20:04:10.201 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
Blockchain info: {"height":53,"currentBlockHash":"c2us3Z7thnSlumK3zTXxRZEvBzN6lrQ8iiOb+wwBhzU=","previousBlockHash":"piZs7C8GdmDoApYMa71p4IMdHeVNFk6enuL3Vt0Mp8Y="}
```
Nice. Now, click the `local_fabric` connection again. Now that it's up and running, it should 
take you to your channel view, which should show one channel, named `mychannel`. Click on 
`mychannel`, and it will expand the `Peers` and `Instantiated Smart Contracts`. Click on 
`peers` and you should see `peer0.org1.example.com`. Right-click on that peer, and click on
`Install Smart Contract`. Next, the extension will ask you which package, and just choose
`demoContract@0.0.1`. That's it! Nice job!


<!-- ## Included components

* [IBM Blockchain Starter Plan](https://console.bluemix.net/catalog/services/blockchain): Use the IBM Blockchain Platform to simplify the developmental, governmental, and operational aspects of creating a blockchain solution.

## Featured technologies
* [IBM Blockchain](https://www.ibm.com/blockchain): Blockchain is a shared, immutable ledger for recording the history of transactions.
* [Cloud](https://www.ibm.com/developerworks/learn/cloud/): Accessing computer and information technology resources through the Internet.

# Watch the Video - Setting up the Node app (Part 1)

[![](docs/part1.png)](https://www.youtube.com/watch?v=3a8ElLxyQAc)

# Watch the Video - Invoking the Node app (Part 2)

[![](docs/part2.png)](https://www.youtube.com/watch?v=R16yLvs6Aeo)

# Prerequisites
1. NPM version >= 5.6.0 
2. Node version >= 8.10.0
3. If you do not have an IBM Cloud account yet, you will need to create one [here](https://ibm.biz/BdjLxy).
4. In your IBM Cloud account, create a Blockchain Starter Plan service on your IBM Cloud account, as shown below:

![nyDSaF](https://i.makeagif.com/media/4-11-2018/nyDSaF.gif)


Then, click on `Launch`, after your network is created. All commands in this tutorial that are 
outside of your editor will be in this newly created network. <b>I will refer to it as "IBM Blockchain Starter Plan" from now on.</b>



# Steps

1. [Clone the repo](#step-1-clone-the-repo)
2. [Enroll App](#step-2-enroll-app)
3. [Register Users](#step-3-register-users)
4. [Invoking Chaincode](#step-4-invoking-chaincode)
5. [Running the app](#step-5-running-the-app)
6. [Querying the ledger](#step-6-querying-the-ledger)
7. [Conclusion + Updating Chaincode](#step-7-conclusion--updating-chaincode)


## Step 1. Clone the repo
The first thing we need to do is clone the repo on your local computer.

```
$ git clone https://github.com/IBM/car-auction-network-fabric-node-sdk
```
Then, go ahead and go into the directory:

```
$ cd car-auction-network-fabric-node-sdk
```

## Step 2. Enroll App 
 ![packageFile](/docs/enrollAdmin.gif)

First, we need to generate the necessary keys and certs from the Certificate Authority to prove our authenticity to the network.
To do this, we will go into our new IBM Blockchain Starter Plan network, and from the `Overview` Tab on the left, we will click on `Connection Profile` on the right-side of the page. Then click on `Raw JSON`.

Open `enrolladmin.js` in an editor of your choice. I prefer VSCode.

Down around line 40 of the file, you will see a new instance of the Fabric_CA_Client. This is where we
need to give our application the necessary endpoints of our CA from our IBM Blockchain Starter Plan.

We will need 4 things from the Certificate Authority
1) `enrollId` - should be "admin"
2) `enrollSecret` - should be similar to "1dcab332aa"
3) `url` - should be similar to 
"nde288ef7dd7542d3a1cc824a02be67f1-org1-ca.us02.blockchain.ibm.com:31011"
4) `caName` - should be "org1CA"

Your code should look something like this when finished:

```
fabric_ca_client = new Fabric_CA_Client('https://admin:4352f3499a@nd61fdbe87a194a10bde3cccdb90d427e-org1-ca.us04.blockchain.ibm.com:31011', null ,"org1CA", crypto_suite);
```

Once you fill out the necessary info as shown in the gif above, move down to the call to 
enroll the CA. You will need to add in the enrollSecret there again. Should be around 
line 55. 

Your code should look something like this when finished (note, this is just a small chunk of the code)

```
return fabric_ca_client.enroll({
          enrollmentID: 'admin',
          enrollmentSecret: '4252f3499a'
        }).then((enrollment) =>
```

Save your file, and run npm install:

```
$ npm install
```

Then, run this command to enroll the admin:

```
$ node enrollAdmin.js
```

If all went well, you should get a response like this.

```
carauction 👉🏼  node enrollAdmin.js
 Store path:/Users/Horea.Porutiu@ibm.com/Workdir/blockchain/carauction/hfc-key-store
(node:86820) 
Successfully enrolled admin user "admin"
Assigned the admin user to the fabric client ::{"name":"admin","mspid":"org1","roles":null,"affiliation":"","enrollmentSecret":"","enrollment":{"signingIdentity":"b*4d7843af972bcfb7dac51f641458af95a54b4904d98da67e5b1db934adf35a","identity":{"certificate":"-----BEGIN CERTIFICATE-----\nMIIB8TCCAZigAwIBAgIULHILXwt3DhxQSW7gdzNmziY18iAwCgYIKoZIzj0EAwIw\nbzELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK\nEwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMSAwHgYDVQQDExdmYWJyaWMt\nY2Etc2VydmVyLW9yZzFDQTAeF*0xODA5MTcxODMxMDBaFw0xOTA5MTcxODM2MDBa\nMCExDzANBgNVBAsTBmNsaWVudDEOMAwGA1UEAxMFYWRtaW4wWTATBgcqhkjOPQIB\nBggqhkjOPQMBBwNCAAQIDN3iZJeoQbFE7+3ShqlhQd6cYsxrOAWs3nGlv/SC+qQV\nQd33uwkkbcs8PTVlWgM6FsmoNZfMEhx5LH1pW+y0o2AwXjAOBgNVHQ8BAf8EBAMC\nB4AwDAYDVR0TAQH/BAIwADAdBgNVHQ4EFgQUuI4+VTbgNTXcnYg+8qpGXf/mUmIw\nHwYDVR0jBBgwFoAUo/8jv0agwT3tol8HsbOmorxdijkwCgYIKoZIzj0EAwIDRwAw\nRAIgTEJKJL6/U/wMgxqG25K5NW4A5+ie1vG9qi7zP98wVVoCIB7hV0en6cV3nI9L\nMdWPTvgrB67CCL8Ay+yJ25B8hMO9\n-----END CERTIFICATE-----\n"}}}
```

## Step 3. Register Users 
![packageFile](/docs/registerUser.gif)

Now that we have generated our client side certificates, and stored them in `htc-key-store`, we need
to register our application so that the network recognizes it. 

Open `registerUser.js` in the editor of your choice. On line 42, we can see a new instance of the 
Fabric_ca_client being created. We can simply copy our existing instance from `enrollAdmin.js`. This is 
very important <b> your URL should be the same as in `enrollAdmin.js` </b>.

After you copy and paste, the code in `registerUser.js` should look like this on line 42, except
your credentials will be different than mine:

```
fabric_ca_client = new Fabric_CA_Client('https://admin:4352f3499a@nd61fdbe87a194a10bde3cccdb90d427e-org1-ca.us04.blockchain.ibm.com:31011', null ,"org1CA", crypto_suite);
```

Note that from the first gif to the second gif that my credentials changed. That is just 
because I needed to get a new network. Yours will remain the same.  

Now, run this command to register and enroll `user1`. You can only register an identity once. If you 
get errors, it is probably because that user is already registered - you can try it with a different name. 

```
$ node registerUser.js 
```

If successful, output should be something like this: 

```
carauction 👉🏼  node registerUser.js
 Store path:/Users/Horea.Porutiu@ibm.com/Workdir/blockchain/carauction/hfc-key-store
Successfully loaded admin from persistence
Successfully registered user1 - secret:OnqVuU*tCwPU
Successfully enrolled member user "user1"User1 was successfully registered and enrolled and is ready to interact with the fabric network
```

🙌🏼 Nice! You are almost there!

## Step 4. Invoking Chaincode 
![packageFile](/docs/invoke.gif)

Now, we need to download the connection profile, and move it to our current working directory. 
1. Go back to your IBM Blockchain Starter Plan, and click on the `Overview` tab in the top-left corner.
2. From the `Overview` tab, click on `Connection Profile`.
3. Once you click on `Connection Profile` you'll have an option to view as `Raw JSON` or `Download`. Click on `Download`. 

This should automatically download the file and add it to your recently downloaded folder.
This recently downloaded file will look something like this:

`creds_nde288ef7dd6542d3a1cc824a02be67f1_org1.json`. 

4. Rename the file to: `creds.json`. 

<b>And yes, this is important. It needs to be exactly `creds.json`, since 
this file is referenced at the top of </b>`invoke.js` - https://github.com/IBM/car-auction-network-fabric-node-sdk/blob/master/invoke.js#L10.

5. Move the `creds.json` file to the `car-auction-network-fabric-node-sdk` directory. 

Next, we need to install the chaincode on the peers. Then, we will need to instantiate the chaincode. 
From the `Overview` tab on the left, click on `Install Code` on the bottom-left hand side of the screen. Then, click on `Install Chaincode` on 
the right-side of the screen.

You should be prompted with the following form: 

```
Chaincode ID:   
Chaincode Version: 
Chaincode Type: 
```

Fill it out as shown below: 

```
Chaincode ID: carauction  
Chaincode Version: 1
Chaincode Type: Node
```

To do this, simply fill out the form with the same exact way,  and increase the `Chaincode Version` by one. This will enable you to do a chaincode update, and will allow you to use the new chaincode when invoking transactions 

-------------⚠️🚧⚠️IMPORTANT - NEED TO CHOOSE 2 FILES FOR CHAINCODE UPLOAD TO PEERS⚠️🚧⚠️----------------
Choose your chaincode files from the `car-auction-network-fabric-node-sdk/chaincode` (recently cloned) 
directory. 
Inside that directory, you should find a `package.json` and `carauction.js` file. Select both of those.
You should see `2 files selected`. Then click `Submit`.

Once the chaincode in installed, we need to instantiate it. From the same screen, click on the 
3-dot symbol under `Actions`. Then click `Instantiate`.

For `Chaincode Type` select `Node`. Then click `Next`. Next, leave the defaults on the next screen,
which show a simple endorsement policy. Just click `Submit`. Note - the policy specifies which peers
will need to validate a new transaction. We are choosing the 
simple policy here to keep things short and simple. For a more complex network, you can 
choose the advanced option to give certain members more influence in validating a new transaction. Click [here](https://console.bluemix.net/docs/services/blockchain/howto/install_instantiate_chaincode.html#installing-instantiating-and-updating-a-chaincode)
to learn more. 

Next, let's click on the `Channels` tab on the left side. Then click on the `defaultchannel`.
You should see the `total blocks` and `time since last transaction`. If we click on the 
block number, we can see our `initLedger` function being called, and the data that was 
written to the ledger.

Open `invoke.js` in the editor of your choice. You'll see at the top of the file
we import our connection profile from IBM Blockchain Platform with this line: 

```
var creds = require('./creds.json');
```

Just save the file and then use this command to invoke chaincode on our network:

```
$ node invoke.js
```

If all went well, you should see something like this: 

```
carauction 👉🏼  node invoke.js
Store path:/Users/Horea.Porutiu@ibm.com/Workdir/blockchain/carauction/hfc-key-store
Successfully loaded user1 from persistence
Assigning transaction_id:  812694fba979d495423a40701fed3b221e7d7add2eecde8f9dd9a64827c8aced
Transaction proposal was good
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
The transaction has been committed on peer n9fb94659f16c4d85a20219df994288bb-org1-peer1.us03.blockchain.ibm.com:31002
Send transaction promise and event listener promise have completed
Successfully sent transaction to the orderer.
Successfully committed the change to the ledger by the peer
```

Lastly, we can refresh our page where we can see the details of `defaultchannel` and we 
should see a our total blocks increase by one. This is due to our last call to `invoke.js`. 

If you look around line 60 of `invoke.js` you should see this: 

```
    var request = {
        //targets: let default to the peer assigned to the client
        chaincodeId: 'carauction',
        fcn: 'initLedger',
        args: [''],
        chainId: 'mychannel',
        txId: tx_id
      };
```
This is where we call the different functions in our chaincode. By default, we call initLedger.
This is why our new block reflects this function call. 

## Step 5. Running the app 
![packageFile](/docs/runApp.gif)

Now that we have connected our app to the IBM Blockchain Platform, each update of the ledger will
be recorded and added as a block. Let's run our app and see what it can do. You can find all
commands that I run in this step at `snips.txt` in this repo.

Go to line 60 of `invoke.js` and copy and paste this code instead what was there before. We are now
going to call the makeOffer method and pass in 3 arguments as shown in the code below:

```
var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'carauction',
    fcn: 'makeOffer',
    args: ['3000', 'ABCD', 'memberA@acme.org'],
    chainId: 'mychannel',
    txId: tx_id
  };
```

Note that in initLedger we created a car and assigned the owner of the car to be `memberA@acme.org`. 
Our auction does not allow the owner of car to bid on his/her own car. <b>Thus, this call should give 
us an error.</b> This step is to show you the error throwing capabilities of the chaincode.
Let's try it. Save `invoke.js` and then run this command to invoke our app. Note, this 
step (the node invoke step) will be repeated 5 more times below, but I will exclude it for the sake of repetition. So 
remember, every time you want to invoke our app, you'll need to change the request variable,
save the file, and then run the command below: 

```
$ node invoke.js
```

You should get an error message like this: 
```
Assigning transaction_id:  06c289642a2b8cfd6c0cff41411c5b6a6fa45472f4e5af56c6adee3f06d98b71
[ [ { Error: 2 UNKNOWN: error executing chaincode: transaction returned with failure: Error: owner cannot bid on own item:
    at...
```

Next, let's give a successful transaction. Copy and paste the code for the request as follows:

```
var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'carauction',
    fcn: 'makeOffer',
    args: ['4000', 'ABCD', 'memberB@acme.org'],
    chainId: 'mychannel',
    txId: tx_id
  };
```
This should work, and now we have an offer from MemberB coming in at $4,000. If we check the 
channel in Starter Plan, we can see the data that was written to the ledger.

Next, let's give another successful offer. Copy and paste the code for the request as follows:

```
var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'carauction',
    fcn: 'makeOffer',
    args: ['5000', 'ABCD', 'memberC@acme.org'],
    chainId: 'mychannel',
    txId: tx_id
  };
```

This will create an offer from Member C coming in at $5,000, which is greater than the reserve price.
If we check the Starter Plan again, we can see this data being written to the ledger, and the block count
increasing by one.

Next, let's give an offer that is too high...that is the offer is greater than the balance in the account.
<b> Note - this should throw an error. This is to show error checks of the chaincode. </b>

```
var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'carauction',
    fcn: 'makeOffer',
    args: ['5001', 'ABCD', 'memberB@acme.org'],
    chainId: 'mychannel',
    txId: tx_id
  };
```
Since our members are initialized with a balance of $5,000, this will not work. You should get an error 
message as follows: 

```
{ Error: 2 UNKNOWN: error executing chaincode: transaction returned with failure: Error: The bid is higher than the balance in your account!
```

Lastly, let's close the bidding. Use this code as follows for the request:

```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'carauction',
    fcn: 'closeBidding',
    args: ['ABCD'],
    chainId: 'mychannel',
    txId: tx_id
  };
```

Save the file and issue the 
following command, as we have been doing all along up to this point. 
```
$ node invoke.js
```

You should get a successful response. If you check the output of the block details, we can see 
that the new owner of the car is MemberC. We also see that Member C now has $0 in their balance,
since they had $5,000 to start with, and their bid of $5,000 won the auction. That means that 
the new owner is Member C, and that Member A, the original owner of the car, will be credited
$5,000. This is reflected on the ledger - Member A now has a balance of $10,000. Lastly,
if we check the vehicle listing, we can see that the status is `SOLD`.

Awesome! You just successfully ran an auction app while using Hyperledger Fabric Node SDK + IBM Blockchain
Starter Plan. There's only one more thing I want to show you before you leave...

## Step 6. Querying the Ledger
![packageFile](/docs/query.gif)

Now that we have submitted transactions on the ledger, we can query the ledger at any point, using the 
key corresponding to that object. So first, let's query Member A. This is the member that just won 
the auction, so they should have $10,000 in their account. To query, open up `query.js` in the editor
of your choice. Just like `invoke.js` there is a request variable that allows us to pass in a 
key to query the ledger. Let's pass in `memberA@acme.org` for the key. The request variable
on line 55, should as follows:

```
  const request = {
    //targets : --- letting this default to the peers assigned to the channel
    chaincodeId: 'carauction',
    fcn: 'query',
    args: ['memberA@acme.org']
  };
```

Our response should look something like this:

```
Response is  {"balance":10000,"firstName":"Amy","lastName":"Williams"}
```

We can query memberB as follows, but this is not too interesting since memberB 
did not win the auction. The output for querying memberB is as follows:

```
Response is  {"balance":5000,"firstName":"Billy","lastName":"Thompson"}
```

Now, if we query memberC, we should see that their balance is 0. The 
request should look as follows for memberC:

```
  const request = {
    //targets : --- letting this default to the peers assigned to the channel
    chaincodeId: 'carauction',
    fcn: 'query',
    args: ['memberC@acme.org']
  };
```

With the response being:

```
Response is  {"balance":0,"firstName":"Tom","lastName":"Werner"}
```

Now if we query the vehicle number, we should see the owner be memberC. Let's 
do that with the following request:

```
  const request = {
    //targets : --- letting this default to the peers assigned to the channel
    chaincodeId: 'carauction',
    fcn: 'query',
    args: ['1234']
  };
```

The response:

```
Response is  {"owner":"memberC@acme.org"}
```

Lastly, and most interestingly, let's query our vehicle listing. It should show up
as being SOLD, and should have no offers.

The request:

```
  const request = {
    //targets : --- letting this default to the peers assigned to the channel
    chaincodeId: 'carauction',
    fcn: 'query',
    args: ['ABCD']
  };
```

The response:

```
Response is  {"description":"Arium Nova","listingState":"SOLD","offers":null,"reservePrice":3500,"vehicle":"1234"}
```
## Step 7. Conclusion + Updating Chaincode
Congrats!

🙌🏼🙌🏼🙌🏼

That's it! I showed you how to register your application and then register users 
to your application using certificates provided by the Certificate Authority. Next,
I showed you how to download your connection profile and use it to create a 
channel and add peers and an orderer to that channel. After that, we used the IBM
Blockchain Starter Plan to install our chaincode on the peers, and then 
instantiated our network on the defaultchannel. Next, we invoked our chaincode 
and created some offers on the network. We closed the bidding and saw our ledger
being updated as Member C won the auction. Lastly, we learned how to query the 
ledger to ensure that our data looks how we intend it to look.

🛑🛑🛑🛑🛑🛑🛑🛑🛑🛑🛑<b> Optional & Extra Credit - Updating Chaincode </b>🛑🛑🛑🛑🛑🛑🛑🛑🛑🛑🛑

> Note - if you want to update the chaincode - and add in your own functions, you will have to install the <b> updated chaincode</b> by <b> selecting the updated `carauction.js` and `package.json` files </b>. So follow the same process as shown above in step 4 to install chaincode, but use your newly updated files. For the form, you would fill it out as shown below, and keep incrementing the version number on subsequent updates:

```
Chaincode ID: carauction  
Chaincode Version: 2
Chaincode Type: Node
```

> Then, when you go to click on the action i.e. the three-dot button, there will be an option that says `update`. Choose that one and then keep the same defaults (the simple policy) as shown before. And that's it!

Hope you learned something, and if you find anything missing, encounter a nasty bug, error, etc. please open an issue on this repo above⤴⤴⤴⤴⤴! 🕷🕷🕷
























<!-- If you check your downloaded files now, you should have a file starting with `cred` ending in `.json`. Let's rename this file to `creds.json` and move this file to the root of our carauction-network directory.

Open `enrolladmin.js` and the newly downloaded `creds.json` in an editor of your choice. I prefer VSCode. -->












<!-- [![Deploy to IBM Cloud](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/IBM/watson-second-opinion) -->
# Links

* [IBM Blockchain - Marbles demo](https://github.com/IBM-Blockchain/marbles)
* [Hyperledger Fabric Docs](https://hyperledger-fabric.readthedocs.io/en/release-1.2/)


# Learn more

* **Blockchain Code Patterns**: Enjoyed this Code Pattern? Check out our other [Blockchain Code Patterns](https://developer.ibm.com/code/technologies/blockchain/)

* **Blockchain 101**: Learn why IBM believes that blockchain can transform businesses, industries – and even the world. [Blockchain 101](https://developer.ibm.com/code/technologies/blockchain/)

# License
[Apache 2.0](LICENSE) -->
<!-- 

at this point, you can go into your terminal and run `$ docker ps` to see the containers. You should
have 4 at this point, namely, the peer, orderer, CA, and couchdb. Nice job :) -->