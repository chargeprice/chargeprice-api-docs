# POST /v1/photos

Upload a photo.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Authorization: Bearer <user_access_token>`
* `Content-Type: application/json`

## Authorization Group

`WritePhotos`

## Request

### Charging Station Photo

The body can have the following attributes:

| **Name**         | **Type**     | **Presence** | **Example**                                                                | **Description**                                                         |
|------------------|--------------|--------------|----------------------------------------------------------------------------|-------------------------------------------------------------------------|
| id               | UUID         | required     | "1e49b853-36fc-47ed-9826-97828b5b2fdd"                                     | Create: Client-side generated UUID.                                     |
| type             | String       | required     | "charging_station_photo"                                                   | Type of the resource. Needs to be `charging_station_photo`.             |
| photo_data       | String       | required     | "base64-encoded-photo-data"                                                | Base64 encoded photo data. See more about the photo requirements below. |
| charging_station | Relationship | required     | `{ id: "2e49b853-36fc-47ed-9826-97828b5b2fdd", type: "charging_station" }` | The ID of the charging station, at which the photo was taken.           |

### Photo Requirements

* Maximum file size: 2 MB
* Accepted file formats: JPEG
* Client should apply a 90% JPEG compression to reduce file size
* Maximum resolution: 1080 px (long side)
* Charging Station Photos:
  * Max. 20 photos can be uploaded per charging station
  * Max. 10 photos can be uploaded per charging station per user
  * Max. 10 photos can be uploaded per user per 2 hours
  * Max. 500 photos can be uploaded per user per month

## Response Body

The following table lists the `relationships` of a `charging_station_photo`:

| **Name**         | **Type**     | **Example**                                                                | **Description**                                               |
|------------------|--------------|----------------------------------------------------------------------------|---------------------------------------------------------------|
| charging_station | Relationship | `{ id: "2e49b853-36fc-47ed-9826-97828b5b2fdd", type: "charging_station" }` | The ID of the charging station, at which the photo was taken. |
                                                                

## Example

### Request

```http
POST http://example-base-url.com/v1/photos
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
    "type": "charging_station_photo",
    "attributes": {
      "photo_data": "base64-encoded-photo-string",
    },
    "relationships": {
      "charging_station": {
        "data": {
          "type": "charging_station",
          "id": "2e49b853-36fc-47ed-9826-97828b5b2fdd"
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
    "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
    "type": "charging_station_photo",
    "relationships": {
      "charging_station": {
        "data": {
          "type": "charging_station",
          "id": "2e49b853-36fc-47ed-9826-97828b5b2fdd"
        }
      }
    }
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