# Abeg Payment.
Patronize/Abeg helps business owners to collect payments, manage their sales and reach more customers via Abeg app in Nigeria.
Bamboo allows its users to transfer money from Abeg to their wallets through Abeg API.
Users who have transferred their money from their Abeg account to their bamboo wallet account can be seen from the Bamboo account dashboard in Abeg: https://dashboard.patronize.co/dashboard

## Communication between Bamboo and Abeg
We have set Abeg configuration details (secret_key, api_key and base_url) in the config file.
These details initiate the communication between bamboo and Abeg during transactions. 
When a user transfers money from Abeg to their Bamboo wallet account,  this event triggers a HTTP POST payload that is sent to bamboo backend in real time via the webhook url: https://api.investbamboo.com/bamboo/webhook .
The received webhook allows bamboo to set up integration. Webhook events payload are formatted in JSON. 

```
%{
  "actual_amount" => 500000,
  "amount" => 500000,
  "customer" => %{
    "avatar" => nil,
    "bvn" => "xxxxxxxxxx",
    "email" => example@email.com,
    "frequency" => 1,
    "name" => "User name",
    "phone" => "xxxxxxxxxx",
    "tag" => "abcd",
    "uuid" => "cccccccccccccccccccc"
  },
  "description" => "test",
  "fees" => 2500,
  "meta" => %{"abeg_tag" => "abcd", "name" => "User name"},
  "paypoint" => false,
  "settled_amount" => 497500,
  "type" => "abeg-transfer.success",
  "uuid" => "cccccccccccccccccccc"
}
```


Bamboo receives this webhook at `apps/web/lib/web/controllers/external_payments/transaction_controller.ex` module. Here , this webhook is clean to sync with the params required to create a deposit into the bamboo wallet.
We use Payment.Deposit.Tenants.Command module to create the new deposit payload from the received Abeg webhook : 


 ```
CreateCommand.new(%{
     amount_paid: convert_to_naira(params["settled_amount"]),
     currency: "NGN",
     payload: params,
     phone_number: params["customer"]["phone"],
     provider: "abeg",
     reference: params["uuid"]
   })
   ```

Creating a deposit in Bamboo wallet
We pass the newly created payload to Payment.create_deposit_from_tenant/1 function. Here:
We fetch the bamboo as the api consumer.
Authorize the user.
Fetch buying exchange rate for Naira currency.
Build deposit payload.
Create a deposit.
The deposit payload:

```
%{
     Api_consumer_id: integer,
     amount_paid: decimal,
     currency: string,
     Destination_wallet_id: integer,
     dollar_amount: decimal,
     dollar_instant_deposit_fee: float,
     dollar_processing_fee: float,
     deposit_type: string,
     exchange_rate: float,
     Dollar_fee: float,
     payment_channel_id: integer,
     provider: string,
     reference: string,
     user_id: integer
   }
```


Verifying created Deposit in Bamboo
After the deposit has been created, we verify it using its reference using the Payment.Deposit.Verify.call/1. The required parameter is : 


`%VerifyCommand{reference: deposit.reference}`


 During verification we fetch deposit status from AbegClient.Payment.Fetchstatus module and handle the deposit status:
status: "accepted" -> Send email to user that deposit has been accepted and make user 
			  as funded
status: "rejected" -> change status to failed
status: "deleted" ->  change status to deleted 
status: "Pending" -> Leave the status as pending.
  
We then update the verified deposit amount.





