# GET /v1/charging_stations/:charging_station_id

Get a single charging station.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`
* `Accept-Language: en`: [Valid Languages](../../enums.md). Used to localize the response, default language is `en`.

## Authorization Group

`ReadStations`

# Request Parameters

**charging_station_id** ID or EVSE ID of station that should be fetched.

### Query Parameters

The following query parameters are available:

| **Name**                 | **Type** | **Presence** | **Example**                   | **Description**                         |
|--------------------------|----------|--------------|-------------------------------|-----------------------------------------|
| fields[charging_station] | CSV      | *optional*    | charge_points.available_count | Defines if the availability is returned |

## Response Body

A response contains a `charging_station` object.
The following table lists it's `attributes`:

| **Name**                       | **Type**          | **Example**                | **Description**                                                                                                                         |
|--------------------------------|-------------------|----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| name                           | String            | "McDonalds Graz"           | Name of the charging station                                                                                                            |
| latitude                       | Float             | 43.345                     | Latitude component of the location                                                                                                      |
| longitude                      | Float             | 12.443                     | Longitude component of the location                                                                                                     |
| country                        | String            | "AT"                       | ISO 3166 country code of the location                                                                                                   |
| address                        | String            | "Teslastraße 1, 8010 Graz" | Address of the station                                                                                                                  |
| free_parking                   | Boolean or `null` | true                       | Parking at the station is free of charge (`null` = unknown)                                                                             |
| free_charging                  | Boolean or `null` | true                       | Charging at the station is free of charge (`null` = unknown)                                                                            |
| evse_ids                       | Array             | ["AT\*ION\*E1234"]         | All [EMI3 EVSE IDs](https://emi3group.com/wp-content/uploads/sites/5/2018/12/eMI3-standard-v1.0-Part-2.pdf) connected to this location. |
| charge_points                  | Array             | -                          | Charge points at this station, grouped by power and plug type                                                                           |
| charge_points.plug             | String            | "ccs"                      | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`)                                          |
| charge_points.power            | Float             | 50.0                       | Max. power                                                                                                                              |
| charge_points.count            | Integer           | 2                          | Total number of charge points of this type at the station                                                                               |
| charge_points.available_count  | Integer or `null` | 2                          | Number of charge points of this type at the station, which are ready to use and not occupied. (`null` = unknown)                        |
| parking_description            | String or `null`  | "0.5€/h"                   | Describes the parking situation with costs, times etc. in the provided language. Not available for all stations.                        |
| operator                       | Relationship      | -                          | Reference of the operator (CPO)                                                                                                         |
| meta.branding                  | Hash or null      | See below                  | Stations with an active promotion campaign.                                                                                             |
| meta.branding.map_pin_icon_url | String            | http://cp.com/logo.png     | URL to the map pin icon.                                                                                                                |

### Included Section

`company`

| **Name** | **Type** | **Example**      | **Description**                       |
|----------|----------|------------------|---------------------------------------|
| name     | String   | "McDonalds Graz" | Name of the charging station operator |


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
      "version": 1,
      "latitude": 10.0,
      "longitude": 20.0,
      "country": "AT",
      "address": "Stangersdorf-Gewerbegebiet 110 A, 8403 Lebring",
      "free_parking": true,
      "free_charging": false,
      "evse_ids": ["AT*ION*E1234"],
      "charge_points": [
        {
          "plug": "ccs",
          "power": 50.0,
          "count": 2,
          "available_count": 2
        }
      ],
      "parking_description": "0.5€/h"
    },
    "relationships": {
      "operator": {
        "data": {
          "type": "company",
          "id": "ae62cd2d-f29d-4107-b087-6d4f75261cca"
        }
      }
    },
    "meta": {
      "branding": {
          "map_pin_icon_url": "http://cp.com/logo.png"
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

A station with the given ID couldn't be found.

```json
{
  "errors": [
    {
      "status": "404",
      "code": "NOT_FOUND",
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
      "code": "INTERNAL_SERVER_ERROR",
      "title": "some error"
    }
  ]
}
```