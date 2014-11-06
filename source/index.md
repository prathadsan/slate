---
title: API Reference

language_tabs:
  - json
  - curl

includes:
  - errors

search: true
---

# Subscription Notification

The Subscription notification is used to notify you of events relevant to a subscription. The notification includes the following information:

- Customer details, including contact data
- Subscription details, including subscription, plan, and payment data

## Event Types 

There are three main event types that trigger a Subscription notification:

| Event Type  | Description |
| ---------- | ------------ |
| Subscription Created  | Notifies you when a customer has subscribed to a plan and successfully paid for it. You can use the notification to know when to provide the customer with access to the subscribed-to plan. |
| Subscription Updated  | Notifies you when an existing subscription has been updated and gives a reason for the update. You can use the notification to stay informed about changes to the subscription. |
| Subscription Reminder  | Notifies you about upcoming subscription events, such as a manual renewal. You can use the notification to know when an action needs to be taken for the subscription. |

## How it Works 

The following describes the notification process:

1.	An event happens, such as a customer signing up and paying for a subscription for one of your plans.
2.	The details of the event are saved in the cleverbridge database.
3.	cleverbridge sends you the notification in JSON format as an HTTP PUT request.
4.	If the call was successful, an HTTP 2XX response code is returned to cleverbridge.
5.	If the call failed, an HTTP 5XX response code is returned to cleverbridge. cleverbridge sends follow-up calls until the call is successful.

## Notification Description 

The notification contains the following information.

### Notification 

| Data  | Type  |  Description  | Required?  |
| ------------ |--------------- |
|  notification_id     | String |  Unique identifier of the notification. | Required |
|  event   | Container |  See Event below. | Required |
|  customer      | Container | See Customer below. |  Required |
|  subscription    | Container | See Subscription below. | Required |

### Event 

| Data  | Type  |  Description  | Required?  |  
| ------------ |--------------- | 
|  type    | String | Type of event. Possible values are `subscription_created`, `subscription_updated`, or `subscription_reminder`.  | Required
|  reason  | String |  Reason for the event. Possible values are `canceled`, `manual_renewal_confirmed`, `manual_renewal_required`, `renewal_type_changed`,  `renewed`, or `upgraded`.  | Required 

### Customer 

| Data  | Type | Description | Required? |  
| ---------- | ---- |------------ |
| ``customer_id``  | String | Unique identifier of the customer.  | Required
| ``client_customer_id``  | String | Your internal identifier of the customer.  | Optional
| ``default_contact``  | Container |  See Default Contact below. | Required

#### Default Contact 

| Data  | Type | Description | Required? |  
| ---------- | ---- |------------ |
| ``locale``  | String | Two letter ISO 639-1 code for the customer language. Format: `iso6391`  | Required
| ``title``  | String | Title of the customer.  | Optional
| ``first_name``  | String |  First name of the customer. | Optional
| ``last_name``  | String | Last name of the customer.  | Required
| ``gender``  | String | Gender of the customer. Possible values are `female` or `male`.  | Optional
| ``company``  | String | Company of the customer.  | Optional
| ``street``  | String |  Street address of the customer. | Optional
| ``zip_code``  | String | Zip code of the customer.  | Optional
| ``city``  | String | City of the customer.  | Optional
| ``state_iso_code``  | String | ISO 3166-2 code for the state of the customer. Format: `iso31662`  | Optional
| ``country_iso_code``  | String | ISO 3166-2 code for the country of the customer. Format: `iso31662`   | Required
| ``phone``  | String | Phone number of the customer.  | Optional
| ``mobile``  | String | Mobile number of the customer.  | Optional
| ``fax``  | String | Fax number of the customer.  | Optional
| ``email``  | String | Email address of the customer.  | Required
| ``vat_id``  | String | VAT ID of the customer.  | Optional

### Subscription 

| Data  | Type | Description | Required? |  
| ---------- | ---- |------------ |
| ``subscription_id``  | String | Unique identifier of the subscription.  | Required
| ``client_subscription_id``  | String | Your internal identifier of the subscription. | Optional
| ``renewal_type``  | String | If `auto`, subscription renews automatically. If `manual`, the customer must initiate the renewal. If `cancel`, the subscription will not continue after the renewal date.  | Required
| ``status``  | String | Status of the subscription. Possible values are: `active`, `canceled`, `expired`, `suspended`, or `deleted`.  | Required
| ``started_at``  | Date-Time |  ISO 8601 code for the date and time the subscription became effective. Format: `iso8601` | Required
| ``next_billing_at``  | Date-Time |  ISO 8601 code for the date and time of the next billing event. Format: `iso8601` | Optional
| ``next_renewal_at``  | Date-Time |  ISO 8601 code for the date and time of the next renewal of the subscription contract. Format: `iso8601` | Optional
| ``canceled_at``  | Date-Time |  ISO 8601 code for the date and time the subscription was canceled. Format: `iso8601` | Optional
| ``ended_at``  | Date-Time |  ISO 8601 code for the date and time the subscription ended, if the subscription was set up as a term subscription. Format: `iso8601` | Optional
| ``currency_iso_code``  | String | Alpha 2 ISO 4217 code for the currency of the subscription. Format: `iso4217` | Required
| ``plans``  | Container | Includes one or more plans. See Plan below.  | Required
| ``payments``  | Container | Includes one or more payment logs. See Payment below. | Required

