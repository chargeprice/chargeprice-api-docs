# GET /v1/charging_stations

Find charging stations in a given area.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadStations`

## Request

### Query Parameters

The following query parameters are available.

| **Name**                           | **Type**         | **Presence** | **Example**                                                               | **Description**                                                                                                    |
| ---------------------------------- | ---------------- | ------------ | ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| filter[id]                         | CSV              | optional*    | 20006f18-3ed4-4715-92b5-08e37e6dd18c,20006f18-3ed4-4715-92b5-08e37e6dd18c | A list of max. 50 Charging Station IDs that can be fetched                                                         |
| filter[latitude.gte]               | Float            | optional*    | 12.345                                                                    | Inclusive lower bound of the latitude location component of the station for a bounding box search                  |
| filter[latitude.lte]               | Float            | optional*    | 12.345                                                                    | Inclusive upper bound of the latitude location component of the station for a bounding box search                  |
| filter[longitude.gte]              | Float            | optional*    | 12.345                                                                    | Inclusive lower bound of the longitude location component of the station for a bounding box search                 |
| filter[longitude.lte]              | Float            | optional*    | 12.345                                                                    | Inclusive upper bound of the longitude location component of the station for a bounding box search                 |
| filter[longitude]                  | Float            | optional*    | 12.345                                                                    | Longitude location component of the center of the radius search                                                    |
| filter[latitude]                   | Float            | optional*    | 12.345                                                                    | Latitude location component of the center of the radius search                                                     |
| filter[radius]                     | Integer          | optional*    | 1000                                                                      | Radius (in meters) in which stations should be searched                                                            |
| filter[charge_points.plug.in]      | CSV              | optional     | "ccs,type2"                                                               | Only return stations that support this plug                                                                        |
| filter[charge_points.power.gte]    | Float            | optional     | 50                                                                        | Minimum power of a plug                                                                                            |
| filter[operator.id]                | String           | optional     | ae62cd2d-f29d-4107-b087-6d4f75261cca                                      | Only stations of this operator                                                                                     |
| filter[operator.supported_emps.id] | CSV              | optional     | ce12cd2d-f29d-3107-8087-6d4f75261cc0,4e12cd2d-d29d-3107-8087-6d4f75261cc7 | Only stations where these EMPs (NOT tariffs!) are supported. Extract the EMP ID of a tariff to use this filter!    |
| filter[free_charging]              | Boolean          | optional     | true                                                                      | Only stations with free charging                                                                                   |
| filter[free_parking]               | Boolean          | optional     | true                                                                      | Only stations with free parking                                                                                    |
| fields[company]                    | CSV              | optional     | "name,supported_emps"                                                     | If set, only the given fields of a company are returned. Default: "name". Allowed Values: "name", "supported_emps" |
| page[size]                         | Positive Integer | optional     | 2                                                                         | Max no. of stations in the current response. Default: 400, Max value: 400                                          |
| page[number]                       | Positive Integer | optional     | 10                                                                        | Current page number. Default: 1                                                                                    |

#### Pagination

Pagination caps the number of items in the response. It is applied all the time.

If no pagination parameters are provided, the default values are applied to the
request. If the number of items exceeds the page size, `meta.more_available`
will be `true` and you can get more results via a second request and so on.

Example: First request `page[size]=400&page[number]=1` returns
`meta.more_available=true`. To fetch more stations, the second page needs to get
requested: `page[size]=400&page[number]=2`. If this then returns
`meta.more_available=false` you know that you have all stations. Otherwise you
can continue with page 3.

#### Type of Search

You always need to perform a specific type of charging station search. The
following are supported and defined by the combination of filters:

* Bounding Box: Search within a rectangle
  * latitude.gte
  * latitude.lte
  * longitude.gte
  * longitude.lte
* Radius: Search in a radius, given a center
  * latitude
  * longitude
  * radius
* ID: Get all stations with the given ids
  * id

These searches can't be combined, they are mutually exclusive (XOR)! 
## Response Body

A response contains 0 to max. 400 `charging_station` objects. The following
table lists the `attributes` of these objects:

| **Name**                      | **Type**          | **Example**                | **Description**                                                                                                                         |
| ----------------------------- | ----------------- | -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| name                          | String            | "McDonalds Graz"           | Name of the charging station                                                                                                            |
| latitude                      | Float             | 43.345                     | Latitude component of the location                                                                                                      |
| longitude                     | Float             | 12.443                     | Longitude component of the location                                                                                                     |
| country                       | String            | "AT"                       | ISO 3166 country code of the location                                                                                                   |
| address                       | String            | "Teslastraße 1, 8010 Graz" | Address of the station                                                                                                                  |
| free_parking                  | Boolean or `null` | true                       | Parking at the station is free of charge (`null` = unknown)                                                                             |
| free_charging                 | Boolean or `null` | true                       | Charging at the station is free of charge (`null` = unknown)                                                                            |
| evse_ids                      | Array             | ["AT\*ION\*E1234"]           | All [EMI3 EVSE IDs](https://emi3group.com/wp-content/uploads/sites/5/2018/12/eMI3-standard-v1.0-Part-2.pdf) connected to this location. |
| charge_points                 | Array             | -                          | Charge points at this station, grouped by power and plug type                                                                           |
| charge_points.plug            | String            | "ccs"                      | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`)                                          |
| charge_points.power           | Float             | 50.0                       | Max. power                                                                                                                              |
| charge_points.count           | Integer           | 2                          | Total number of charge points of this type at the station                                                                               |
| charge_points.available_count | Integer or `null` | 2                          | Number of charge points of this type at the station, which are ready to use and not occupied. (`null` = unknown)                        |

