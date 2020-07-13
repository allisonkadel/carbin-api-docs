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

All API access is over HTTPS, and accessed from `https://api.carboninsights.co`. All data is sent and received as JSON.

# Authentication

<!-- > To authorize, use this code: -->

```shell
curl "https://api.carboninsights.co"
  -H "Authorization: Token YOU_TOKEN"
```

carbIN uses JSON Web Tokens to allow access to the API. If you are interested in applying for a token, please contact <tech@carboninsights.co>.

carbIN requires that the JWT is included in all API requests to the server in a header that looks like the following:

`Authorization: Token YOUR_TOKEN`

<aside class="notice">
You must replace <code>YOUR_TOKEN</code> with your personal JWT.
</aside>

# Endpoints

## Calculate a carbon score

> Sample Request:

```json
{
	"user_profile": {
		"diet": "vegetarian",
		"VRE": false,
		"natural_gas": false,
		"shared_account": false, 
        "zip_code": 19130
	},
	"options": {
		"offsets": false,
		"recommendations": true,
		"CO2e": true,
		"land": false,
		"water": false
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

Parameter | Default | Description
--------- | ------- | -----------
`date` |  |  The transaction date
`amount` |  | The transaction amount in USD
`category` |  | The transaction category
`description` |  | The transaction description
`original_description` |  | The original description of the transaction

### Query Parameters for `user_profile`
_Optional_

Parameter | Default | Description
--------- | ------- | -----------
`diet` | `"typical"` | The user's diet. Can be one of: <br>`"typical"`<br>`"vegetarian"`<br>`"vegan"`
`VRE` |  `0` | The fraction of the user's eletricity that is green
`natural_gas` |  `false` | Whether the user uses natural gas for heating and cooling
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


<aside class="notice">
If you include the <code>user_profile</code> or <code>options</code> parent parameters, you are required to include all corresponding child parameters. Providing the data in <code>user_profile</code> improves the accuracy of the environmental footprint calculation. If it is not included, typical values for a US resident will be assumed.
</aside>

# Throttling

The Carbon Insights API currently specifies the following request rate limits for authenticated users:

  * Per Second: `30`
  * Per Minute: `400`
  * Per Day: `100000`