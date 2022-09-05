# Introduction


Hi there,

Welcome to Powered by Bamboo!

In line with our focus of democratizing access to the global capital markets for Africans, we decided to make our APIs available to developers and partner institutions.

Here, you will find all the help you need to get started with our Bamboo API set.


# Getting started


## API Reference

The sandbox server, for test purposes, is available under the address:

```
https://powered-by-bamboo-sandbox.investbamboo.com/
```

It's an exact copy of the production environment with a separate database. It can be used for any kind of testing without violating data consistency in a production environment.

## Authorization

In order to send requests to the sandbox server, the client would need to authorize it.

To achieve this, every request needs to contain an `x-client-token` header containing a your authorization token.

This token can be fetched after logging into your account using the endpoint:

```
POST /oauth/token
```

This call requires an app key provided in the header under a key `app-key`. The body should consist of your account username and password.

```
# Sample request

curl --location --request POST 'https://powered-by-bamboo-sandbox.investbamboo.com/oauth/token' \
--header 'app-key: APP_KEY' \
--header 'accept: application/json' \
--header 'content-type: application/json' \
--data-raw '{
  "password": "password",
  "username": "login"
}'
```

The call will return a token, that needs to be provided under an `x-client-token` key in every other request's header.

```json
# Response

{
    "access_token": "token",
    "expires_in": 86400
}
```