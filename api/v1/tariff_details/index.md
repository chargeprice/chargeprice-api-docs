# GET /v1/tariff_details

Get details of multiple tariffs given a charge at...
* a specific type of station
* operator
* in a country
* with a specific vehicle
* and battery range 

This API follows the https://jsonapi.org specification.

## Authorization Group

`ViewPriceBenchmark`

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

The following fields are to be sent in the request body, in the `attributes` section:

| **Name**              | **Type** | **Presence** | **Example**                            | **Description**                                |
| --------------------- | -------- | ------------ | -------------------------------------- | ---------------------------------------------- |
| station               | Object   | mandatory    |                                        | Station reference                              |
| station.country       | String   | mandatory    | `AT`                                   | ISO 3166 country code of the location          |
| station.operator      | Object   | mandatory    |                                        | Operator of the station                        |
| station.operator.id   | String   | mandatory    | `20006f18-3ed4-4715-92b5-08e37e6dd18c` | ID of the operator company                     |
| station.operator.type | String   | mandatory    | `company`                              | Type of the company (always `company` for now) |

The following table lists the `relationships` section:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                   |
| -------- | ---------------------- | ------------ | --------------------------------------- | ------------------------------------------------- |
| tariffs  | Array of Relationships | mandatory    | `[{"id": "some-uuid", type:"tariff" }]` | Details for the those tariffs should be returned. |

### Includes

The `emp` and `tariff` relationship are included per default.
See the response of the `included` section. 

## Response Body

The following table lists the `attributes` of these objects:

| **Name**                             | **Type**      | **Example** | **Description**                                                                                                                                                                         |
| ------------------------------------ | ------------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| segments                             | Array<Object> | -           |                                                                                                                                                                                         |
| segments.dimension                   | String        | `minute`    | Either "minute", "kwh" or "session"                                                                                                                                                     |
| segments.price                       | Float         | 0.39        | The price per dimension.                                                                                                                                                                |
| segments.range_gte                   | Integer       | 60          | From which dimension value the price applies. e.g. from 60 minutes on.                                                                                                                  |
| segments.range_lt                    | Integer       | 120         | Until which dimension value the price applies.      e.g. until 120 minutes.                                                                                                             |
| segments.billing_increment           | Float         | 10          | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.                                                                 |
| segments.currency                    | String        | `EUR`       | The currency of this component. Overrules the `currency` of the tariff.                                                                                                                 |
| segments.time_of_day_start           | Integer       | 720         | Time of day when this segment starts to count or gets active. In minutes.                                                                                                               |
| segments.time_of_day_end             | Integer       | 1200        | Time of day when this segment stops to count or be active. In minutes.                                                                                                                  |
| segments.charge_point_powers         | Array<Float>  | [0.0,22.0]  | List of valid power values of the charge point                                                                                                                                          |
| segments.charge_point_energy_type    | String        | `ac`        | Valid power/phase. Either "ac", "dc" or `null` if it applies for both.                                                                                                                  |
| segments.car_ac_phase                | Integer       | 3           | Valid AC phases of the car.                                                                                                                                                             |
| segments.charge_point_power_is_range | Boolean       | `true`      | true: List of `charge_point_powers` needs to have two values, which define a range of valid values (first value needs to be <= second value). False: Only the specifc values are valid. |
| segments.use_consumed_charging_power | Boolean       | `false`     | if true: The consumed power of the car defines the price, instead of the maximum power of the station.                                                                                  |

The following table lists the `relationships` and their values in the `included` section:

| **Name**    | **Type**     | **Presence** | **Example**                            | **Description**                                               |
| ----------- | ------------ | ------------ | -------------------------------------- | ------------------------------------------------------------- |
| tariff      | Relationship | mandatory    | `{"id": "some-uuid", type:"tariff" }`  | The tariff for which the details are given.                   |
| tariff.name | String       | mandatory    | `easyFlex`                             | Name of the tariff                                            |
| emp         | Relationship | mandatory    | `{"id": "some-uuid", type:"company" }` | The EMP (E-Mobility Service Provider) who offers the tariff.  |
| emp.name    | String       | mandatory    | `Energie Steiermark`                   | Company name of the EMP                                       |
| cpo         | Relationship | mandatory    | `{"id": "some-uuid", type:"company" }` | The CPO (Charge Point Operator) to which this tariff applies. |


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
        "name": "Energie Steiermark"
      }
    },
    {
      "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "tariff",
      "attributes": {
        "name": "easyFlex"
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