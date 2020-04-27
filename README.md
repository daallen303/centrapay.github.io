# Centrapay Documentation

## Overview

### Introduction

Welcome to Centrapay! Payments through Centrapay are accomplished via a two-step process, involving both the merchant and consumer.  

The API is designed to support payment terminals, point of sale, shopping cart, vending and unattended transactions.

It enables consumers to pay with a range of digital assets, including bitcoin and vouchers. The merchant can initiate payment from a consumer or the consumer can push a payment to the merchant. It will also be possible to activate our services in the payment flow such as carbon offsetting.

### Payment Flow

#### Payment flow using Pocket Vouchers

```
requests.create > Pocket Voucher Code Entry screen on terminal > User enters Pocket Voucher Code into terminal > requests.pay > terminal Loading >Webhook Notification > terminal display result.
```

#### Payment flow using QR Code

```
requests.create > Generate QR Code on terminal > User selects payment type on phone > Phone Scans > requests.pay > Authenticated and Processed via Phone > Webhook Notification > terminal display result.
You will need to create a request ‘/requests.create’ first and use the requestId for making a payment ‘/requests.pay’.
```

### Glossary

### API keys and access

To gain access to the payments API, please create an account in our Portal. Once you’ve completed the sign up process and acknowledged our terms, we will provide a merchant_id, secret, and api_key via the Portal.

To generate test credentials, click here.


### API protocols

In order to be authorized to use any of the payments API endpoints, you will need to send your api_key in the header of your request.


### API hosts

service.centrapay.com/payments/api

### Endpoints

|             |                                                                                                   |
|-------------|---------------------------------------------------------------------------------------------------|
| Requests    | /requests.cancel <br> /requests.create <br> /requests.info <br> /requests.pay <br> /requests.void |
| Service     | /service.info                                                                                     |
| Transaction | /transactions.processSettlements <br> /transactions.refund                                        |

### QR code payments

#### Merchant Push

At a high level, a merchant makes a **POST** request to `/requests.create` and optionally sets notifyURL to the URL that will receive webhook notifications when the request status is updated. 

A response will be sent to the merchant with a payment request and accompanying QR code (as a base64 encoded string) that encodes the requestId, which can then be presented to consumers as the merchant sees fit.

 A consumer then is then presented with the ID or QR code, which they then scan to initiate payment. If the payment is successful, a notification is sent to the webhook (if provided), confirming with the merchant that the consumer has completed the payment request.

#### Merchant Pull

Where the merchant does not have a screen or payment terminal to display the QR code, this method works well by allowing merchants to simply scan a 2D barcode which is presented on the consumers device to accept payment.

Steps: 
1. Generate barcode in Sylo 
2. Create a request in requests.create, using fields: 
3. Amount 
4. Asset 
5. MerchantID 
6. PatronCode (Enter barcode) 

>Once you have created the request, the apk should display payment option/ confirmation. 

Example:

An example URL that a **POST** request could be sent to would be the following:

``` 
https://service.centrapay.com/payments/api/requests.create?amount=100&asset=NZD&merchantId=1399b053-b3dd-4c5b-9859-b5bf5c2ac477&patronCode=2368720114399939″-H“accept:application/json”-H“x-api-key:1399b053-b3dd-4c5b-9859-b5bf5c2ac477
```

#### Non integrated

Where the merchant is unable to integrate electronically, Centrapay can issue the merchant with a unique QR code which can be used at the point of sale to engage consumers and receive payment.


## Im not sure about this section

### [Creating a payment request](https://service.centrapay.com/payments/api/documentation#/Requests/postRequestscreate)


A payment request can be created by sending a **POST** request to the following endpoint:<br>
https://service.centrapay.com/payments/api/requests.create

The following parameters **must** be provided with the request:

| Parameter  | Description                                 |
|------------|---------------------------------------------|
| amount     | The payment amount in cents                 |
| asset      | The currency - NZD or AUD                   |
| merchantId | The ID of the merchant creating the request |

<br>

The following parameters can **optionally** be provided with the request:

| Parameter         | Description                                                  |
|-------------------|--------------------------------------------------------------|
| clientId          | The ID of the merchant specific client configuration         |
| description       | Description of the payment                                   |
| externalReference | Unique merchant reference for the payment request            |
| notifyUrl         | The URL that will receive **POST** requests from the webhook |

An example URL that a **POST** request could be sent to would be the following:

```
https://service.centrapay.com/payments/api/requests.create?amount=100&asset=NZD&description=Test%20purchase&merchantId=1399b053-b3dd-4c5b-9859-b5bf5c2ac477&notifyUrl=https%3A%2F%2Fexample.com%2Fservice.notify
```

<br>

### [Paying a payment request - Pocket Voucher](https://service.centrapay.com/payments/api/documentation#/requests/postRequestspay)

