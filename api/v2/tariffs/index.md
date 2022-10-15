# GET /v2/tariffs

Returns raw tariffs. Tariffs can either be of type `tariff` or `sub_tariff`
(which describes the tariff per CPO in more detail).

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadTariffs`

## Request

The following query parameters are available.

| **Name**                 | **Type**         | **Presence** | **Example**         | **Description**                                                           |
| ------------------------ | ---------------- | ------------ | ------------------- | ------------------------------------------------------------------------- |
| filter[emp.id]           | String           | required     | "123"               | Only the tariffs which belong to this EMP are returned                    |
| filter[id]               | CSV              | optional     | "123,456"           | If set, only tariffs with the given ids are returned                      |
| filter[cpo.id]           | CSV              | optional     | "123"               | If set, only tariffs with the given CPO are returned                      |
| filter[super_tariffs.id] | CSV              | optional     | "123"               | If set, only tariffs with the given Super Tariff are returned             |
| filter[type]             | CSV              | optional     | "tariff,sub_tariff" | If set, only tariffs with the given types are returned                    |
| fields[tariff]           | CSV              | optional     | "name,url"          | If set, only the given fields of a tariff are returned                    |
| fields[sub_tariff]       | CSV              | optional     | "name,url"          | If set, only the given fields of a sub_tariff are returned                |
| page[size]               | Positive Integer | optional     | 2                   | Max no. of elements in the current response. Default: 100, Max value: 100 |
| page[number]             | Positive Integer | optional     | 10                  | Current page number. Default: 1                                           |

## Response Body

A response contains 0 to many tariffs.
The following table lists the `attributes` of a `tariff` or `sub_tariff` (both have the same fields):

| **Name**                                        | **Type**            | **Example**            | **Description**                                                                                                                                                                                                       |
| ----------------------------------------------- | ------------------- | ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                            | String              | "Mobility+"            | Name of the tariff                                                                                                                                                                                                    |
| created_at                                      | Timestamp           | 1546297200000          | Creation time of the resource                                                                                                                                                                                         |
| updated_at                                      | Timestamp           | 1546297200000          | Last update of the resource                                                                                                                                                                                           |
| version                                         | Integer             | 1                      | Current lock version                                                                                                                                                                                                  |
| monthly_min_sales                               | Float               | 6.0                    | Minimum charging costs per month                                                                                                                                                                                      |
| monthly_fee                                     | Float               | 4.9                    | Subscription fee per month                                                                                                                                                                                            |
| yearly_service_fee                              | Float               | 20.0                   | Any additionaly fee to be paid per year                                                                                                                                                                               |
| is_flat_rate                                    | Boolean             | true                   | Given a monthly fee, charging with this tariff is at no extra cost for a single charge                                                                                                                                |
| is_direct_payment                               | Boolean             | true                   | This tariff can be used without registration                                                                                                                                                                          |
| provider_customer_only                          | Boolean             | true                   | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).                                                                                                               |
| currency                                        | String              | "EUR"                  | Currency in which any price of the tariff is defined.                                                                                                                                                                 |
| notes                                           | String              | "Very special tariff"  | Any other information about the tariff. Free text.                                                                                                                                                                    |
| url                                             | String              | "http://www.google.at" | In case the tariff has a specific url (other than the general EMP url)                                                                                                                                                |
| no_price_policy                                 | String              | "inherit"              | What should happen if no price is available? Options: `inherit` (tariff -> `hide`, `sub_tariff` and `poi_tariff` -> same as `super_tariff`), `hide` (don't show at station), `show_reason` (show reason stated below) |
| no_price_reason                                 | String              | "inherit"              | Reason why there is no price available. Options: `inherit` (tariff -> `not_yet_listed`, `sub_tariff` and `poi_tariff` -> same as `super_tariff`), `prices_per_station`, `not_public`, `not_yet_listed`                |
| apply_prices_to_sub_tariff                      | Boolean (or `null`) | true                   | Prices of a tariff should be applied (copied to) sub tariffs during the price calculation.                                                                                                                            |
| supported_countries                             | Array<String>       | ["AT","DE"]            | List of countries where the tariff is mainly supported.                                                                                                                                                               |
| tags                                            | Array               | -                      | Tags that are shown next to the tariff. E.g. "New Prices since 01.09.", "Club Membership required"                                                                                                                    |
| tags.kind                                       | String              | "info"                 | Find all valid value [here](/docs/api/enums.md).                                                                                                                                                                      |
| tags.localized_text                             | Hash                | { "en": "Hello" }      | Key-value pairs of localized text. Find all valid languages [here](/docs/api/enums.md).                                                                                                                               |
| tags.url                                        | String              | "http://xyz.com"       | A URL where the tag links to. Might contain `{locale}` (e.g. `http://test.com/{locale}/post.html`), which should be replaced by the current locale by the client.                                                     |
| prices                                          | Array               | -                      | One ore more price components (restriction + price)                                                                                                                                                                   |
| prices.restrictions                             | Hash                | -                      | Restrictions of the current price component                                                                                                                                                                           |
| prices.restrictions.allowance                   | String              | "allow"                | "allow": All restriction values need to pass, to fulfill the restriction. "deny": All restriction values need to be false, to fulfill the restriction                                                                 |
| prices.restrictions.charge_point_powers         | Array<Float>        | [7.4,11]               | List of valid power values of the charge point                                                                                                                                                                        |
| prices.restrictions.charge_point_energy_type    | String              | "ac"                   | Valid power/phase. Either "ac", "dc" or `null` if it applies for both.                                                                                                                                                |
| prices.restrictions.cpo_ids                     | Array<String>       | ["123","456"]          | All valid CPO (company) IDs.                                                                                                                                                                                          |
| prices.restrictions.countries                   | Array<String>       | ["AT","DE"]            | All countries that apply. `ISO 3166-1` values are valid                                                                                                                                                               |
| prices.restrictions.car_ac_phase                | Integer             | 3                      | Valid AC phases of the car                                                                                                                                                                                            |
| prices.restrictions.charge_point_power_is_range | Boolean             | true                   | true: List of `charge_point_powers` needs to have two values, which define a range of valid values (first value needs to be <= second value). False: Only the specifc values are valid.                               |
| prices.restrictions.use_consumed_charging_power | Boolean             | true                   | if true: The consumed power of the car defines the price, instead of the maximum power of the station.                                                                                                                |
| prices.decomposition                            | Hash                | -                      | Defines the price segment                                                                                                                                                                                             |
| prices.decomposition.dimension                  | String              | "minute"               | Either "minute", "kwh" or "session"                                                                                                                                                                                   |
| prices.decomposition.price                      | Float               | -                      | The price per dimension.                                                                                                                                                                                              |
| prices.decomposition.range_gte                  | Integer             | -                      | From which dimension value the price applies.                                                                                                                                                                         |
| prices.decomposition.range_lt                   | Integer             | -                      | Until which dimension value the price applies.                                                                                                                                                                        |
| prices.decomposition.billing_increment          | Float               | 10                     | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.                                                                                               |
| prices.decomposition.currency                   | String              | -                      | The currency of this component. Overrules the `currency` of the tariff. For any combination of prices, all values defined in the price segment which currently apply, need to have the same currency!                 |
| prices.decomposition.time_of_day_start          | Integer             | 720                    | Time of day when this segment starts to count or gets active                                                                                                                                                          |
| prices.decomposition.time_of_day_end            | Integer             | 720                    | Time of day when this segment stops to count or be active                                                                                                                                                             |
| vehicle_brands                                  | Relationship Array  | -                      | Which vehicle brands are supported by this tariff.                                                                                                                                                                    |
| super_tariffs                                   | Relationship Array  | -                      | Only allowed for `sub_tariff`. To which tariffs this sub tariff counts to.                                                                                                                                            |
| emp                                             | Relationship        | -                      | Owner EMP of the tariff.                                                                                                                                                                                              |
| cpo                                             | Relationship        | -                      | Only allowed for `sub_tariff`. CPO to which this tariff applies to.                                                                                                                                                   |

