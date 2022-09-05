# Collaterization API


## Introduction

This document is intended to help you integrate with our Collaterization API with ease

The API is intended to allow you to use digital assets as collateral from your users and in return,  offer them financial services* To make this happen, our API offers the following high level features:

* API Authorization (API keys)
* Webhooks
* Account Linking & unlinking
* 2-FA
* user KYC details
* Portfolio Balance
* Lock/Collateral (CRUD)
* Reminders & Notifications
* Liquidation

## API Authorization

To use this service, you will need to create an API consumer account but with an additional label of **credit_company**

After creating an account, the API consumer you will be taken to your dashboard where you can generate your **API key**

_An API key can be used to authenticate all requests coming from the API consumer, hence you must include it as a **Bearer token** in the authorization header of every request made to the Collaterization API_

After authorizing a request (via a plug) the API consumer ID is passed to the construct for easy identification of the API consumer*

## Webhooks

To receive webhook events as an API consumer, you are expected to provide a webhook url* The data sent from Bamboo to the API consumer’s webhook url is in the following format;
```
1.{
2.  "event": "event*account linking successful",
3.  "data": {
4.      "first_name": "Harry",
5.      "last_name": "Potter",
6.      "email": "harry@email*com",
7.      "phone_number": "+2347085295313",
8.      "collateralized_user_id": 1    
9.  },
10.  message: "success", #can be success or failure
11.  status: 200
12.}
```