# GET /v2/vehicle_brands

Returns all brands of all supported electric vehicles.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadVehicles`

## Request

No parameters.

## Response Body

A response contains 0 to many vehicle brands.
The following table lists the `attributes` of a `vehicle_brand`:

| **Name** | **Type** | **Example** | **Description**           |
| -------- | -------- | ----------- | ------------------------- |
| name     | String   | "Opel"      | Name of the vehicle brand |

## Example

### Request

```http
GET http://example-base-url.com/v2/vehicle_brands
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
            "id": "a43d2607-eead-46c2-9fd5-2ebd3c49d895",
            "type": "vehicle_brand",
            "attributes": {
                "name": "Audi"
            }
        },
        {
            "id": "6d9d9248-de4a-4b13-976b-4e9f6688b0b1",
            "type": "vehicle_brand",
            "attributes": {
                "name": "BMW"
            }
        }
    ],
    "meta": {
        "overall_count": 2
    }
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
