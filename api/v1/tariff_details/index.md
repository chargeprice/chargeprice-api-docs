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

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

The following fields are to be sent in the request body, in the `attributes` section:

| **Name**                    | **Type**       | **Presence** | **Example**                            | **Description**                                                                                     |
| --------------------------- | -------------- | ------------ | -------------------------------------- | --------------------------------------------------------------------------------------------------- |
| station                     | Object         | mandatory    |                                        | Station reference                                                                                   |
| station.country             | String         | mandatory    | `AT`                                   | ISO 3166 country code of the location                                                               |
| station.operator            | Object         | mandatory    |                                        | Operator of the station                                                                             |
| station.operator.id         | String         | mandatory    | `20006f18-3ed4-4715-92b5-08e37e6dd18c` | ID of the operator company                                                                          |
| station.operator.type       | String         | mandatory    | `company`                              | Type of the company (always `company` for now)                                                      |
| station.charge_points       | Array[Object]  | mandatory    |                                        | All kinds of connectors where the tariff details should be returned                                 |
| station.charge_points.power | Float          | mandatory    | 22                                     | In kW                                                                                               |
| station.charge_points.plug  | String         | mandatory    | `ccs`                                  | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`)      |
| battery_range               | Array[Integer] | mandatory    | [20,80]                                | At which battery level the charge should start (first value) and end (second value). In percentage. |

The following table lists the `relationships` section:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                                                       |
| -------- | ---------------------- | ------------ | --------------------------------------- | ------------------------------------------------------------------------------------- |
| tariffs  | Array of Relationships | mandatory    | `[{"id": "some-uuid", type:"tariff" }]` | Details for the those tariffs should be returned.                                     |
| vehicle  | Relationship           | mandatory    | `{"id": "some-uuid", type:"car" }`      | Vehicle at charge. See [GET v1/vehicles](../vehicles/index.md) for the valid options. |


## Response Body

The following table lists the `attributes` of these objects:

| **Name**                   | **Type**      | **Example** | **Description**                                                                                                           |
| -------------------------- | ------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------- |
| segments                   | Array<Object> | -           |                                                                                                                           |
| segments.dimension         | String        | `minute`    | Either "minute", "kwh" or "session"                                                                                       |
| segments.price             | Float         | 0.39        | The price per dimension.                                                                                                  |
| segments.range_gte         | Integer       | 60          | From which dimension value the price applies. e.g. from 60 minutes on.                                                    |
| segments.range_lt          | Integer       | 120         | Until which dimension value the price applies.      e.g. until 120 minutes.                                               |
| segments.billing_increment | Float         | 10          | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.   |
| segments.currency          | String        | `EUR`       | The currency of this component. Overrules the `currency` of the tariff. If not set, the `currency` of the tariff applies. |
| segments.time_of_day_start | Integer       | 720         | Time of day when this segment starts to count or gets active. In minutes.                                                 |
| segments.time_of_day_end   | Integer       | 1200        | Time of day when this segment stops to count or be active. In minutes.                                                    |
| charge_point               | Object        | -           |                                                                                                                           |
| charge_point.plug          | String        | `ccs`       | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`)                            |
| charge_point.power         | Float         | 22          | In kW                                                                                                                     |
| charge_point.energy_type   | String        | `ac`        | Either `ac` or `dc`.                                                                                                      |
| country                    | String        | `AT`        | ISO 3166 country code of the location                                                                                     |

The following table lists the `relationships` section:

| **Name** | **Type**     | **Presence** | **Example**                              | **Description**                                               |
| -------- | ------------ | ------------ | ---------------------------------------- | ------------------------------------------------------------- |
| tariff   | Relationship | mandatory    | `{"id": "some-uuid", type:"tariff" }`    | The tariff for which the details are given.                   |
| emp      | Relationship | mandatory    | `{"id": "some-uuid", type:"company" }`   | The EMP (E-Mobility Service Provider) who offers the tariff.  |
| cpo      | Relationship | mandatory    | `{"id": "some-uuid", type:"company" }` | The CPO (Charge Point Operator) to which this tariff applies. |


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
    "attributes":    {
      "battery_range": [20, 80],
      "station":       {
        "country": "AT",
        "operator": {
          "id": "50006f18-3ed4-4715-92b5-08e37e6dd18c",
          "type": "company"
        },
        "charge_points": [
          { "plug": "ccs", "power": 50 }
        ]
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
      },
      "vehicle": {
        "data": {
          "id": "90006f18-3ed4-4715-92b5-08e37e6dd18c",
          "type": "car"
        }
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
        "segments": [
          {
            "dimension": "kwh",
            "price": 2.0,
            "range_gte": null,
            "range_lt": null,
            "billing_increment": 0.01,
            "currency": null,
            "time_of_day_start": null,
            "time_of_day_end": null
          },
          {
            "dimension": "minute",
            "price": 0.5,
            "range_gte": 60,
            "range_lt": 120,
            "billing_increment": 0.01,
            "currency": "EUR",
            "time_of_day_start": 600,
            "time_of_day_end": 1200
          }
        ],
        "charge_point": {
          "plug": "ccs",
          "power": 50.0,
          "energy_type": "dc"
        },
        "country": "AT"
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