#### Step 1. Test Voucher Allocation

To generate a test value voucher text ‘CENTRALBONUS’ 393. <br>
You will then receive a response text containing an 8 digit voucher code that has $20 of loaded credit. 

>Note:  
The received code is only valid for two weeks from the issue date.<br>
You might get charged your standard text rates from your provider.


#### Step 2. Redeem Test Voucher to Pay Request

Voucher redemption is accomplished via sending a **POST** request to the following endpoint:<br>
https://service.centrapay.com/payments/api/requests.pay

The following parameters **must** be provided with the request query:

| Parameter     | Description                                                               |
|---------------|---------------------------------------------------------------------------|
| authorization | The voucher code                                                          |
| ledger        | The ledger to use for payment in this case `g.pocketvouchers.pv`          |
| requestId     | The payment requestId that is generated when `/requests.create` is called |

An example URL that a **POST** request could be sent to would be the following:

```
https://service.centrapay.com/payments/api/requests.pay?amount=100&authorization=09626714&ledger=g.pocketvouchers.pv
```

<br>

### [Paying a payment request - QR code](https://service.centrapay.com/payments/api/documentation#/requests/postRequestspay)

Generate the QR code returned in `/requests.create` receipt and scan using APK provided.

```
 “qrCode”: “cp_f551e0d9-5d50-47b6-bb77-faba277a6042", 
 “requestCode”: “9VHg2V1QR7a7d/q6J3pgQg==“, 
 “status”: “new”, 
 “createdAt”: “2020-02-26T00:13:56.071Z”, 
 “updatedAt”: “2020-02-26T00:13:56.071Z” 
}
```

<br>

### [Getting the information about a payment request](https://service.centrapay.com/payments/api/documentation#/Requests/getRequestsinfo)

A payment request can be created by sending a **GET** request to the following endpoint:<br>
https://service.centrapay.com/payments/api/requests.info

The following parameters **must** be provided with the request query:

| Parameter | Description                                                               |
|-----------|---------------------------------------------------------------------------|
| requestId | The payment requestId that is generated when `/requests.create` is called |

>Entering the requestId in `/requests.info` will return information about the request and its status.

<br>

### [Cancelling a payment request](https://service.centrapay.com/payments/api/documentation#/Requests/postRequestscancel)

A payment request can be created by sending a **POST** request to the following endpoint:<br>
https://service.centrapay.com/payments/api/requests.cancel 

The following parameters **must** be provided with the request query:

| Parameter | Description                                                               |
|-----------|---------------------------------------------------------------------------|
| requestId | The payment requestId that is generated when `/requests.create` is called |
>Entering the requestId in `/requests.cancel` will allow you to cancel a request.
You are unable to cancel a request where its status has been set to paid.

<br>

### [Voiding a payment request](https://service.centrapay.com/payments/api/documentation#/Requests/postRequestsvoid)

A payment request can be created by sending a **POST** request to the following endpoint:<br>
https://service.centrapay.com/payments/api/requests.void

The following parameters **must** be provided with the request query:

| Parameter | Description                                                                                                                                                                                                                                                             |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| requestId | The payment requestId that is generated when `/requests.create` is called. Entering the requestId in `/requests.info` will allow you to determine what state the request is in and cancel the request if it hasn’t been paid or refund the request if it has been paid. |

<br>

### [Refunding a transaction](https://service.centrapay.com/payments/api/documentation#/Transactions/postTransactionsrefund)

Refunds can be processed for a transaction by sending a **POST** request to the following endpoint:   

https://service.centrapay.com/payments/api/transactions.refund

Refunding a transaction can be done two ways:

- Refund the full or partial amount once
- Refund a partial amount multiple times up to the transaction amount

The following parameters **must** be provided with the request query to do a single refund of a transaction:

| Parameter     | Description                                                                                                                                                                                                                                                |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| transactionId | The transaction to refund you can either get this by setting notifyUrl when the request is created and receiving a webhook notification with the transaction details, or you can call the `/requests.info` endpoint and grab the transactionId from there. |
| amount        | The amount to refund                                                                                                                                                                                                                                       |

If you wish to refund a transaction multiple times you must also provide:
|                   |                                                                                                                                               |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| externalReference | A reference supplied by the merchant that must be unique for each refund of that transaction, can be anything you want but it must be unique. |

An example request **POST** request could look like this:

```
https://service.centrapay.com/payments/api/transactions.refund?payamount=100&transactionId=619f6088-722e-4851-a093-50e64071ed3d&externalReference=AE478RD
```

---

## Errors

### Error codes

