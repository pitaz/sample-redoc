This is the beginning of any interaction that would happen between Bamboo and the API consumer with a credit-company type on behalf of a user* To create a link for a user between Bamboo and the API consumer, a request has to made to Bamboo from the API consumer to get the url to a linking page*  The API consumer gives a redirect_url*

The linking page is basically a web page/view displayed to the user to collect their bamboo login details (phone_number and password + email used on the API consumer platform)* Upon submission of the details, Bamboo checks whether the user is an existing bamboo user; if not, an error is displayed to the user on the page* If the user exists, Bamboo goes further to check if the user has been linked already to the API consumer; if the user has been linked, a response is sent to the user making them aware that they’ve been linked already (a webhook event should be sent also)* If the user hasn’t been linked, then linking happens at this stage*

There’s a table that represents collateralized/linked users (we call this the  collateralized_users table / collateralized_linked_accounts) This table contains the following fields:

* `user_id: integer`
* `collateralized_linked_account_id: integer`
* `api_consumer_id: integer`
* `linked: boolean (true/false)`

Linking is basically adding the user to this table and adding the appropriate api_consumer_id to the list of api_consumer_ids

Once linking is done, a webhook event is sent to the API consumer’s webhook to inform of the successful account linking* The event sent is written below:
```
1.{
2.  "event": "event*account linking successful",
3.  "data": {
4.      "first_name": "Harry",
5.      "last_name": "Potter",
6.      "email": "harry@email*com",
7.      "phone_number": "+2347085295313",
8.      "collateralized_linked_account_id": 1    
9.  },
10.  message: "success", #can be success or failure
11.  status: 200
12.}
```

Unlinking an account is basically marking the entity as unlinked (collateralized_linked_account table)* But before an unlinking can occur, we must ensure that the user doesn’t have any active money locked as collateral*

**In summary**, the sequence of API calls is as follows:
   * API consumer calls **GET base_url/collateralized-linked-account/link**, this returns a page specifically for the api_consumer
   * API consumer displays this page; user inputs fields and clicks submit 
   * Submit triggers **POST base_url/collateralized-linked-account/authenticate** (request parameters are api_consumer_id, phone_number, password, email)
   * Once successfully linked, a webhook event is triggered to the API consumer’s webhook url*
   * To unlink a user, the API consumer must make a request to **POST base_url/   collateralized-linked-account/{collateralized--linked-account-id}/unlink**
