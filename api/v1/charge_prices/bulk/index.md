# POST /v1/charge_prices/bulk

Gets the calculated prices of specific tariffs for one or many charging
networks, given a vehicle and battery range.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadCosts`

## Request

The following query parameters are available: 

| **Name**          | **Type** | **Presence**                 | **Example** | **Description**                                                                                                      |
| ----------------- | -------- | ---------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------- |
| page[prices.size] | Integer  | optional (default: no limit) | 1           | Number of prices returned per `bulk_charge_price_group`. Example: If set to 3, the lowest 3 prices will be returned. |

The following fields are to be sent in the request body, in the `attributes` section of a `charge_price_bulk_request` object:


| **Name**                    | **Type**     | **Presence**              | **Example**      | **Description**                                                                                              |
| --------------------------- | ------------ | ------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------ |
| stations                    | Array        | required                  |                  | 1 - 20 stations for wich the prices are fetched.                                                             |
| stations.data_adapter       | string       | required                  | "going_electric" | The source of the station. Possible values: "going_electric", "chargeprice", "open_charge_map"               |
| stations.country            | String       | required                  | "Deutschland"    | Country in which the station is located.                                                                     |
| stations.network            | String       | required                  | "Wien Energie"   | Network to which the station belongs to.                                                                     |
| stations.charge_point_power | Float        | required                  | 22               | Power of the station (kW).                                                                                   |
| stations.charge_point_plug  | String       | required                  | "CCS"            | Name of plug.                                                                                                |
| battery_range               | Array[Float] | required                  | [20.0,80.0]      | Array with 2 values, that defines the battery start and end values for the charge in percentage.             |
| currency                    | String       | optional (default: "EUR") | "EUR"            | Currency in which the prices should be returned. Possible values are listed at the [eurofxref]               |
| start_time                  | Integer      | optional (default: 12:00) | 720 (=12:00)     | Time of day in minutes when the charging session gets started. Min value: 0 (00:00), max value: 1439 (23:59) |
| allow_unbalanced_load       | Boolean      | optional (default: false) | false            | If true, it allows higher powers for uniphase charging vehicles. If false, power is restricted to 4.5 kW.    |

The following table lists the `relationships` section of a `charge_price_bulk_request` object:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                                                                                                                |
| -------- | ---------------------- | ------------ | --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| tariffs  | Array of Relationships | required     | `[{"id": "some-uuid", type:"tariff" }]` | Prices are calculated for the given tariffs. Up to 20 tariffs can be defined. See [GET v1/tariffs](../tariffs/index.md) for the valid options. |
| vehicle  | Relationship           | required     | `{"id": "some-uuid", type:"car" }`      | Vehicle at charge. See [GET v1/vehicles](../vehicles/index.md) for the valid options.                                                          |

## Response Body

### Bulk Charge Price Group

A response contains one `bulk_charge_price_group` object per station, provided in the request.
The following table lists the `attributes` of these objects:

| **Name**           | **Type** | **Example**      | **Description**                                                                        |
| ------------------ | -------- | ---------------- | -------------------------------------------------------------------------------------- |
| data_adapter       | String   | "going_electric" | Same as in the request.                                                                |
| country            | String   | "Österreich"     | Same as in the request.                                                                |
| network            | String   | "Wien Energie"   | Same as in the request.                                                                |
| charge_point_power | Float    | 50.0             | Same as in the request.                                                                |
| charge_point_plug  | String   | "CCS"            | Same as in the request.                                                                |
| energy             | Float    | 25.0             | Energy to be charged (kWh) based on the request (duration or vehicle + battery range)  |
| duration           | Float    | 30.0             | Charging duration (minutes) based on the request (duration or vehicle + battery range) |

The following table lists the `relationships`:

| **Name** | **Type**               | **Example**                                        | **Description**                                                                                                |
| -------- | ---------------------- | -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| prices   | Array of Relationships | `[{"id": "some-uuid", type:"bulk_charge_price" }]` | The reference to the calculated prices for the given station. The `included` section contains the full object. |

### Bulk Charge Price

The `included` section of the response contains `bulk_charge_price` objects.
The following table lists the `attributes` of these objects:

| **Name**    | **Type** | **Example**         | **Description**                                                          |
| ----------- | -------- | ------------------- | ------------------------------------------------------------------------ |
| emp_name    | String   | "Maingau Energie"   | Name of the E-Mobility Provider offering the tariff.                     |
| tariff_name | String   | "EinfachStromLaden" | Name of the tariff.                                                      |
| price       | Float    | 12.0                | Calculated cost of a single charge, given the `bulk_charge_price_group`. |
| currency    | String   | "EUR"               | Currency of the price.                                                   |

The following table lists the `relationships`:

| **Name** | **Type**     | **Example**                           | **Description**                                    |
| -------- | ------------ | ------------------------------------- | -------------------------------------------------- |
| tariff   | Relationship | `{"id": "some-uuid", type:"tariff" }` | The tariff that was applied for this charge price. |


## Example

### Request

```http
POST http://example-base-url.com/v1/charge_prices/bulk?page[prices.size]=1
Content-Type: application/json
Api-Key: my-secret-key
```

```json
{
  "data": {
    "type": "charge_price_bulk_request",
    "attributes": {
      "stations": [
        {
          "data_adapter": "going_electric",
          "network": "Wien Energie",
          "country": "Österreich",
          "charge_point_power": 50.0,
          "charge_point_plug": "CCS"
        }
      ],
      "battery_range": [20.0,80.0],
      "start_time": 720,
      "currency": "EUR"
    },
    "relationships": {
      "tariffs": {
        "data": [
          {
            "id": "c6adf982-4e41-431b-833a-dfa89b484c75",
            "type": "tariff"
          }
        ]
      },
      "vehicle": {
        "data": {
          "id": "46adf982-4e41-431b-833a-dfa89b484c71",
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
      "id": "123",
      "type": "bulk_charge_price_group",
      "attributes": {
        "data_adapter": "going_electric",
        "network": "Wien Energie",
        "country": "Österreich",
        "charge_point_power": 50.0,
        "charge_point_plug": "CCS",
        "duration": 30.0,
        "energy": 12.0,
      },
      "relationships": {
        "prices": {
          "data": [
            {
              "id": "456",
              "type": "bulk_charge_price"
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": "456",
      "type": "bulk_charge_price",
      "attributes": {
        "emp_name": "Maingau Energie",
        "tariff_name": "EinfachStromladen",
        "price": 12.0,
        "currency": "EUR"
      },
      "relationships": {
        "tariff": {
          "data": {
            "id": "c6adf982-4e41-431b-833a-dfa89b484c75",
            "type": "tariff"
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

##### 404 Not Found

e.g. The provided currency doesn't exist in the system.

```json
{
  "errors": [
    {
      "status": "404",
      "code": "NOT_FOUND",
      "title": "No currency with iso ATS"
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

[eurofxref]: https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html
