# POST /v1/tariff_details

Get details of multiple tariffs given a charge at...

* operator
* in a country

This API follows the <https://jsonapi.org> specification.

## Authorization Group

`ViewPriceBenchmark`

## Headers

* `API-Key: <your_api_key>` (contact <sales@chargeprice.net> to get access)
* `Content-Type: application/json`

## Request

The following fields are to be sent in the request body, in the `attributes` section:

| **Name**                         | **Type** | **Presence** | **Example**                            | **Description**                                                         |
|----------------------------------|----------|--------------|----------------------------------------|-------------------------------------------------------------------------|
| station                          | Object   | required     |                                        | Station reference                                                       |
| station.country                  | String   | required     | `AT`                                   | ISO 3166 code of the country where the prices should be fetched         |
| station.operator                 | Object   | required     |                                        | Operator of the station                                                 |
| station.operator.id              | String   | required     | `20006f18-3ed4-4715-92b5-08e37e6dd18c` | ID of the operator company                                              |
| station.operator.type            | String   | required     | `company`                              | Type of the company (always `company` for now)                          |
| filter.brand_restricted_tariffs  | Boolean  | optional     | `true`                                 | Include tariffs restricted to specific vehicle brands. Default: `true`. |
| filter.foreign_tariffs           | Boolean  | optional     | `true`                                 | Include tariffs restricted to specific countries. Default: `true`.      |
| filter.provider_customer_tariffs | Boolean  | optional     | `true`                                 | Include tariffs restricted to provider customers. Default: `false`.     |

The following table lists the `relationships` section:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                   |
|----------|------------------------|--------------|-----------------------------------------|---------------------------------------------------|
| tariffs  | Array of Relationships | optional     | `[{"id": "some-uuid", type:"tariff" }]` | Details for the those tariffs should be returned. |

### Includes

The `emp` and `tariff` relationship are included per default.
See the response of the `included` section.

## Response Body

The following table lists the `attributes` of these objects:

| **Name**                                        | **Type**      | **Example**   | **Description**                                                                                                                                                                                                                                                              |
|-------------------------------------------------|---------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| country                                         | String        | `AT`          | ISO 3166 code of the country for which the prices are defined.                                                                                                                                                                                                               |
| updated_at                                      | Timestamp     | 1664446527000 | Time when the tariff has been updated                                                                                                                                                                                                                                        |
| tariff_level                                    | String        | `cpo`         | At which level this tariff applies: `country`: the default tariff for all CPOs in this country, `cpo`: tariff applies to all EVSEs of this CPO, `evse` (not implemented yet): tariff applies only to a specific EVSE.                                                        |
| restricted_segments                             | Array<Object> | -             |                                                                                                                                                                                                                                                                              |
| restricted_segments.dimension                   | String        | `minute`      | Either "minute", "kwh" or "session"                                                                                                                                                                                                                                          |
| restricted_segments.price                       | Float         | 0.39          | The price per dimension.                                                                                                                                                                                                                                                     |
| restricted_segments.range_gte                   | Integer       | 60            | From which dimension value the price applies. e.g. from 60 minutes on.                                                                                                                                                                                                       |
| restricted_segments.range_lt                    | Integer       | 120           | Until which dimension value the price applies.      e.g. until 120 minutes.                                                                                                                                                                                                  |
| restricted_segments.billing_increment           | Float         | 10            | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.                                                                                                                                                      |
| restricted_segments.currency                    | String        | `EUR`         | The currency of this component. Overrules the `currency` of the tariff.                                                                                                                                                                                                      |
| restricted_segments.time_of_day_start           | Integer       | 720           | Time of day when this segment starts to match. In minutes.                                                                                                                                                                                                                   |
| restricted_segments.time_of_day_end             | Integer       | 1200          | Time of day when this segment stops to match. In minutes.                                                                                                                                                                                                                    |
| restricted_segments.charge_point_powers         | Array<Float>  | [0.0,22.0]    | List of power value restrictions of a charge point. If empty, all power values match. Also see `charge_point_power_is_range` on how to use this field.                                                                                                                       |
| restricted_segments.charge_point_energy_type    | String        | `ac`          | Matching power/phase. Either "ac", "dc" or `null` if it applies for both.                                                                                                                                                                                                    |
| restricted_segments.car_ac_phase                | Integer       | 3             | Matching AC phases of the car (legacy attribute, practically not used anymore by EMSPs).                                                                                                                                                                                     |
| restricted_segments.charge_point_power_is_range | Boolean       | `true`        | `true`: List of `charge_point_powers` has two values, which define a range of matching power values (Example: [11,22] => 11, 15 and 22 kW charge points match). `false`: Only specifc values are matching (Example: [11,22] => 11 and 22 kW charge points match, 15 kw not). |
| restricted_segments.use_consumed_charging_power | Boolean       | `false`       | if `true`: The consumed power of the car defines the price, instead of the maximum power of the station. `false`: Maximum power of the station defines the price.                                                                                                            |

