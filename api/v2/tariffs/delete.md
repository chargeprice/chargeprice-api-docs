# DELETE /v2/tariffs/:tariff_id

Delete a tariff.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`WriteTariffs`

# Request Parameters

**tariff_id** Id of tariff that should be deleted.

## Example

### Request

```http
DELETE http://example-base-url.com/v2/tariffs/2e49b853-36fc-47ed-9826-97828b5b2fdd
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 204 No Content

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