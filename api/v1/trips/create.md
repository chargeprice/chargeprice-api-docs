# POST /v1/trips

Create a trip with one or multiple stops and get the charging stops on the way.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`CreateTrips`

## Request

The following fields are to be sent in the request body, in the `attributes` section of a `trip` object:

| **Name**                    | **Type** | **Presence** | **Example**        | **Description**                                                                                                                                                                                                  |
|-----------------------------|----------|--------------|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| stops                       | Array    | required     | -                  | Stops during the trip the given order. At least 2 stops (start and destination) need to be defined.                                                                                                              |
| stops.longitude             | Float    | required     | 13.345             | Longitude Location of the stop                                                                                                                                                                                   |
| stops.latitude              | Float    | required     | 43.456             | Latitude Location of the stop                                                                                                                                                                                    |
| stops.name                  | String   | required     | "Vienna"           | Name of the stop.                                                                                                                                                                                                |
| currency                    | String   | optional     | "EUR"              | Currency of the routing charging costs. Default: EUR                                                                                                                                                             |
| route_strategy              | String   | optional     | "fastest"          | Either "fastest" or "shortest". Default: fastest                                                                                                                                                                 |
| state_of_charge_start       | Float    | optional     | 0.9                | State of Charge at Start in Percentage (90%=0.9). Default: 90%                                                                                                                                                   |
| state_of_charge_destination | Float    | optional     | 0.2                | Min. State of Charge when reaching destination in Percentage (20% = 0.2). Default: 20%                                                                                                                           |
| vehicle_consumption         | Float    | optional     | 20                 | Average consumption of the vehicle on the trip. Default: standard consumption of the vehicle.                                                                                                                    |
| exclude                     | Array    | optional     | ["motorway"]       | Exclude specific roads. Possible values: "motorway", "toll". Default: nothing excluded.                                                                                                                          |
| user_products               | Array    | required     | ["mobile_premium"] | List of products the user has activated. [See supported values](../../enums.md#user-products). If a user doesn't have a chargeprice account, but has an active premium subscription, also send "mobile_premium". |
| price_display_mode          | String   | required     | "total_cost"       | Define what kind of price should be in the response: `total_cost`, `average_price_per_kwh`                                                                                                                       |

The following table lists the `relationships` section of a `trip` object:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                                                                                                                 |
|----------|------------------------|--------------|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| tariffs  | Array of Relationships | required     | `[{"id": "some-uuid", type:"tariff" }]` | Prices are calculated for the given tariffs. Up to 200 tariffs can be defined. See [GET v1/tariffs](../tariffs/index.md) for the valid options. |
| vehicle  | Relationship           | required     | `{"id": "some-uuid", type:"car" }`      | Vehicle at charge. See [GET v1/vehicles](../vehicles/index.md) for the valid options.                                                           |

## Response Body

The following table lists the `attributes` of a `trip` response:

| **Name**                             | **Type**        | **Example**                            | **Description**                                                                                                                                                                         |
|--------------------------------------|-----------------|----------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| selected_route_id                    | String          | "46adf982-4e41-431b-833a-dfa89b484c71" | ID of the currently selected route.                                                                                                                                                     |
| routes                               | Array           |                                        | List of possible routes                                                                                                                                                                 |
| routes.id                            | String          | "46adf982-4e41-431b-833a-dfa89b484c71" | Unique ID of the route.                                                                                                                                                                 |
| total_distance                       | Integer         | 431450                                 | Total distance of the trip (in meters).                                                                                                                                                 |
| total_duration                       | Integer         | 152                                    | Total trip time incl. driving and charging (in minutes)                                                                                                                                 |
| total_driving_duration               | Integer         | 102                                    | Total time driving during the trip (in minutes)                                                                                                                                         |
| total_charging_duration              | Integer         | 50                                     | Total time charging during the trip (in minutes)                                                                                                                                        |
| charge_stop_count                    | Integer         | 1                                      | Number of charging stops on the trip                                                                                                                                                    |
| total_charging_cost                  | Float           | 37.2                                   | Total cost of all charging sessions on the trip.                                                                                                                                        |
| currency                             | String          | "EUR"                                  | Currency of the charging cost                                                                                                                                                           |
| geometry_segments                    | Array           |                                        | List of polylines that represent the whole route path of the trip.                                                                                                                      |
| geometry_segments.polyline           | String          | "k~myHtp^KeA_@aEACCW"                  | Polyline coordinates encoded as a string in the [Google Polyline format](https://developers.google.com/maps/documentation/utilities/polylinealgorithm)                                  |
| geometry_segments.battery_level      | String          | "normal"                               | The color of a polyline is dependent on the battery level. Possible values: normal, warning, low, critical, empty.                                                                      |
| steps                                | Array           |                                        | See below for details.                                                                                                                                                                  |
| stations_on_route                    | Array           |                                        |                                                                                                                                                                                         |
| stations_on_route.station_id         | String          | 46adf982-4e41-431b-833a-dfa89b484c71   | ID of the related charging station                                                                                                                                                      |
| stations_on_route.longitude          | Float           | 11.56789                               | Longitude of the charging station                                                                                                                                                       |
| stations_on_route.latitude           | Float           | 48.12345                               | Latitude of the charging station                                                                                                                                                        |
| stations_on_route.power              | Float           | 150.0                                  | Highest power of the charging station in kW.                                                                                                                                            |
| stations_on_route.charge_point_count | Integer or null | 3                                      | Number of charge points at this location. If `null`, the number should not be displayed on the pin.                                                                                     |
| stations_on_route.price              | Float or null   | 30.0                                   | Price of the station. If `null`, no price should be shown. Depending on the request parameters this is the total cost or the average price per kWh already. No need modify this number. |
| stations_on_route.promoted           | Boolean         | true                                   | If `true` the golden map pin with bigger size should be used for this location.                                                                                                         |
| stations_on_route.map_pin_icon_url   | String          | "http://example.com/icon.png"          | URL of the icon to be used for the map pin, instead of the power level.                                                                                                                 |
| stations_on_route.currency           | String          | "EUR"                                  | Currency of the price                                                                                                                                                                   |
| status                               | String          | "completed"                            | "pending": Still calculating, fetch the result in a few seconds again. "completed": Calculation complete.                                                                               |


### Steps & Types

The stops of the route are listed in the order they happen during the trip.
The following types exist:

#### Stop

The start or destination of the trip or planned intermediate stop without charging.

| **Name**        | **Type** | **Example**                            | **Description**                                         |
|-----------------|----------|----------------------------------------|---------------------------------------------------------|
| type            | String   | "stop"                                 |                                                         |
| id              | String   | "46adf982-4e41-431b-833a-dfa89b484c71" | ID of the stop. Needed only for the Update API.         |
| name            | String   | Vienna                                 | Name of the location                                    |
| longitude       | Float    | 11.56789                               | Longitude coordinate                                    |
| latitude        | Float    | 48.12345                               | Latitude coordinate                                     |
| state_of_charge | Float    | 0.5                                    | State of Charge when reaching this stop. 0.5=50%        |
| stop_type       | String   | "start"                                | Possible values: "start", "intermediate", "destination" |

#### Charge Stop

When you need to charge during the trip.

| **Name**              | **Type** | **Example**                            | **Description**                                         |
|-----------------------|----------|----------------------------------------|---------------------------------------------------------|
| type                  | String   | "charge_stop"                          |                                                         |
| station_id            | String   | "46adf982-4e41-431b-833a-dfa89b484c71" | ID of the related charging station                      |
| station_name          | String   | McDonalds Vienna South                 | Name of the charging station                            |
| operator_name         | String   | IONITY                                 | Name of the charge point operator (CPO)                 |
| longitude             | Float    | 11.56789                               | Longitude of the charging station                       |
| latitude              | Float    | 48.12345                               | Latitude of the charging station                        |
| power                 | Float    | 150.0                                  | Highest power of the charging station in kW.            |
| charge_point_count    | Integer  | 4                                      | Number of charge points at this location.               |
| duration              | Integer  | 32                                     | Time charging at this stop.                             |
| cost                  | Float    | 37.2                                   | Total cost of the whole charging session.               |
| currency              | String   | "EUR"                                  | Currency of the cost.                                   |
| state_of_charge_start | Float    | 0.3                                    | State of Charge when reaching this charge stop. 0.3=30% |
| state_of_charge_end   | Float    | 0.8                                    | State of Charge when leaving the charge stop. 0.8=80%   |

#### Route Leg

When you are currently driving.

| **Name** | **Type** | **Example** | **Description**                                                        |
|----------|----------|-------------|------------------------------------------------------------------------|
| type     | String   | "route_leg" |                                                                        |
| distance | Integer  | 131450      | Distance from the previous to the next (charge) stop (in meters).      |
| duration | Integer  | 87          | Driving time from the previous to the next (charge) stop (in minutes). |

## Example

### Request

```http
POST http://example-base-url.com/v1/trips
Content-Type: application/json
Api-Key: my-secret-key
```

```json
{
  "data": {
    "type": "trip",
    "attributes": {
      "stops": [
        {
          "longitude": 13.345,
          "latitude": 43.456,
          "name": "Vienna"
        },
        {
          "longitude": 16.373,
          "latitude": 48.208,
          "name": "Graz"
        }
      ],
      "currency": "EUR",
      "route_strategy": "fastest",
      "state_of_charge_start": 0.9,
      "state_of_charge_destination": 0.2,
      "vehicle_consumption": 20,
      "exclude": ["motorway"],
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

#### 201 Created

Body:
```json

{
  "data": {
    "id": "16adf982-4e41-431b-833a-dfa89b484c71",
    "type": "trip",
    "attributes": {
      "selected_route_id": "26adf982-4e41-431b-833a-dfa89b484c71",
      "routes": [
        {
          "id": "26adf982-4e41-431b-833a-dfa89b484c71",
          "total_distance": 453845,
          "total_duration": 494,
          "total_driving_duration": 344,
          "total_charging_duration": 150,
          "total_charging_cost": 37.2,
          "currency": "EUR",
          "charge_stop_count": 3,
          "geometry_segments": [
            {
              "polyline": "k~myHtp^KeA_@aEACCW",
              "battery_level": "normal"
            }
          ],
          "steps": [
            {
              "type": "stop",
              "name": "Graz",
              "longitude": 15.43528,
              "latitude": 47.06583,
              "state_of_charge": 0.9,
              "stop_type": "start"
            },
            {
              "type": "route_leg",
              "distance": 253845,
              "duration": 120
            },
            {
              "type": "charge_stop",
              "station_id": "36adf982-4e41-431b-833a-dfa89b484c71",
              "longitude": 13.383,
              "latitude": 47.42122,
              "station_name": "St. Johann",
              "operator_name": "Tesla",
              "power": 50.0,
              "charge_point_count": 2,
              "duration": 50,
              "cost": 30.0,
              "currency": "EUR",
              "state_of_charge_start": 0.2,
              "state_of_charge_end": 0.8,
            },
            {
              "type": "route_leg",
              "distance": 123845,
              "duration": 80
            },
            {
              "type": "stop",
              "name": "St. Johann",
              "longitude": 13.237589,
              "latitude": 47.380604,
              "state_of_charge": 0.0,
              "stop_type": "intermediate"
            },
            {
              "type": "route_leg",
              "distance": 12345,
              "duration": 45
            },
            {
              "type": "charge_stop",
              "station_id": "46adf982-4e41-431b-833a-dfa89b484c71",
              "longitude": 12.3662,
              "latitude": 47.8152,
              "station_name": "Chiemsee",
              "operator_name": "Tesla",
              "power": 50.0,
              "charge_point_count": 2,
              "duration": 50,
              "cost": 30.0,
              "currency": "EUR",
              "state_of_charge_start": 0.2,
              "state_of_charge_end": 0.8,
            },
            {
              "type": "route_leg",
              "distance": 54213,
              "duration": 100
            },
            {
              "type": "stop",
              "name": "Innsbruck",
              "longitude": 11.396641,
              "latitude": 47.260277,
              "state_of_charge": 0.2,
              "stop_type": "destination"
            }
          ],
          "stations_on_route": [
            {
              "id": "36adf982-4e41-431b-833a-dfa89b484c71",
              "longitude": 13.383,
              "latitude": 47.42122,
              "station_name": "St. Johann",
              "operator_name": "Tesla",
              "power": 50.0,
              "charge_point_count": 2,
              "price": 30.0,
              "currency": "EUR",
              "promoted": true,
              "map_pin_icon_url": "http://cp.com/logo.png",
            },
          ],
          "status": "completed"
        }
      ]
    }
  }
}
```

##### 400 Bad Request (No Route)

No route could be calculated for the given parameters.
Either there is no road or no charging stations could be found to charge.

```json
{
  "errors": [
    {
      "status": "400",
      "code": "NO_ROUTE_FOUND",
      "title": "No currency with iso ATS"
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

##### 403 Forbidden (Premium Required)

* A premium subscription is required for planning this trip.

```json
{
  "errors": [
    {
      "status": "403",
      "code": "PREMIUM_REQUIRED",
      "title": "..."
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
