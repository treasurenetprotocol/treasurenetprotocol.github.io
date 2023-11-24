---
layout: default
title: Automatic Upload Tools
parent: Pilot Producer Guide
grand_parent: Assets Production
nav_order: 3
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

# Introduction

:::info
It should be noted that all tools can only target specific data structures and are not applicable to all production storage databases.

We will continue to improve it, and at the same time, we also welcome manufacturers themselves or third-party developers to share automation tools designed by them.
:::

:::caution
What needs special explanation is that no matter which automation tool. Because of the nature of open source, we cannot completely guarantee that it will not be used maliciously.
:::

## Why we need an automation tool

Yes, even without the tools, you can still upload production by sending a certified transaction to the blockchain network. You can click [here](./production_data_manual.md) for more information.

Each day the previous 4 days' output is provided, and this may go on forever. If you have not connected the uploading of output with your internal enterprise tools or other software, this will require your engineers to repeat the work every day, which is obviously unwise.

The meaning of automatic tools is to let the software do it for us.


## Let’s get to know these two automation tools

### Production Data Process

Because the database is constantly changing, maybe the Sensor field name or other fields have changed.

So we need an automated tool to organize or clean the ever-changing database data.

Our tool currently only adapts to one type of oil and gas production database, which is described in detail next.

If you know some software development, you can use this tool as a template to modify it into a customized tool suitable for your data center.

### Production Data Uploader

The scheduled upload tool regularly extracts data from the database organized by the Production Data Process and uploads it to the blockchain network through the account information and oil well information configured by you.

# Production Data Process

:::info
Before you begin, please ensure that your host has the ability to connect to the Internet, has docker and docker-compose installed correctly, and has a available MongoAtlas database.

