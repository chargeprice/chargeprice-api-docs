# GET /v1/charging_stations

Find charging stations in a given area.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadStations`

## Request

### Filters

The following query filter parameters are available. They basically represent a bounding box in which the stations need to be.

| **Name**                | **Type** | **Presence** | **Example**                          | **Description**                                                          |
| ----------------------- | -------- | ------------ | ------------------------------------ | ------------------------------------------------------------------------ |
| latitude.gte            | Float    | mandatory    | 12.345                               | Inclusive lower bound of the latitude location component of the station  |
| latitude.lte            | Float    | mandatory    | 12.345                               | Inclusive upper bound of the latitude location component of the station  |
| longitude.gte           | Float    | mandatory    | 12.345                               | Inclusive lower bound of the longitude location component of the station |
| longitude.lte           | Float    | mandatory    | 12.345                               | Inclusive upper bound of the longitude location component of the station |
| charge_points.plug.in   | CSV      | optional     | "ccs,type2"                          | Only return stations that support this plug                              |
| charge_points.power.gte | Float    | optional     | 50                                   | Minimum power of a plug                                                  |
| operator.id             | String   | optional     | ae62cd2d-f29d-4107-b087-6d4f75261cca | Only stations of this operator                                           |
| free_charging           | Boolean  | optional     | true                                 | Only stations with free charging                                         |
| free_parking            | Boolean  | optional     | true                                 | Only stations with free parking                                          |


## Response Body

A response contains 0 to max. 400 `charging_station` objects.
The following table lists the `attributes` of these objects:

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
| charge_points.plug            | String            | "ccs"                      | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`)                   |
| charge_points.power           | Float             | 50.0                       | Max. power                                                                                                       |
| charge_points.count           | Integer           | 2                          | Total number of charge points of this type at the station                                                        |
| charge_points.available_count | Integer or `null` | 2                          | Number of charge points of this type at the station, which are ready to use and not occupied. (`null` = unknown) |

# Meta
- disabled_going_electric_countries: Countries where the Chargeprice Data has
  likely better quality than the GoingElectric data and hence the GoingElectric
  data shouldn't be shown at all

## Example

### Request

```http
GET http://example-base-url.com/v1/charging_stations?filter[latitude.gte]=9.0&filter[latitude.lte]=11.0&filter[longitude.gte]=19.0&filter[longitude.lte]=21.0
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 200 Ok

Body:
```json
{
  "data": [
    {
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
    }
  ],
  "included": [
    {
      "id": "ae62cd2d-f29d-4107-b087-6d4f75261cca",
      "type": "company",
      "attributes": {
        "name": "ELLA"
      }
    }
  ],
  "meta": {
    "disabled_going_electric_countries": [
      "Frankreich"
    ]
  }
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