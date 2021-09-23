# GET /v1/charging_stations/:charging_station_id

Get a single charging stations.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadStations`

## Response Body

A response contains a `charging_station` object.
The following table lists it's `attributes`:

| **Name**                      | **Type**          | **Example**                | **Description**                                                                                                  |
| ----------------------------- | ----------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| name                          | String            | "McDonalds Graz"           | Name of the charging station                                                                                     |
| latitude                      | Float             | 43.345                     | Latitude component of the location                                                                               |
| longitude                     | Float             | 12.443                     | Longitude component of the location                                                                              |
| country                       | String            | "AT"                       | ISO 3166 country code of the location                                                                            |
| address                       | String            | "Teslastraße 1, 8010 Graz" | Address of the station                                                                                           |
| free_parking                  | Boolean or `null` | true                       | Parking at the station is free of charge (`null` = unknown)                                                      |
| free_charging                 | Boolean or `null` | true                       | Charging at the station is free of charge (`null` = unknown)                                                     |
| charge_points                 | Array             | -                          | Charge points at this station, grouped by power and plug type                                                    |
| charge_points.plug            | String            | "ccs"                      | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`)                                                            |
| charge_points.power           | Float             | 50.0                       | Max. power                                                                                                       |
| charge_points.count           | Integer           | 2                          | Total number of charge points of this type at the station                                                        |
| charge_points.available_count | Integer or `null` | 2                          | Number of charge points of this type at the station, which are ready to use and not occupied. (`null` = unknown) |

## Example

### Request

```http
GET http://example-base-url.com/v1/charging_stations/20006f18-3ed4-4715-92b5-08e37e6dd18c
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 200 Ok

Body:
```json
{
  "data": {
    "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
    "type": "charging_station",
    "attributes": {
      "name": "Spar",
      "latitude": 10.0,
      "longitude": 20.0,
      "country": "AT",
      "address": "Stangersdorf-Gewerbegebiet 110 A, 8403 Lebring",
      "free_parking": true,
      "free_charging": false,
      "charge_points": [
        {
          "plug": "ccs",
          "power": 50.0,
          "count": 2,
          "available_count": 2
        }
      ]
    },
    "relationships": {
      "operator": {
        "data": {
          "type": "company",
          "id": "ae62cd2d-f29d-4107-b087-6d4f75261cca"
        }
      }
    }
  },
  "included": [
    {
      "id": "ae62cd2d-f29d-4107-b087-6d4f75261cca",
      "type": "company",
      "attributes": {
        "name": "ELLA"
      }
    }
  ]
}

```

##### 400 Bad Request

Client provided invalid request.

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

##### 404 Not Found

A station with the given ID couldn't be found.

```json
{
  "errors": [
    {
      "status": "404",
      "title": "charging station <id>"
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