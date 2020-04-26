# PUT /v2/tariffs/:tariff_id

Create or update a tariff, if already existing.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

The body can have the following attributes:

| **Name**                                        | **Type**           | **Presence** | **Example**                            | **Description**                                                                                                                                                                                       |
| ----------------------------------------------- | ------------------ | ------------ | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                                              | UUID               | required     | "1e49b853-36fc-47ed-9826-97828b5b2fdd" | Create: Client-side generated UUID, Update: Existing Resource ID                                                                                                                                      |
| type                                            | String             | required     | "tariff"                               | Type of the resource. Needs to be `company`.                                                                                                                                                          |
| name                                            | String             |              | "Mobility+"                            | Name of the tariff. Needs to be `null` for a `sub_tariff`                                                                                                                                             |
| version                                         | Integer            |              | 1                                      | Lock version. Needs to be 1 on create and for update 1 higher than the current version.                                                                                                               |
| monthly_min_sales                               | Float              |              | 6.0                                    | Minimum charging costs per month                                                                                                                                                                      |
| monthly_fee                                     | Float              |              | 4.9                                    | Subscription fee per month                                                                                                                                                                            |
| yearly_service_fee                              | Float              |              | 20.0                                   | Any additionaly fee to be paid per year                                                                                                                                                               |
| is_flat_rate                                    | Boolean            |              | true                                   | Given a monthly fee, charging with this tariff is at no extra cost for a single charge                                                                                                                |
| is_direct_payment                               | Boolean            |              | true                                   | This tariff can be used without registration                                                                                                                                                          |
| provider_customer_only                          | Boolean            |              | true                                   | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).                                                                                               |
| currency                                        | String             |              | "EUR"                                  | Currency in which any price of the tariff is defined.                                                                                                                                                 |
| prices                                          | Array              |              | -                                      | One ore more price components (restriction + price)                                                                                                                                                   |
| prices.restrictions                             | Hash               |              | -                                      | Restrictions of the current price component                                                                                                                                                           |
| prices.restrictions.allowance                   | String             |              | "allow"                                | "allow": All restriction values need to pass, to fulfill the restriction. "deny": All restriction values need to be false, to fulfill the restriction                                                 |
| prices.restrictions.charge_point_powers         | Array<Float>       |              | [7.4,11]                               | List of valid power values of the charge point                                                                                                                                                        |
| prices.restrictions.charge_point_energy_type    | String             |              | "ac"                                   | Valid power. Either "ac" or "dc".                                                                                                                                                                     |
| prices.restrictions.cpo_ids                     | Array<String>      |              | ["123","456"]                          | All valid CPO (company) IDs.                                                                                                                                                                          |
| prices.restrictions.countries                   | Array<String>      |              | ["AT","DE"]                            | All countries that apply. `ISO 3166-1` values are valid                                                                                                                                               |
| prices.restrictions.car_ac_phase                | Integer            |              | 3                                      | Valid AC phases of the car                                                                                                                                                                            |
| prices.restrictions.charge_point_power_is_range | Boolean            |              | true                                   | true: List of `charge_point_powers` needs to have two values, which define a range of valid values (first value needs to be <= second value). False: Only the specifc values are valid.               |
| prices.decomposition                            | Hash               |              | -                                      | Defines the price segment                                                                                                                                                                             |
| prices.decomposition.dimension                  | String             |              | "minute"                               | Either "minute", "kwh" or "session"                                                                                                                                                                   |
| prices.decomposition.price                      | Float              |              | -                                      | The price per dimension.                                                                                                                                                                              |
| prices.decomposition.range_gte                  | Integer            |              | -                                      | From which dimension value the price applies.                                                                                                                                                         |
| prices.decomposition.range_lt                   | Integer            |              | -                                      | Until which dimension value the price applies.                                                                                                                                                        |
| prices.decomposition.billing_increment          | Float              |              | 10                                     | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.                                                                               |
| prices.decomposition.currency                   | String             |              | -                                      | The currency of this component. Overrules the `currency` of the tariff. For any combination of prices, all values defined in the price segment which currently apply, need to have the same currency! |
| prices.decomposition.time_of_day_start          | Integer            |              | 600                                    | Time of day when this segment starts to count or gets active. Both start and end need to be set or stay empty!                                                                                        |
| prices.decomposition.time_of_day_end            | Integer            |              | 1200                                   | Time of day when this segment stops to count or be active. Both start and end need to be set or stay empty!                                                                                           |
| vehicle_brands                                  | Relationship Array |              | -                                      | Which vehicle brands are supported by this tariff.                                                                                                                                                    |
| super_tariffs                                   | Relationship Array |              | -                                      | Only allowed for `sub_tariff`. To which tariffs this sub tariff counts to.                                                                                                                            |
| emp                                             | Relationship       |              | -                                      | Owner EMP of the tariff.                                                                                                                                                                              |
| cpo                                             | Relationship       |              | -                                      | Only allowed for `sub_tariff`. CPO to which this tariff applies to.                                                                                                                                   |

## Response Body

The following table lists the `attributes` of a `tariff` or `sub_tariff` (both have the same fields):

