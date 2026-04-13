# GET /v1/users/:user_id/trips

Get a list of saved trips for a user, sorted by the latest created trip first.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Authorization: Bearer <user_access_token>`
* `Content-Type: application/json`

## Authorization Group

`CreateTrips`

### Response

Limited subset of [Create Trips API](../../trips/create.md) response.
Only the fields shown in the example below are included.

#### 200 OK

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
          "steps": [
            {
              "type": "stop",
              "name": "Graz",
              "longitude": 15.43528,
              "latitude": 47.06583,
              "state_of_charge": 0.9,
              "stop_type": "start"
            }
            {
              "type": "stop",
              "name": "Innsbruck",
              "longitude": 11.396641,
              "latitude": 47.260277,
              "state_of_charge": 0.2,
              "stop_type": "destination"
            }
          ]
        }
      ]
    }
  }
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