| Error code | Http code | Message                             |
|------------|-----------|-------------------------------------|
| 1          | 401       | KEY_NOT_AUTHORIZED                  |
| 2          | 404       | REQUEST_NOT_FOUND                   |
| 3          | 404       | TRANSACTION_NOT_FOUND               |
| 4          | 404       | MERCHANT_NOT_FOUND                  |
| 5          | 400       | INVALID_REQUEST_ID                  |
| 6          | 400       | INVALID_AMOUNT                      |
| 7          | 400       | INVALID_ASSET                       |
| 8          | 400       | INVALID_AUTHORIZATION               |
| 9          | 400       | INVALID_LEDGER                      |
| 10         | 400       | INVALID_MERCHANT_ID                 |
| 11         | 400       | INVALID_CLIENT_ID                   |
| 12         | 400       | INVALID_PATRON_CODE                 |
| 13         | 400       | INVALID_DESCRIPTION                 |
| 14         | 400       | INVALID_REFERENCE                   |
| 15         | 400       | INVALID_NOTIFY_URL                  |
| 16         | 400       | INVALID_TRANSACTION_ID              |
| 17         | 400       | REQUEST_CANCELLED                   |
| 18         | 400       | REQUEST_EXPIRED                     |
| 19         | 400       | REQUEST_PAID                        |
| 51         | 500       | INTERNAL_ERROR                      |
| 76         | 503       | EXTERNAL_SERVICE                    |
| 77         | 500       | INTERNAL_ERROR                      |
| 126        | 403       | IN_USE                              |
| 151        | 403       | IN_USE                              |
| 176        | 400       | LEDGER_NOT_ENABLED                  |
| 177        | 400       | INVALID_LEDGER                      |
| 178        | 500       | INTERNAL_SERVER_ERROR               |
| 179        | 404       | BITCOIN_TRANSACTION_NOT_FOUND       |
| 180        | 400       | OLD_TRANSACTION                     |
| 181        | 400       | INSUFFICIENT_PAYMENT                |
| 182        | 403       | MERCHANT_TRANSACTION_LIMIT_EXCEEDED |
| 183        | 403       | INVALID_TRANSACTION_AMOUNT          |
| 184        | 403       | INVALID_VOUCHER_AMOUNT              |
| 185        | 403       | VOUCHER_EXPIRED                     |
| 186        | 403       | INSUFFICIENT_VOUCHER_BALANCE        |
| 187        | 404       | VOUCHER_UNKNOWN                     |
| 276        | 400       | ALREADY_REFUNDED                    |
| 277        | 400       | INVALID_AMOUNT                      |

---

## Webhooks

### Successful webhook notification

```json
{ 
    "iss": "b4d5d7a3-38bf-4c41-8e38-e33d96ddb169", 
    "iat": 1538440151, 
    "jti": "fff41104-8a22-493a-a9d2-f6d94e7b901e", 
    "transaction": { 
        "transactionId": "aba4b07d-fd12-43bc-bbb1-12fda46d9937", 
        "transactionType": "PURCHASE", 
        "ledger": "g.crypto.bitcoin.mainnet", 
        "state": "completed", 
        "amount": 2000, 
        "request": { 
        "requestId": "1b23d1f9-8a3e-414d-ac94-f4b331095197", 
        "merchantId": "0b1100be-6a76-45b0-adb8-bfe7db5ae720", 
        "externalReference": "12345sixseveneightnineten", 
        "denomination": { "asset": "NZD", "amount": 2000 } 
        }, 
        "createdAt": "2018-10-02T00:29:09.307Z", 
        "updatedAt": "2018-10-02T00:29:11.383Z", 
        "type": "BITCOIN", 
        "timestamp": "2018-10-02T00:29:08.761Z", 
        "account": "1Fy8xwtT8csbVwVCw2NMkpTjXx7AURY7fp", 
        "responseCode": "00", 
        "settlementDate": "2018-10-02T00:29:08.760Z", 
        "receipt": "", 
        "authCode": "961241" 
    } 
} 
```

### Failure webhook notification

```json
{
    "iss": "b4d5d7a3-38bf-4c41-8e38-e33d96ddb169",
    "iat": 1585003238,
    "jti": "44c32390-aa1f-400b-b01a-b1bc58339745",
    "transaction": {
        "transactionType": "CANCELLED",
        "request": {
            "requestId": "bec358bf-edb5-4633-a785-a95cc281d3b7",
            "merchantId": "c614d516-7fbe-4acc-8a49-ed1ce8c54e77",
            "clientId": "ac1cf8f3-3bbb-4286-beb5-70e7efe562c8",
            "denomination": {
                "asset": "NZD",
                "amount": "1"
            }
        }
    }
}
```

`TransactionType` can either be:

|           |                                       |
|-----------|---------------------------------------|
| CANCELLED | For a request that has been cancelled |
| EXPIRED   | For a request that has expired        |
| PURCHASE  | For a completed transaction           |
| REFUND    | For a refunded transaction            |