#### Plan 

| Data  | Type | Description | Required? |
| ---------- | ---- |------------ |
| ``plan_id``  | String | Unique identifier of the plan.  | Required
| ``client_plan_id``  | String | Your internal identifier of the plan.  | Optional
| ``plan_version``  | Integer | Version number of the plan.  | Required
| ``name``  | String | Name of the plan.  | Optional
| ``billing_cycle_frequency``  | String | Period of time between recurring billing events, such as daily, monthly, quarterly, or annually. Example: `3m4d`.  | Optional
| ``charges``  | Container | Includes one or more charges. See Charge below.  | Optional
| ``quantity``  | String | Number of times this plan is in the subscription.  | Required
| ``coupon_code``  | String | Identifier of a coupon associated with a promotional discount offer. | Optional
| ``custom_data``  | String | A set of key/value pairs that you can use for storing additional information about the subscription in a structured format.  | Optional

##### Charge 

| Data  | Type | Description | Required? |
| ---------- | ---- |------------ |
| ``name``  | String | Name of the charge.  | Optional
| ``price``  | Number | Price of the charge.  | Optional

#### Payment 

| Data  | Type | Description | Required? |
| ---------- | ---- |------------ |
| ``created_at``  | Date-Time |  ISO 8601 code for the date and time when payment was received. Format: `iso8601` | Required
| ``payment_provider_transaction_id``  | String | Unique identifier of the payment transaction from the payment provider.  | Optional
| ``payment_method``  | String | How the customer paid, such as by wire transfer, PayPal, MasterCard, etc. | Required
| ``card_last_four_digits``  | String | Last four digits of the credit card. | Optional
| ``card_expiration_date``  | String | Expiration date of the credit card. | Optional
| ``currency_iso_code``  | String | Alpha 2 ISO 4217 code for the currency of the payment. Format: `iso4217`  | Required
| ``net``  | Number | Net amount of the payment.  | Required
| ``tax``  | Number | Amount of any applicable taxes.  | Required
| ``gross``  | Number | Gross amount of the payment.  | Required

```json

     {
        "notification_id": "1251A1",
        "event": {
            "type": "subscription_created",
        },
         "customer": {
	         "customer_id": "1251A1",
	         "client_customer_id": "ABC123",
	         "default_contact": {
	             "locale": "de-DE",
	             "first_name": "Max",
	             "last_name": "Samplemann",
	             "street": "Anystreet 123",
	             "zip_code": "12345",
	             "city": "Anycity",
	             "country_iso_code": "DE",
	             "email": "example@examplecompany.com"
			}
         },
         "subscription": {
             "subscription_id": "1251A1",
             "renewal_type": "manual",
             "status": "active",
             "started_at": "2014-09-02T14:44:43.314789Z",
             "next_billing_at": "2015-09-02T14:44:43.610217Z",
             "next_renewal_at": "2015-09-02T14:44:43.610217Z",
             "currency_iso_code": "EUR",
             "plans": [
                 {
                     "plan_id": "1251A1",
                     "plan_version": 0,
                     "name": "Keepster Professional",
                     "billing_cycle_frequency": "1y",
                     "charges": [
                         {
                             "name": "Yearly",
                             "price": 199
                         }
                     ],
                     "quantity": 1,
                     "custom_data": {
                         "license_key": "123456ABC"
                     }
                 }
             ],
         "payment_log": [
             {
                 "created_at": "2014-09-02T14:44:43.314789Z",
                 "payment_provider_transaction_id": "123456789",
                 "payment_method": "mastercard",
                 "card_last_four_digits": "1234",
                 "card_expiration_date": "1/2016",
                 "currency_iso_code": "EUR",
                 "net": 167.23,
                 "tax": 31.77,
                 "gross": 199
             }
         ]
     }

```

# Upgrade a Subscription  

A subscription upgrade happens when a customer chooses to replace an existing plan with a better plan. The access to the new plan is available to the customer immediately. The rebilling for the new plan, however, takes place at the next billing event.

## Request 

    PUT https://api-subscription.cleverbridge.com/v1/subscriptions/{subscription_id}

The following parameters are required:

| Parameter  | Type | Description |
| ---------- | ---- |------------ |
| ``action``  | String | The `upgrade` value replaces the initial plan with the new plan.   |
| ``plan_id``  | String | Unique identifier of the plan. |

```curl
     curl PUT https://api-subscription.cleverbridge.com/v1/subscriptions/1251A1 \
      -H "Authorization: Bearer [token]" \
      -H "Accept: application/json" \
      -d '{ "action": "upgrade" }'
      -d '{ "plan_id": "1251A1" }'
```

> Response: If the upgrade was successful, a 200 response is returned. A Subscription notification is sent with an event type of `subscription_updated` and the reason for the update as `upgraded`. 















