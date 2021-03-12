---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  # - shell
  # - ruby
  # - python
  # - javascript

toc_footers:
  # - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the Carbon Insights Documentation. This is a guide for usage of the Carbon Insights API.

If you have questions or need support, please contact <tech@carboninsights.co>.

# Schema

All API access is over HTTPS, and accessed from `https://api.carboninsights.co`. All data is sent and received as JSON. You must include the `Content-Type: application/json` header when sending your requests.

# Registration & Authentication

<!-- > To authorize, use this code: -->

```shell
curl "https://api.carboninsights.co"
  -H "Authorization: Token YOUR_ACCESS_TOKEN"
  -H "Content-Type: application/json"
```

carbIN uses `JSON Web Tokens` to control access to the API. In order to send and receive data, you must first [register for an account](#register-for-an-account).

After successfully registering, you can [request your authorization tokens](#request-authorization-tokens). You will receive two tokens. Save these, but don't get too attached to them. They expire quickly for security purposes.

Your `access token` is a short-lived token that is sent in the header of every request:

`Authorization: Token YOUR_ACCESS_TOKEN`

<aside class="notice">
You must replace <code>YOUR_ACCESS_TOKEN</code> with your personal access JWT you received upon registering for an account.
</aside>

Your `refresh token` is a longer-lived token that is used to [request a new access token]#(request-a-new-access-token) when your access token expires.

When your refresh token expires, you will use your account credentials to reauthenticate and [request a new set of tokens](#request-authorization-tokens).

# Request Ids

Each API request has an associated request id. You can find this value in the `Request-Id` response header. Referencing the request id is helpful when you need to contact us for support regarding a specific request.

# Endpoints

## Register for an account

> Sample Request:

```json
{
    "username": "ilovetheplanet",
    "password": "Sav3Th3Whal3s",
    "first_name": "jeff",
    "last_name": "whalen",
    "email": "jwhalen@bluebank.com"
}
```

> Sample Response:

```json
{
    "message": "Account with username ilovetheplanet created successfully. Use credentials to generate authorization token."
}
```

`POST https://api.carboninsights.co/register`

### Query Parameters
**Required**

Parameter | Type | Description
--------- | ------- | -----------
`username` | `string` |  The account username. Must be < 50 characters.
`password` | `string` | The account password. Must be distinct from username and > 7 characters.
`first_name` | `string` | The account holder first name. Must be < 50 characters.
`last_name` | `string` | The account holder last name. Must be < 50 characters.
`email` | `string` | The account email. Must be a valid email format.

## Request authorization tokens

> Sample Request:

```json
{
    "username": "ilovetheplanet",
    "password": "Sav3Th3Whal3s"
}
```

> Sample Response:

```json
{
    "access": <YOUR_ACCESS_TOKEN>,
    "refresh": <YOUR_REFRESH_TOKEN>
}
```

`POST https://api.carboninsights.co/token`

### Query Parameters
**Required**

Parameter | Type | Description
--------- | ------- | -----------
`username` | `string` |  The account username. Must match registered account username.
`password` | `string` | The account password. Must match registered account password.

## Request new access token

> Sample Request:

```json
{
    "refresh": <YOUR_REFRESH_TOKEN>
}
```

> Sample Response:

```json
{
    "access": <YOUR_NEW_ACCESS_TOKEN>
}
```

`POST https://api.carboninsights.co/token/refresh`

### Query Parameters
**Required**

Parameter | Type | Description
--------- | ------- | -----------
`refresh` | `string` |  Your refresh token received when initially requesting your authorization tokens.

## Calculate a carbon score

> Sample Request:

```json
{
	"user_profile": {
		"diet": "vegetarian",
		"VRE": 0,
		"natural_gas": false,
		"shared_account": false, 
        "zip_code": 19130
	},
	"options": {
		"offsets": false,
		"recommendations": true,
		"CO2e": true,
		"land": false,
		"water": false,
        "category_type": "category",
        "country": "United States"
	},
	"transactions": [
		{
			"date": "2020-03-01",
			"amount": 248.76,
			"category": "Air Travel", 
            "description": "Delta",
            "original_description": "Delta 866-576-1039 GA"
		},
				{
			"date": "2020-03-03",
			"amount": 73.88,
			"category": "Hotel", 
            "description": "Hilton",
            "original_description": "Hilton - WESTHEIM HOUSTON"
		},
				{
			"date": "2020-03-03",
			"amount": 11.23,
			"category": "Rental Car & Taxi", 
            "description": "Lyft",
            "original_description": "LYFT *RIDE SUN 12AM 855-865-9553 CA"
		}
	]
}
```

> Sample Response:

```json
{
    "messages": [],
    "details": {
        "footprint": {
            "category": {
                "0": "Air Travel",
                "1": "Hotel",
                "2": "Rental Car & Taxi"
            },
            "CO2e Emissions [tonne]": {
                "0": 0.22999763029727627,
                "1": 0.02183167856093383,
                "2": 0.04184753435490269
            }
        },
        "recommendations": {
            "category": {
                "0": "One Less Flight",
                "1": "Driving 10% less"
            },
            "CO2e Emissions [tonne]": {
                "0": 0.22999763029727627,
                "1": 0.004184753435490269
            }
        }
    }
}
```

<!-- ### HTTP Request -->

`POST https://api.carboninsights.co/calculateCarbonScore`

### Query Parameters for `transactions`
**Required**

Parameter | Type | Description
--------- | ------- | -----------
`date` | `date string` |  The transaction date. Format can be one of: <br>`YYYY-MM-DD`<br>`DD-MM-YYYY`
`amount` | `decimal` | The transaction amount in USD <br> _Note: negative amounts are treated as refunds and have negative emissions associated with them_
`category` | `string` | The transaction category
`description` | `string` | The transaction description
`original_description` | `string` | _Optional_ The original description of the transaction

<aside class="notice">
Negative purchases (refunds) will return negative numbers for environmental impacts.
</aside>

### Query Parameters for `user_profile`
_Optional_

Parameter | Default | Description
--------- | ------- | -----------
`diet` | `"typical"` | The user's diet. Can be one of: <br>`"typical"`<br>`"vegetarian"`<br>`"vegan"`
`VRE` |  `0` | The fraction of the user's eletricity that is green
`natural_gas` |  `false` | Whether the user uses natural gas for heating and cooking
`shared_account` |  `false` | Whether the user shares the account with at least one other person
`zip_code` |  `00000` | The user's zip code

### Query Parameters for `options`
_Optional_

Parameter | Default | Description
--------- | ------- | -----------
`offsets`      | `false` |  Whether to return an offset referral link
`recommendations`      | `false` | Whether to return user recommendedations
`CO2e`      | `true` | Whether to return user CO2 footprint in response
`land`      | `false` | Whether to return land use in response
`water`      | `false` | Whether to return water use in response
`category_type` | `"category"` | Whether to use a category or mcc code to categorize the transaction. Can be one of: <br>`"category"`<br>`"mcc"`
`country` | `"United States"` | Country used to determine economic and environmental data. Usually set to user's permanent residence.


<aside class="notice">
If you include the <code>user_profile</code> or <code>options</code> parent parameters, you are required to include all corresponding child parameters. Providing the data in <code>user_profile</code> improves the accuracy of the environmental footprint calculation. If it is not included, typical values for a US resident will be assumed.
</aside>

# Transaction Categories
Below is an exhaustive list of the transaction categories we currently support for computing emissions
## Recommended Categories
      |        |           |     
--------- | ------- | -----------
`Air Travel`<br>`Alcohol & Bars`<br>`Allowance`<br>`Amusement`<br>`Arts`<br>`ATM Fee`<br>`Baby Supplies`<br>`Babysitter & Daycare`<br>`Bank Fee`<br>`Bonus`<br>`Books & Supplies`<br>`Books`<br>`Cash & ATM`<br>`Charity`<br>`Check` <br>`Child Support`<br>`Clothing`<br>`Coffee Shops`<br>`Credit Card Payment`<br>`Dentist`<br>`Doctor`<br>`Electronics & Software`<br>`Eyecare`<br>`Fast Food`<br>`Federal Tax`<br>`Finance Charge` | `Financial Advisor`<br>`Furnishings`<br>`Gift`<br>`Groceries`<br>`Gym`<br>`Hair`<br>`Health Insurance`<br>`Hobbies`<br>`Home Improvement`<br>`Home Insurance`<br>`Home Services`<br>`Home Supplies`<br>`Hotel`<br>`Interest Income`<br>`Investments`<br>`Kids Activities`<br>`Late Fee`<br>`Laundry`<br>`Lawn & Garden`<br>`Life Insurance`<br>`Local Tax`<br>`Misc Expenses`<br>`Mortgage & Rent`<br>`Movies & DVDs`<br>`Music`<br>`Newspapers & Magazines` | `Paycheck`<br>`Pet Food & Supplies`<br>`Pet Grooming`<br>`Pharmacy`<br>`Printing`<br>`Property Tax`<br>`Reimbursement`<br>`Rental Car & Taxi`<br>`Rental Income`<br>`Renturned Purchase`<br>`Restaurants`<br>`Sales Tax`<br>`Service Fee`<br>`Shipping`<br>`Spa & Massage`<br>`Sporting Goods`<br>`Sports`<br>`State Tax`<br>`Student Loan`<br>`Toys`<br>`Trade Commissions`<br>`Transfer for Cash Spending`<br>`Tuition`<br>`Vacation`<br>`Veterinary`

## Additional Categories

<aside class="notice">
One of these categories can be used if the transaction does not fit into the above categories
</aside>

      |        |           |     
--------- | ------- | -----------
`Auto & Transport`<br>`Bills & Utilities`<br>`Business Services`<br>`Education`<br>`Entertainment`<br>`Fees & Charge`<br>`Financial` | `Food & Dining`<br>`Gifts & Donations`<br>`Health & Fitness`<br>`Home`<br>`Income`<br>`Investments`<br>`Kids`<br>`Misc Expenses` | `Personal Care`<br>`Pets`<br>`Shopping`<br>`Taxes`<br>`Transfer`<br>`Travel`<br>`Uncategorized`<br>


# Throttling

The Carbon Insights API currently specifies the following request rate limits for authenticated users:

  * Per Second: `30`
  * Per Minute: `400`
  * Per Day: `100000`