For the installation of docker, please refer to [here](https://docs.docker.com/engine/install/)

For the installation of docker-compose, please refer to [here](https://docs.docker.com/compose/install/)

To apply for free MongoAtlas, please refer to [here](https://www.mongodb.com/atlas)
:::

## Configuration

`./testnet.yaml`

```yaml
environment:
  - MSSQL_DATABASE_USER=  # MSSQL username
  - MSSQL_DATABASE_PASSWORD=  # MSSQL password
  - MSSQL_DATABASE_DBNAME= # MSSQL Database
  - MSSQL_DATABASE_SERVER= # MSSQL Server URL
  - MSSQL_DATABASE_PORT= # MSSQL Server Port
  - MSSQL_DATABASE_TABLE= # MSSQL Table Name
  - MONGO_URL=mongodb+srv://<USERNAME>:<PASSWORD>@<MONGODB URL>/<DATABASE>  # MONGODB INFO (It is recommended to use Mongo Atlas)
volumes:
  - ./config/wellinfo_testnet.js:/app/config/wellinfo.js

```

`./config/wellinfo_testnet.js`

```javascript

module.exports = {
    uniqueId: {
        "locationID-Here": ['OILuniqueId-Here', 'GASuniqueId-Here'],
    }
};
```

## How to use

```shell

docker-compose -f testnet.yaml up -d

```

## How to build

```shell

docker build -t treasurenet/productiondataprocess:1.0 .

```

## How to change frequency

`./index.js`

```javascript
const rule = new schedule.RecurrenceRule();
rule.hour = [0, 12];
```

## Want to upload production volume for a specific date

`./index.js`

```javascript

const startDate = moment('2023-08-01');  //START Date
const endDate = moment('2023-10-05');  //END Date
```


## Data Schema that can be accepted by the ProdctionDataUploader automatic tool

```javascript

const RecordSchema = new mongoose.Schema({
    location_id: {type: Number, index: true, required: true},
    date: {type: Number, index: true, required: true},
    amount: Number,
    month: Number,
    uniqueId: {type: String, required: true},
    status: {type: Number, default: dict.STATUS.UNUSED},  // 0:available 1：taken
    timestamp: {type: Date, default: Date.now},
});

const OilDataRecordModel = mongoose.model('ProductionDataRecord_OIL', RecordSchema);
const GasDataRecordModel = mongoose.model('ProductionDataRecord_GAS', RecordSchema);

```


# Production Data Uploader

:::info
Because development has not yet been completely completed, the project remains private for the time being and will be made public soon.
:::

[GitHub](https://github.com/treasurenetprotocol/treasurenet-tnservices-productiondata-uploader)

## Configuration

`./testnet.yaml`

```yaml

environment:
      - MONGO_URL=mongodb+srv://<USERNAME>:<PASSWORD>@<MONGODB URL>/<DATABASE>  # MONGODB INFO (It is recommended to use Mongo Atlas)
      - EVM_ENDPOINT=https://node0.testnet.treasurenet.io  # Treasurenet Node Endpoint
      - UPLOADER=  # Uploader EVM Address
      - SENDER=  # Uploader EVM Private Key
      - TNGATEWAY_ACCESS_TOKEN_URL=https://tngateway.testnet.treasurenet.io/oauth/access_token  # TNGATEWAY Get Access Token URL 
      - TNGATEWAY_CLIENT_ID= # TNGATEWAY Oauth2.0 Client ID
      - TNGATEWAY_CLIENT_SECRET= # TNGATEWAY Oauth2.0 Client Secret
      - TNGATEWAY_SCOPE= # TNGATEWAY Oauth2.0 Scope
      - TNGATEWAY_API_URL=https://tngateway.testnet.treasurenet.io/api # TNGATEWAY API URL

```

## How to use

```shell

docker-compose -f testnet.yaml up -d

```
## How to build 

```shell

docker build -t treasurenet/productiondatauploader:1.0 .

```

## How to change frequency

`./index.js`

```javascript
const rule = new schedule.RecurrenceRule();
rule.hour = [0, 12];
```

## Want to upload production volume for a specific date

`./index.js`

```javascript

const startDate = '2023-01-01'  //Start Date
for (let i = 0; i < 90; i++) {  //times
    ...
}

```

# Production Data Manual

:::info
Manual uploading requires basic blockchain knowledge, as well as certain code development, usage, and interface review capabilities.

More demos are being added continuously
:::

### NodeJS(Javascript)

#### Set Production Data

```javascript
const web3 = new Web3(new Web3.providers.HttpProvider('https://node0.testnet.treasurenet.io'));

const sender_prvi_key = 'YOUR PRIVATE KEY';
// TESTNET
const contractAddress = '0xFe6810EDE16180686ee083A1B246A4182501E597' //GasData:0xe501CD75BA83798ECB408900034FF9BAC4926d5E
// Specify the relative path of the corresponding abi.json file
const abiJSON = JSON.parse(fs.readFileSync(path.join(__dirname, "./", `OilData.json`)));
// Params
const params = [
  "0x4872484e4579694e575a65745956524879303873680000000000000000000000",  // RequestID
  [
    "0x4872484e4579694e575a65745956524879303873680000000000000000000000",  // RequestID
    0,  // Keep Zero
    "0x0C6621BE4Bcd1Fff25d337D149246130cFc2B4d8",  //Owner Address
    1000,  //Asset Value
    0,   // Keep Zero   
    230101, //Date (Format YYMMDD)
    2301,   //Month (Format YYMM)
    0   // Keep Zero
  ]
];
// gas
const gas = 5000000;

const account = web3.eth.accounts.privateKeyToAccount(sender)
const contract = new web3.eth.Contract(abiJSON, contractAddress);
contract.handleRevert = true;

const tx_builder = await contract.methods.setProductionData(...params);
const encoded_tx = tx_builder.encodeABI();
const transactionObject = {
  gas,
  data: encoded_tx,
  from: account.address,
  to: contractAddress
};

try {
  const signTx = await web3.eth.accounts.signTransaction(transactionObject, account.privateKey);
  const result = await web3.eth.sendSignedTransaction(signTx.rawTransaction);
  console.log(`blockHash:${result.blockHash},blockNumber:${result.blockNumber}`);
} catch (e) {
  console.error(e);
}


```

The transaction can then be queried based on the printed blockHash or blockNumber in [evmexplorer.testnet.treasurenet.io](https://evmexplorer.testnet.treasurenet.io)

#### Possible ERRORs

|Explorer Error Message|Description|Processing|
|--|--|--|
|must be the producer|The account sending the transaction does not match the account address of the approved oil well holder|Check whether `sender_prvi_key` is consistent with the account number approved by the ProducerProtal oil well|
|zero production month|The month field cannot be 0|Check whether the `month` position in the parameter array is in the correct month format|
|month format is not YYMM|The month field format is incorrect|Check whether the `month` position in the parameter array is in the correct month format|
|zero production date|The date field cannot be 0|Check whether the `date` position in the parameter array is in the correct date format|
|date format is not YYMMDD|The date field format is incorrect|Check whether the `date` position in the parameter array is the correct date format|
|zero production amount|The production field cannot be 0|Check whether the `Asset Value` position in the parameter array is the correct production data|


 

