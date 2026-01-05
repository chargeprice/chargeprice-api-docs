# DELETE /v1/photos/:photo_id

Delete a photo.

Only the user who uploaded the photo can delete it.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Authorization: Bearer <user_access_token>`
* `Content-Type: application/json`

## Authorization Group

`WritePhotos`

# Request Parameters

**photo_id** Id of photo that should be deleted.

## Example

### Request

```http
DELETE http://example-base-url.com/v1/photos/2e49b853-36fc-47ed-9826-97828b5b2fdd
Content-Type: application/json
Authorization: Bearer user-access-token
Api-Key: my-secret-key
```

### Response

#### 204 No Content

Photo was successfully deleted. No content is returned.

##### 403 Forbidden

* API-Key is missing
* API-Key is invalid
* API-Key not authorized to perform action
* User access token is missing or not authorized to perform action

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

Photo with given ID does not exist.

```json
{
  "errors": [
    {
      "status": "404",
      "code": "NOT_FOUND",
      "title": "photo not found"
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