Timestamp = Millis since 1.1.1970

## Example

### Request

```http
GET http://example-base-url.com/v2/tariffs?page[number]=1&page[size]=1&filter[emp.id]=2e49b853-36fc-47ed-9826-97828b5b2fdd
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 200 Ok

Body:
```json
{
  "data": [
    {
      "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
      "type": "tariff",
      "attributes": {
        "created_at": 1546297200000,
        "updated_at": 1546297200000,
        "version": 1,
        "name": "EinfachStromLaden",
        "prices": [
          {
            "restrictions": [
              {
                "allowance": "allow",
                "charge_point_powers": [
                  3.7,11
                ],
                "charge_point_energy_type": null,
                "cpo_ids": [
                  "2e49b853-36fc-47ed-9826-97828b5b2fdd"
                ],
                "countries": [
                  "DE"
                ],
                "car_ac_phase": 3,
                "charge_point_power_is_range": false
              }
            ],
            "decomposition": [
              {
                "dimension": "minute",
                "price": 0.1,
                "range_gte": 0,
                "range_lt": 60,
                "billing_increment": 0.01,
                "currency": null,
                "time_of_day_start": 600,
                "time_of_day_end": 1200
              }
            ]
          }
        ],
        "monthly_min_sales": 0.0,
        "monthly_fee": 0.0,
        "yearly_service_fee": 0.0,
        "is_flat_rate": false,
        "is_direct_payment": false,
        "provider_customer_only": false,
        "currency": "EUR",
        "notes": null,
        "url": "http://www.google.at",
        "supported_countries": ["AT","DE"],
        "no_price_policy": "inherit",
        "no_price_reason": "inherit",
        "tags": [
          {
            "kind": "alert",
            "localized_text": {
              "en": "Hello World",
              "de": "Hallo Welt"
            },
            "url": "http://www.google.at"
          }
        ],
        "apply_prices_to_sub_tariff": false
      },
      "relationships": {
        "vehicle_brands": {
          "data": [
            {
              "type": "brand",
              "id": "7e49b853-36fc-47ed-9826-97828b5b2fdd"
            }
          ]
        },
        "super_tariffs": {
          "data": [

          ]
        },
        "emp": {
          "data": {
            "type": "company",
            "id": "2e49b853-36fc-47ed-9826-97828b5b2fdd"
          }
        },
        "cpo": {
          "data": null
        }
      }
    }
  ],
  "meta": {
    "overall_count": 3
  }
}
```

##### 400 Bad Request

Client provided invalid request body.

```json
{
  "errors": [
    {
      "status": "400",
      "code": "BAD_REQUEST",
      "title": "..."
    }
  ]
}
```

##### 403 Forbidden

* API-Key is missing
* API-Key is invalid
* API-Key not authorized to perform action

```json
{
  "errors": [
    {
      "status": "403",
      "code": "FORBIDDEN",
      "title": "api_key missing"
    }
  ]
}
```

##### 500 Internal Server Error

An unexpected error happened.

```json
{
  "errors": [
    {
      "status": "500",
      "code": "INTERNAL_SERVER_ERROR",
      "title": "some error"
    }
  ]
}
```