### Included Section

`company`

| **Name**       | **Type** | **Example**      | **Description**                                                                                                                                                                                                                                                          |
| -------------- | -------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name           | String   | "McDonalds Graz" | Name of the charging station operator                                                                                                                                                                                                                                    |
| supported_emps | Array    | -                | EMP companies which are connected to this CPO (=customers of these EMPS are able to activate charging stations of the current CPO). Attention: This is not a list of all supported EMPs, but only those who have been passed in the `operator.supported_emps.id` filter! |


# Meta
- more_available: Indicates if there are more stations available on the
  following page (see pagination).
- disabled_going_electric_countries: Countries where the Chargeprice Data has
  likely better quality than the GoingElectric data and hence the GoingElectric
  data shouldn't be shown at all

## Example

### Bounding Box Request

```http
GET http://example-base-url.com/v1/charging_stations?filter[latitude.gte]=9.0&filter[latitude.lte]=11.0&filter[longitude.gte]=19.0&filter[longitude.lte]=21.0
Content-Type: application/json
Api-Key: my-secret-key
```

### Radius Request

```http
GET http://example-base-url.com/v1/charging_stations?filter[latitude]=9.0&filter[latitude]=11.0&filter[radius]=1000
Content-Type: application/json
Api-Key: my-secret-key
```

### ID Request

```http
GET http://example-base-url.com/v1/charging_stations?filter[id]=20006f18-3ed4-4715-92b5-08e37e6dd18c
Content-Type: application/json
Api-Key: my-secret-key
```

### With Supported EMPS

```http
GET http://example-base-url.com/v1/charging_stations?filter[operator.supported_emps.id]=6e62cd2d-f29d-4107-b087-6d4f75261cce&fields[company]=name,supported_emps
Content-Type: application/json
Api-Key: my-secret-key
```

Note: Only the parameters relevant for the supported EMPs are listed in the
above example.

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
        "evse_ids": ["AT*ION*E1234"],
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
      },
      "relationships": {
        "supported_emps": {
          "data": [
            {
              "type": "company",
              "id": "6e62cd2d-f29d-4107-b087-6d4f75261cce"
            }
          ]
        }
      }
    }
  ],
  "meta": {
    "more_available": true,
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