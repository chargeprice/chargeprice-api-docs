# GET /v1/charging_stations/:charging_station_id/photos

Fetch photos of a single charging station. The photos are ordered by creation
date, starting with the most recent one. Photos may be uploaded by users via the
[Create Charging Station Photo](../photos/create.md) API or may be fetched from
external sources.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadPhotos`

## Request

# Request Parameters

**charging_station_id** Id of the charging station for which photos should be fetched.

## Response Body

A response contains 0 to max. 400 `charging_station` objects. The following
table lists the `attributes` of these objects:

| **Name**     | **Type**          | **Example**                                                    | **Description**                                                                                            |
|--------------|-------------------|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| created_at   | Timestamp or null | 1546297200000                                                  | Time when the photo was taken.                                                                             |
| parking_type | String or `null`  | "along_motorway"                                               | The general type of parking at the charging location. [See Supported values](../../enums.md#parking-type). |
| source       | String or null    | "chargeprice"                                                  | Indicator for the source of the photo, used as an identifier.                                              |
| source_label | String or null    | "Chargeprice Community"                                        | Indicator for the source of the photo, that can be displayed in the user interface.                        |
| source_url   | String or null    | "https://www.chargeprice.net"                                  | URL of the source of the photo, that can be displayed in the user interface.                               |
| author       | Relationship      | `{ id: "2e49b853-36fc-47ed-9826-97828b5b2fdd", type: "user" }` | The ID of the user who took the photo.                                                                     |

Timestamp = Millis since 1.1.1970

## Example

```http
GET http://example-base-url.com/v1/charging_stations/20006f18-3ed4-4715-92b5-08e37e6dd18c/photos
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
      "type": "charging_station_photo",
      "attributes": {
        "created_at": 1546297200000,
        "source": "chargeprice",
        "source_label": "Chargeprice Community",
        "source_url": "https://www.chargeprice.net",
        "url": "https://www.my-photos.com/20006f18-3ed4-4715-92b5-08e37e6dd18c.jpg",
      },
      "relationships": {
        "author": {
          "data": {
            "type": "user",
            "id": "ae62cd2d-f29d-4107-b087-6d4f75261cca"
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