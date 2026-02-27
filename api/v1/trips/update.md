# PATCH /v1/trips/:trip_id

Get a trip with one or multiple stops and get the charging stops on the way.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`CreateTrips`




## Request Body

The following fields are to be sent in the request body, in the `attributes` section of a `trip` object:

| **Name**                           | **Type** | **Presence** | **Example**                            | **Description**                                                                                 |
|------------------------------------|----------|--------------|----------------------------------------|-------------------------------------------------------------------------------------------------|
| selected_route_id                  | String   | optional     | "14adf982-4e41-431b-833a-dfa89b484c15" | Change the selected route to another. It will trigger the calculation of the alternative route. |
| replace_charge_stop                |          | optional     | -                                      | Replace a scheduled charging stop with another.                                                 |
| replace_charge_stop.old_station_id | String   | required     | "21adf982-4e41-431b-833a-dfa89b484c75" | Station that should be removed as a charge stop.                                                |
| replace_charge_stop.new_station_id | String   | required     | "4fadf982-4e41-431b-833a-dfa89b484c45" | Station that should be added as a charge stop instead.                                          |

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
      "selected_route_id": "14adf982-4e41-431b-833a-dfa89b484c15",
      "replace_charge_stop": {
        "old_station_id": "21adf982-4e41-431b-833a-dfa89b484c75",
        "new_station_id": "4fadf982-4e41-431b-833a-dfa89b484c45"
      }
    }
  }
}
```

## Response

See [Create Trips API](create.md).