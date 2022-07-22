# GET /v1/users/:user_id/settings

Gets the settings of a user.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Authorization: Bearer <user_access_token>` (Link to Authentication API docs **TBD**)
* `Content-Type: application/json`

## Authorization Group

`ReadWriteUserSettings`

## Response Body

A response contains a `user_settings` object.
The following table lists it's `attributes` and `relationships`:

| **Name**   | **Type**     | **Example**                             | **Description**                                              |
| ---------- | ------------ | --------------------------------------- | ------------------------------------------------------------ |
| created_at | Timestamp    | 1546297200000                           | Creation time of the resource                                |
| updated_at | Timestamp    | 1546297200000                           | Last update of the resource                                  |
| version    | Integer      | 1                                       | Current lock version                                         |
| owner      | Relationship | `{"id": "some-uuid", type:"user" }`     | The user that belongs to these settings                      |
| tariffs    | Relationship | `[{"id": "some-uuid", type:"tariff" }]` | A list of tariffs that the user "owns" or has subscribed to. |
| vehicle    | Relationship | `[{"id": "some-uuid", type:"car" }]`    | The current selected vehicle of the user                     |

## Example

### Request

```http
GET http://example-base-url.com/v1/users/ae62cd2d-f29d-4107-b087-6d4f75261cca/settings
Content-Type: application/json
Api-Key: my-secret-key
Authorization: Bearer user-access-token
```

### Response

#### 200 Ok

Body:
```json
{
  "data": {
    "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
    "type": "user_settings",
    "attributes": {
      "created_at": 1546297200000,
      "updated_at": 1546297200000,
      "version": 1,
    },
    "relationships": {
      "owner": {
        "data": {
          "type": "user",
          "id": "ae62cd2d-f29d-4107-b087-6d4f75261cca"
        }
      },
      "vehicle": {
        "data": {
          "type": "car",
          "id": "122cd2d-f29d-4107-b087-6d4f75261cc3"
        }
      },
      "tariffs": {
        "data": [
          {
            "type": "tariff",
            "id": "522cd2d-f29d-4107-b087-6d4f75261cc8"
          },
          {
            "type": "tariff",
            "id": "022cd2d-f29d-4107-b087-6d4f75261ccf"
          }
        ]
      }
    }
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

##### 404 Not Found

No settings have been found for this user.

```json
{
  "errors": [
    {
      "status": "404",
      "code": "NOT_FOUND",
      "title": "not found"
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