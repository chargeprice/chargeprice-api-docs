# PUT /v2/charging_stations/:charging_station_id

Create or update a charging station, if already existing.
Only charging stations with source "manual" can be created and modified.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`WriteAllStations`

## Request

The body can have the following attributes:

| **Name**                         | **Type**          | **Presence** | **Example**                            | **Description**                                                                                                                         |
|----------------------------------|-------------------|--------------|----------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| id                               | UUID              | required     | "1e49b853-36fc-47ed-9826-97828b5b2fdd" | Create: Client-side generated UUID, Update: Existing Resource ID                                                                        |
| version                          | Integer           | required     | 1                                      | Lock version. Needs to be 1 on create and for update 1 higher than the current version.                                                 |
| type                             | String            | required     | "charging_station"                     | Always "charging_station".                                                                                                              |
| name                             | String            | required     | "McDonalds Graz"                       |                                                                                                                                         |
| latitude                         | Float             | required     | 43.345                                 | Latitude component of the location                                                                                                      |
| longitude                        | Float             | required     | 12.443                                 | Longitude component of the location                                                                                                     |
| country                          | String            | required     | "AT"                                   | ISO 3166 country code of the location                                                                                                   |
| address                          | String            | required     | "Teslastraße 1, 8010 Graz"             | Address of the station                                                                                                                  |
| status                           | String            | required     | "operational"                          | Possible values: `operational`, `out_of_order`, `removed`, `under_construction`                                                         |
| free_parking                     | Boolean or `null` | required     | true                                   | Parking at the station is free of charge (`null` = unknown)                                                                             |
| free_charging                    | Boolean or `null` | required     | true                                   | Charging at the station is free of charge (`null` = unknown)                                                                            |
| charge_points                    | Array             | required     | -                                      | Charge points at this station, grouped by power and plug type                                                                           |
| charge_points.plug               | String            | required     | "ccs"                                  | Type of plug. [See Supported values](../../enums.md#plugs)                                                                              |
| charge_points.power              | Float             | required     | 50.0                                   | Max. power                                                                                                                              |
| charge_points.count              | Integer           | required     | 2                                      | Total number of charge points of this type at the station                                                                               |
| charge_points.evse_ids           | Array             | required     | ["AT\*ION\*E1234"]                     | All [EMI3 EVSE IDs](https://emi3group.com/wp-content/uploads/sites/5/2018/12/eMI3-standard-v1.0-Part-2.pdf) connected to this location. |
| charge_points.evse_ids           | Array             | required     | ["AT\*ION\*E1234"]                     | All [EMI3 EVSE IDs](https://emi3group.com/wp-content/uploads/sites/5/2018/12/eMI3-standard-v1.0-Part-2.pdf) connected to this location. |
| operator                         | Relationship      | required     | -                                      | Reference of the operator (CPO)                                                                                                         |
| meta.replaces_charging_staton_id | String            | optional     | "1e49b853-36fc-47ed-9826-97828b5b2fdd" | If the current station replaces a station from another source, the station with the given ID will be deleted.                           |

## Response Body

A response contains a `charging_station` object.
The following table lists it's `attributes`:

| **Name**                      | **Type**          | **Example**                | **Description**                                                                                                                         |
|-------------------------------|-------------------|----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| name                          | String            | "McDonalds Graz"           | Name of the charging station                                                                                                            |
| latitude                      | Float             | 43.345                     | Latitude component of the location                                                                                                      |
| longitude                     | Float             | 12.443                     | Longitude component of the location                                                                                                     |
| country                       | String            | "AT"                       | ISO 3166 country code of the location                                                                                                   |
| address                       | String            | "Teslastraße 1, 8010 Graz" | Address of the station                                                                                                                  |
| free_parking                  | Boolean or `null` | true                       | Parking at the station is free of charge (`null` = unknown)                                                                             |
| free_charging                 | Boolean or `null` | true                       | Charging at the station is free of charge (`null` = unknown)                                                                            |
| evse_ids                      | Array             | ["AT\*ION\*E1234"]         | All [EMI3 EVSE IDs](https://emi3group.com/wp-content/uploads/sites/5/2018/12/eMI3-standard-v1.0-Part-2.pdf) connected to this location. |
| charge_points                 | Array             | -                          | Charge points at this station, grouped by power and plug type                                                                           |
| charge_points.plug            | String            | "ccs"                      | Type of plug. [See Supported values](../../enums.md#plugs)                                                                              |
| charge_points.power           | Float             | 50.0                       | Max. power                                                                                                                              |
| charge_points.count           | Integer           | 2                          | Total number of charge points of this type at the station                                                                               |
| charge_points.available_count | Integer or `null` | 2                          | Number of charge points of this type at the station, which are ready to use and not occupied. (`null` = unknown)                        |
| version                       | Integer           | 1                          | Current lock version                                                                                                                    |
| operator                      | Relationship      | -                          | Reference of the operator (CPO)                                                                                                         |

Timestamp = Millis since 1.1.1970

## Example

### Request

```http
PUT http://example-base-url.com/v1/charging_stations/20006f18-3ed4-4715-92b5-08e37e6dd18c
Content-Type: application/json
Api-Key: my-secret-key

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
      "status": "operational",
      "charge_points": [
        {
          "plug": "ccs",
          "power": 50.0,
          "count": 2,
          "evse_ids": ["AT*ION*E1234"]
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
    },
    "meta": {
      "replaces_charging_staton_id": "1a006f18-3ed4-4715-92b5-08e37e6dd183" 
    }
  }
}

```

### Response

#### 200 Ok (after update)
#### 201 Created (after create)

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
          "available_count": null
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

##### 409 Conflict

An Invalid version was sent in the request.

```json
{
  "errors": [
    {
      "status": "409",
      "code": "VERSION_CONFLICT",
      "title": "Version conflict"
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