| **Name**                                        | **Type**           | **Example**   | **Description**                                                                                                                                                                                       |
| ----------------------------------------------- | ------------------ | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                            | String             | "Mobility+"   | Name of the tariff                                                                                                                                                                                    |
| created_at                                      | Timestamp          | 1546297200000 | Creation time of the resource                                                                                                                                                                         |
| updated_at                                      | Timestamp          | 1546297200000 | Last update of the resource                                                                                                                                                                           |
| version                                         | Integer            | 1             | Current lock version                                                                                                                                                                                  |
| monthly_min_sales                               | Float              | 6.0           | Minimum charging costs per month                                                                                                                                                                      |
| monthly_fee                                     | Float              | 4.9           | Subscription fee per month                                                                                                                                                                            |
| yearly_service_fee                              | Float              | 20.0          | Any additionaly fee to be paid per year                                                                                                                                                               |
| is_flat_rate                                    | Boolean            | true          | Given a monthly fee, charging with this tariff is at no extra cost for a single charge                                                                                                                |
| is_direct_payment                               | Boolean            | true          | This tariff can be used without registration                                                                                                                                                          |
| provider_customer_only                          | Boolean            | true          | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).                                                                                               |
| currency                                        | String             | "EUR"         | Currency in which any price of the tariff is defined.                                                                                                                                                 |
| prices                                          | Array              | -             | One ore more price components (restriction + price)                                                                                                                                                   |
| prices.restrictions                             | Hash               | -             | Restrictions of the current price component                                                                                                                                                           |
| prices.restrictions.allowance                   | String             | "allow"       | "allow": All restriction values need to pass, to fulfill the restriction. "deny": All restriction values need to be false, to fulfill the restriction                                                 |
| prices.restrictions.charge_point_powers         | Array<Float>       | [7.4,11]      | List of valid power values of the charge point                                                                                                                                                        |
| prices.restrictions.charge_point_energy_type    | String             | "ac"          | Valid power. Either "ac" or "dc".                                                                                                                                                                     |
| prices.restrictions.cpo_ids                     | Array<String>      | ["123","456"] | All valid CPO (company) IDs.                                                                                                                                                                          |
| prices.restrictions.countries                   | Array<String>      | ["AT","DE"]   | All countries that apply. `ISO 3166-1` values are valid                                                                                                                                               |
| prices.restrictions.car_ac_phase                | Integer            | 3             | Valid AC phases of the car                                                                                                                                                                            |
| prices.restrictions.charge_point_power_is_range | Boolean            | true          | true: List of `charge_point_powers` needs to have two values, which define a range of valid values (first value needs to be <= second value). False: Only the specifc values are valid.               |
| prices.decomposition                            | Hash               | -             | Defines the price segment                                                                                                                                                                             |
| prices.decomposition.dimension                  | String             | "minute"      | Either "minute", "kwh" or "session"                                                                                                                                                                   |
| prices.decomposition.price                      | Float              | -             | The price per dimension.                                                                                                                                                                              |
| prices.decomposition.range_gte                  | Integer            | -             | From which dimension value the price applies.                                                                                                                                                         |
| prices.decomposition.range_lt                   | Integer            | -             | Until which dimension value the price applies.                                                                                                                                                        |
| prices.decomposition.billing_increment          | Float              | 10            | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.                                                                               |
| prices.decomposition.currency                   | String             | -             | The currency of this component. Overrules the `currency` of the tariff. For any combination of prices, all values defined in the price segment which currently apply, need to have the same currency! |
| prices.decomposition.time_of_day_start          | Integer            | 720           | Time of day when this segment starts to count or gets active                                                                                                                                          |
| prices.decomposition.time_of_day_end            | Integer            | 720           | Time of day when this segment stops to count or be active                                                                                                                                             |
| vehicle_brands                                  | Relationship Array | -             | Which vehicle brands are supported by this tariff.                                                                                                                                                    |
| super_tariffs                                   | Relationship Array | -             | Only allowed for `sub_tariff`. To which tariffs this sub tariff counts to.                                                                                                                            |
| emp                                             | Relationship       | -             | Owner EMP of the tariff.                                                                                                                                                                              |
| cpo                                             | Relationship       | -             | Only allowed for `sub_tariff`. CPO to which this tariff applies to.                                                                                                                                   |

Timestamp = Millis since 1.1.1970

## Example

### Request

```http
PUT http://example-base-url.com/v2/tariffs/2e49b853-36fc-47ed-9826-97828b5b2fdd
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "id": "2e49b853-36fc-47ed-9826-97828b5b2fdd",
    "type": "tariff",
    "attributes": {
      "version": 1,
      "name": "EinfachStromLaden",
      "prices": [
        {
          "restrictions": [
            {
              "allowance": "allow",
              "charge_point_powers": [
                11,22
              ],
              "charge_point_energy_type": "ac",
              "cpo_ids": [
                "3e49b853-36fc-47ed-9826-97828b5b2fdd"
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
              "billing_increment": 1.0,
              "currency": "EUR",
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
      "currency": "EUR"
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
          "id": "4e49b853-36fc-47ed-9826-97828b5b2fdd"
        }
      },
      "cpo": {
        "data": null
      }
    }
  }
}
```

### Response

#### 200 Ok (after update)
#### 201 Created (after create)

Body:
```json
{
  "data": {
    "id": "2e49b853-36fc-47ed-9826-97828b5b2fdd",
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
                11,22
              ],
              "charge_point_energy_type": "ac",
              "cpo_ids": [
                "3e49b853-36fc-47ed-9826-97828b5b2fdd"
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
              "billing_increment": 1.0,
              "currency": "EUR",
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
      "currency": "EUR"
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
          "id": "4e49b853-36fc-47ed-9826-97828b5b2fdd"
        }
      },
      "cpo": {
        "data": null
      }
    }
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
      "title": "api_key missing"
    }
  ]
}
```

##### 409 Conflict

An Invalid version was sent in the request.

```json
{
  "errors": [
    {
      "status": "409",
      "title": "Version conflict"
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
      "title": "some error"
    }
  ]
}
```