The following table lists the `relationships` and their values in the `included` section:

| **Name**                      | **Type**           | **Example**                            | **Description**                                                                                               |
|-------------------------------|--------------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------|
| tariff                        | Relationship       | `{"id": "some-uuid", type:"tariff" }`  | The tariff for which the details are given.                                                                   |
| tariff.name                   | String             | `easyFlex`                             | Name of the tariff                                                                                            |
| tariff.total_monthly_fee      | Float              | 10.0                                   | Monthly fee incl. a 12th of any yearly fee.                                                                   |
| tariff.is_direct_payment      | Boolean            | `true`                                 | This tariff can be used without registration                                                                  |
| tariff.is_card_payment        | Boolean            | `true`                                 | This tariff applies to card payments at stations with a terminal                                                          |
| tariff.provider_customer_only | Boolean            | `true`                                 | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).       |
| tariff.currency               | String             | `EUR`                                  | Main currency of the tariff. Applies to e.g. the monthly fee. Currency of prices can vary country by country. |
| tariff.url                    | String             | `http://www.google.at`                 | Website of the tariff.                                                                                        |
| tariff.vehicle_brands         | Relationship Array | -                                      | Only owners of these vehicle brands are allowed to subscribe to this tariff.                                  |
| emp                           | Relationship       | `{"id": "some-uuid", type:"company" }` | The EMP (E-Mobility Service Provider) who offers the tariff.                                                  |
| emp.name                      | String             | `Energie Steiermark`                   | Company name of the EMP                                                                                       |
| cpo                           | Relationship       | `{"id": "some-uuid", type:"company" }` | The CPO (Charge Point Operator) to which this tariff applies.                                                 |

Timestamp = Millis since 1.1.1970

### Unit prices for a specifc charge point

The `restricted_segments` array contains a list of all unit prices that define an EMP tariff at a specific CPO for all charge points.

In order to get the unit prices for a specifc charge point (e.g. 22 kW AC) you need to filter the `restricted_segments` and only use those array items, that are matching the charging station configuration. Note that one segment can be used by multiple charging station configurations. e.g. there could be a session fee, which applies to all power levels and it might only listed once in the `restricted_segments`.

To find all matching segments you need to filter using the following attributes:

* charge_point_energy_type
* charge_point_powers
* charge_point_power_is_range
* use_consumed_charging_power
* car_ac_phase

## Example

### Request

```http
POST http://example-base-url.com/v1/tariff_details
Content-Type: application/json
Api-Key: my-secret-key
```

Body:

```json
{
  "data": {
    "attributes": {
      "station": {
        "country": "AT",
        "operator": {
          "id": "50006f18-3ed4-4715-92b5-08e37e6dd18c",
          "type": "company"
        }
      },
      "filter": {
        "brand_restricted_tariffs": true,
        "foreign_tariffs": true,
        "provider_customer_tariffs": true,
      }
    },
    "relationships": {
      "tariffs": {
        "data": [
          {
            "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
            "type": "tariff"
          }
        ]
      }
    }
  }
}
```

### Response

#### 200 Ok

Body:

```json
{
  "data": [
    {
      "id": "40006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "station_tariff_details",
      "attributes": {
        "updated_at": 1546300800000,
        "tariff_level": "country",
        "restricted_segments": [
          {
            "dimension": "kwh",
            "price": 2.0,
            "range_gte": null,
            "range_lt": null,
            "billing_increment": 0.01,
            "currency": "EUR",
            "time_of_day_start": null,
            "time_of_day_end": null,
            "charge_point_powers": [
              0.0,
              22.0
            ],
            "charge_point_energy_type": "ac",
            "car_ac_phase": 2,
            "charge_point_power_is_range": true,
            "use_consumed_charging_power": false
          }
        ]
      },
      "relationships": {
        "emp": {
          "data": {
            "type": "company",
            "id": "10006f18-3ed4-4715-92b5-08e37e6dd18c"
          }
        },
        "tariff": {
          "data": {
            "type": "tariff",
            "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c"
          }
        },
        "cpo": {
          "data": {
            "type": "company",
            "id": "50006f18-3ed4-4715-92b5-08e37e6dd18c"
          }
        }
      }
    }
  ],
  "included": [
    {
      "id": "10006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "company",
      "attributes": {
        "name": "Energie Steiermark",
      }
    },
    {
      "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "tariff",
      "attributes": {
        "name": "easyFlex",
        "total_monthly_fee": 5.0,
        "is_direct_payment": false,
        "is_card_payment": false,
        "provider_customer_only": false,
        "currency": "EUR",
        "url": "http://www.google.at"
      },
      "relationships": {
        "vehicle_brands": {
          "data": [
            {
              "id": "50006f11-3ed4-4715-92b5-08e37e6dd185",
              "type": "brand"
            }
          ]
        }
      }
    }
  ]
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
