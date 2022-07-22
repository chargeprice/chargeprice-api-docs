# DELETE /v1/users/:user_id

GDPR Delete all data of the user.

Internal API that is not supposed to be called from Third parties.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>`
* `Content-Type: application/json`

## Authorization Group

`IamFull`

## Example

### Request

```http
DELETE http://example-base-url.com/v1/users/ae62cd2d-f29d-4107-b087-6d4f75261cca
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 204 No Content

#### 400 Bad Request

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

#### 403 Forbidden

* API-Key is missing
* API-Key is invalid
* API-Key not authorized to perform action
* User Access Token not authorized to perform action
* User Access Token is invalid or expired

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

#### 500 Internal Server Error

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