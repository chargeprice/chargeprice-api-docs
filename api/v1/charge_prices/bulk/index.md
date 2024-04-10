# POST /v1/charge_prices/bulk

Calculates the best price at a list of stations, given a list of tariffs, a
vehicle and battery range.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadCosts`

## Request

The following fields are to be sent in the request body, in the `attributes` section of a `bulk_price_preview_request` object:

| **Name**              | **Type**     | **Presence**              | **Example**  | **Description**                                                                                              |
|-----------------------|--------------|---------------------------|--------------|--------------------------------------------------------------------------------------------------------------|
| battery_range         | Array[Float] | required                  | [20.0,80.0]  | Array with 2 values, that defines the battery start and end values for the charge in percentage.             |
| currency              | String       | optional (default: "EUR") | "EUR"        | Currency in which the prices should be returned. Possible values are listed at the [eurofxref]               |
| start_time            | Integer      | optional (default: 12:00) | 720 (=12:00) | Time of day in minutes when the charging session gets started. Min value: 0 (00:00), max value: 1439 (23:59) |
| allow_unbalanced_load | Boolean      | optional (default: false) | false        | If true, it allows higher powers for uniphase charging vehicles. If false, power is restricted to 4.5 kW.    |

The following table lists the `relationships` section of a `charge_price_bulk_request` object:

| **Name**          | **Type**               | **Presence** | **Example**                                       | **Description**                                                                                                                                             |
|-------------------|------------------------|--------------|---------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| charging_stations | Array of Relationships | required     | `[{"id": "some-uuid", type:"charging_station" }]` | Charging station for which the price should be calculated. Only charging stations of data_adapter chargeprice are allowed. Max. 50 stations can be defined. |
| tariffs           | Array of Relationships | required     | `[{"id": "some-uuid", type:"tariff" }]`           | Prices are calculated for the given tariffs. Up to 200 tariffs can be defined. See [GET v1/tariffs](../tariffs/index.md) for the valid options.              |
| vehicle           | Relationship           | required     | `{"id": "some-uuid", type:"car" }`                | Vehicle at charge. See [GET v1/vehicles](../vehicles/index.md) for the valid options.                                                                       |

## Response Body

### Bulk Price Preview Response

A response contains one `bulk_price_preview_response` object per station, provided in the request, if a price was found for this station.
The following table lists the `attributes` of these objects:

| **Name** | **Type** | **Example** | **Description**                                                               |
|----------|----------|-------------|-------------------------------------------------------------------------------|
| energy   | Float    | 25.0        | Energy to be charged (kWh) based on the request (vehicle + battery range)     |
| duration | Float    | 25.0        | Duration based on the request (vehicle + battery range)                       |
| price    | Float    | 30.0        | Price of a charging session with the cheapest tariff provided in the request. |
| currency | String   | "EUR"       | Currency in which the prices is returned.                                     |


| **Name**         | **Type**     | **Example**                                     | **Description**                             |
|------------------|--------------|-------------------------------------------------|---------------------------------------------|
| charging_station | Relationship | `{"id": "some-uuid", type:"charging_station" }` | Charging Station at which the price applies |


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
    "type": "bulk_price_preview_request",
    "attributes": {
      "battery_range": [20.0,80.0],
      "start_time": 720,
      "currency": "EUR",
      "allow_unbalanced_load": true,
    },
    "relationships": {
      "tariffs": {
        "data": [
          {
            "id": "14adf982-4e41-431b-833a-dfa89b484c75",
            "type": "tariff"
          }
        ]
      },
      "charging_stations": {
        "data": [
          {
            "id": "c6adf982-4e41-431b-833a-dfa89b484c75",
            "type": "charging_station"
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
      "type": "charge_price_preview",
      "attributes": {
        "energy": 38.5,
        "duration": 45.0,
        "price": 12.0,
        "currency": "EUR"
      },
      "relationships": {
        "charging_station": {
          "data": {
            "id": "c6adf982-4e41-431b-833a-dfa89b484c75",
            "type": "charging_station"
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
