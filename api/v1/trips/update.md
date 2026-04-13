# PATCH /v1/trips/:trip_id

Get a trip with one or multiple stops and get the charging stops on the way.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Authorization: Bearer <user_access_token>`
* `Content-Type: application/json`

## Authorization Group

`CreateTrips`

## Request Body

The following fields are to be sent in the request body, in the `attributes` section of a `trip` object:

| **Name**                   | **Type** | **Presence** | **Example**                            | **Description**                                                                                 |  |
|----------------------------|----------|--------------|----------------------------------------|-------------------------------------------------------------------------------------------------|--|
| charge_stop                |          | optional     | -                                      | Replace the selected charging station for a scheduled charging stop with another.               |  |
| charge_stop.id             | String   | required     | "21adf982-4e41-431b-833a-dfa89b484c75" | Charge Stop ID where the selected charging station should be replaced.                          |  |
| charge_stop.new_station_id | String   | required     | "4fadf982-4e41-431b-833a-dfa89b484c45" | Station that should be selected as a charge stop instead.                                       |  |
| is_saved                   | Boolean  | required     | true                                   | Indicates whether the trip is saved. Send `true` to save it, and `false` to delete it.          |  |

## Example

### Request

```http
PATCH http://example-base-url.com/v1/trips/01adf982-4e41-431b-833a-dfa89b484c19
Content-Type: application/json
Api-Key: my-secret-key
```

```json
{
  "data": {
    "type": "trip_update",
    "attributes": {
      "charge_stop": {
        "id": "21adf982-4e41-431b-833a-dfa89b484c75",
        "new_station_id": "4fadf982-4e41-431b-833a-dfa89b484c45"
      },
      "is_saved": true
    }
  }
}
```

## Response

See [Create Trips API](create.md).

#### 400 Bad Request (Too Many Saved Trips)

The user already has the maximum number of saved trips (200) and cannot save
more trips until they delete some of the existing saved trips.

```json
{
  "errors": [
    {
      "status": "400",
      "code": "TOO_MANY_SAVED_TRIPS",
      "title": "Too many saved trips"
    }
  ]
}
```