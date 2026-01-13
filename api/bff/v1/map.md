# POST /bff/v1/map

This Backend-For-Frontend (BFF) endpoint allows clients to retrieve a list of charging stations along with prices that should be displayed on a map.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadCosts`

## Request

The following fields are to be sent in the request body, in the `attributes` section of a `map_request` object:

| **Name**                       | **Type**      | **Presence** | **Example**        | **Description**                                                                                                                                                                                          |
|--------------------------------|---------------|--------------|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| battery_range                  | Array[Float]  | required     | [20.0,80.0]        | Array with 2 values, that defines the battery start and end values for the charge in percentage.                                                                                                         |
| currency                       | String        | required     | "EUR"              | Currency in which the prices should be returned. Possible values are listed at the [eurofxref]                                                                                                           |
| start_time                     | Integer       | required     | 720 (=12:00)       | Time of day in minutes when the charging session gets started. Min value: 0 (00:00), max value: 1439 (23:59)                                                                                             |
| allow_unbalanced_load          | Boolean       | required     | false              | If true, it allows higher powers for uniphase charging vehicles. If false, power is restricted to 4.5 kW.                                                                                                |
| include_direct_payment         | Boolean       | required     | false              | If true, all direct payment tariffs are also considered for the prices.                                                                                                                                  |
| bbox.latitude_gte              | Float         | required     | 12.345             | Inclusive lower bound of the latitude location component to find stations.                                                                                                                               |
| bbox.latitude_lte              | Float         | required     | 12.345             | Inclusive upper bound of the latitude location component to find stations.                                                                                                                               |
| bbox.longitude_gte             | Float         | required     | 12.345             | Inclusive lower bound of the longitude location component to find stations.                                                                                                                              |
| bbox.longitude_lte             | Float         | required     | 12.345             | Inclusive upper bound of the longitude location component to find stations.                                                                                                                              |
| zoom_level                     | Float         | required     | 8.5                | Current Zoom Level of the Map                                                                                                                                                                            |
| user_location                  | Object        | optional     | 12.345             | Only send this attribute, if the user location is known.                                                                                                                                                 |
| user_location.longitude        | Float         | required     | 12.345             | Current Location (Longitude) of the user.                                                                                                                                                                |
| user_location.latitude         | Float         | required     | 12.345             | Current Location (Latitude) of the user.                                                                                                                                                                 |
| filter                         | Object        | optional     | 12.345             | Filter options for the map.                                                                                                                                                                              |
| filter.charge_points_power_gte | Float         | optional     | 12.345             | Only return charging stations with a power greater than or equal to this value.                                                                                                                          |
| filter.charge_points_power_lte | Float         | optional     | 12.345             | Only return charging stations with a power less than or equal to this value.                                                                                                                             |
| filter.price_lte               | Float         | optional     | 30.0               | Only display stations with a maximum price defined here.                                                                                                                                                 |
| filter.facilities              | Array         | optional     | ["supermarket"]    | Only stations that have at least one of these facilities close nearby. [See supported values](../../enums.md#facilities)                                                                                 |
| filter.supported_by_my_tariffs | Boolean       | optional     | false              | Only stations that are supported by the tariffs sent in the request.                                                                                                                                          |
| user_products                  | Array         | required     | ["mobile_premium"] | List of products the user has activated. [See supported values](../../enums.md#user-products). If a user doesn't have a chargeprice account, but has an active premium subscription, also send "mobile_premium". |
| price_display_mode             | String        | required     | "total_cost"       | Define what kind of price should be in the response: `total_cost`, `average_price_per_kwh`                                                                                                               |

The following table lists the `relationships` section of a `charge_price_bulk_request` object:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                                                                                                                 |
|----------|------------------------|--------------|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| tariffs  | Array of Relationships | required     | `[{"id": "some-uuid", type:"tariff" }]` | Prices are calculated for the given tariffs. Up to 200 tariffs can be defined. See [GET v1/tariffs](../tariffs/index.md) for the valid options. |
| vehicle  | Relationship           | required     | `{"id": "some-uuid", type:"car" }`      | Vehicle at charge. See [GET v1/vehicles](../vehicles/index.md) for the valid options.                                                           |

## Response Body

A response contains one `poi_preview` object per station that should be displayed on the map.
The following table lists the `attributes` of these objects:

| **Name**         | **Type**       | **Example**                   | **Description**                                                                                                                                                                         |
|------------------|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| longitude        | Float          | 11.56789                      | Longitude of the charging station                                                                                                                                                       |
| latitude         | Float          | 48.12345                      | Latitude of the charging station                                                                                                                                                        |
| price            | Float or null  | 30.0                          | Price of the station. If `null`, no price should be shown. Depending on the request parameters this is the total cost or the average price per kWh already. No need modify this number. |
| power            | Float          | 22.0                          | Power of the charging station in kW.                                                                                                                                                    |
| charge_points    | Float or null  | 4                             | Number of charge points at this location. If `null`, the number should not be displayed on the pin.                                                                                     |
| currency         | String         | "EUR"                         | Currency to be displayed on the map pin.                                                                                                                                                |
| promoted         | Boolean        | -                             | If `true` the golden map pin with bigger size should be used for this location.                                                                                                         |
| map_pin_icon_url | String or null | "http://example.com/icon.png" | URL of the icon to be used for the map pin, instead of the power level.                                                                                                                 |


| **Name**         | **Type**     | **Example**                                     | **Description**                                               |
|------------------|--------------|-------------------------------------------------|---------------------------------------------------------------|
| charging_station | Relationship | `{"id": "some-uuid", type:"charging_station" }` | ID of the Charging Station that is related to the poi preview |


## Example

### Request

```http
POST http://example-base-url.com/bff/v1/map
Content-Type: application/json
Api-Key: my-secret-key
```

```json
{
  "data": {
    "type": "map_request",
    "attributes": {
      "battery_range": [20.0,80.0],
      "start_time": 720,
      "currency": "EUR",
      "allow_unbalanced_load": true,
      "include_direct_payment": true,
      "bbox": {
        "longitude_gte": 11.4,
        "longitude_lte": 11.6,
        "latitude_gte": 48.1,
        "latitude_lte": 48.2
      },
      "zoom_level": 12.4,
      "user_location": {
        "latitude": 48.137154,
        "longitude": 11.576124
      },
      "filter": {
        "charge_points_power_gte": 11,
        "charge_points_power_lte": 350,
        "facilities": ["restaurant","supermarket"]
      },
      "user_products": ["mobile_premium"],
      "price_display_mode": "total_cost"
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
      "type": "poi_preview",
      "attributes": {
        "longitude": 11.56789,
        "latitude": 48.12345,
        "price": 12.0,
        "power": 150,
        "charge_points_count": 4,
        "currency": "EUR",
        "promoted": true,
        "map_pin_icon_url": "https://example.com/pin_icon.png",
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
