# PUT /v1/users/:user_id/settings

Upserts (Creates or updates of existing) the settings of a user.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Authorization: Bearer <user_access_token>` (Link to Authentication API docs **TBD**)
* `Content-Type: application/json`

## Authorization Group

`ReadWriteUserSettings`

## Request Body

## Request

The body can have the following attributes and relationships:

| **Name**                              | **Type**           | **Presence** | **Example**                                       | **Description**                                                                                                                                                                                                                             |
|---------------------------------------|--------------------|--------------|---------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                                    | UUID               | required     | "1e49b853-36fc-47ed-9826-97828b5b2fdd"            | Create: Client-side generated UUIDv4, Update: Existing Resource ID                                                                                                                                                                          |
| type                                  | String             | required     | "tariff"                                          | Type of the resource. Needs to be `user_settings`.                                                                                                                                                                                          |
| android_purchases                     | Array              | optional     | -                                                 | List of **active** purchases on an Android device the user did.                                                                                                                                                                             |
| android_purchases.purchase_token      | String             | required     | -                                                 | `purchaseToken` received from Google Play Store.                                                                                                                                                                                            |
| android_purchases.product_id          | String             | required     | -                                                 | `productId` from the Google Play Store. Currently only `premium`.                                                                                                                                                                             |
| ios_purchases                         | Array              | optional     | -                                                 | List of **active** purchases on an iOS device the user did.                                                                                                                                                                                 |
| ios_purchases.receipt_data            | String             | required     | -                                                 | `receiptData` received from the Apple App Store.                                                                                                                                                                                            |
| ios_purchases.original_transaction_id | String             | required     | -                                                 | `original_transaction_id` received from the Apple App Store.                                                                                                                                                                                |
| version                               | Integer            | required     | 1                                                 | Lock version. Needs to be 1 on create and for update 1 higher than the current version.                                                                                                                                                     |
| vehicle                               | Relationship       | required     | `{"id": "some-uuid", type:"car" }`                | The current selected vehicle of the user.                                                                                                                                                                                                   |
| available_vehicles                    | Relationship       | required     | `[{"id": "some-uuid", type:"car" }]`              | All vehicles that the user has added to their profile. Max number: 20.                                                                                                                                                                      |
| tariffs                               | Relationship Array | required     | `[{"id": "some-uuid", type:"tariff" }]`           | A list of tariffs that the user "owns" or has subscribed to. Max number of tariffs: 200                                                                                                                                                     |
| favourite_stations                    | Relationship Array | required     | `[{"id": "some-uuid", type:"charging_station" }]` | Stations that the user has added to their favourites. The type defines the source of the station: "charging_station" => Chargeprice Station DB, "going_electric_charging_station" => Going Electric Station DB. Max number of stations: 100 |

## Response Body

A response contains a `user_settings` object.
The following table lists it's `attributes` and `relationships`:

| **Name**                              | **Type**     | **Example**                                       | **Description**                                                                                                                                                                                                 |
|---------------------------------------|--------------|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| android_purchases                     | Array        | -                                                 | List of **active** purchases on an Android device the user did.                                                                                                                                                 |
| android_purchases.purchase_token      | String       | -                                                 | `purchaseToken` received from Google Play Store.                                                                                                                                                                |
| android_purchases.product_id          | String       | -                                                 | `productId` from the Google Play Store. Currently only `premium`.                                                                                                                                               |
| ios_purchases                         | Array        | -                                                 | List of **active** purchases on an iOS device the user did.                                                                                                                                                     |
| ios_purchases.receipt_data            | String       | -                                                 | `receiptData` received from the Apple App Store.                                                                                                                                                                |
| ios_purchases.original_transaction_id | String       | -                                                 | `original_transaction_id` received from the Apple App Store.                                                                                                                                                    |
| created_at                            | Timestamp    | 1546297200000                                     | Creation time of the resource                                                                                                                                                                                   |
| updated_at                            | Timestamp    | 1546297200000                                     | Last update of the resource                                                                                                                                                                                     |
| version                               | Integer      | 1                                                 | Current lock version                                                                                                                                                                                            |
| owner                                 | Relationship | `{"id": "some-uuid", type:"user" }`               | The user that belongs to these settings                                                                                                                                                                         |
| tariffs                               | Relationship | `[{"id": "some-uuid", type:"tariff" }]`           | A list of tariffs that the user "owns" or has subscribed to.                                                                                                                                                    |
| vehicle                               | Relationship | `{"id": "some-uuid", type:"car" }`                | The current selected vehicle of the user                                                                                                                                                                        |
| available_vehicles                    | Relationship | `[{"id": "some-uuid", type:"car" }]`              | All vehicles that the user has added to their profile.                                                                                                                                                          |
| favourite_stations                    | Relationship | `[{"id": "some-uuid", type:"charging_station" }]` | Stations that the user has added to their favourites. The type defines the source of the station: "charging_station" => Chargeprice Station DB, "going_electric_charging_station" => Going Electric Station DB. |

## Example

### Request

```http
PUT http://example-base-url.com/v1/users/ae62cd2d-f29d-4107-b087-6d4f75261cca/settings
Content-Type: application/json
Api-Key: my-secret-key
Authorization: Bearer user-access-token
```
Body:

```json
{
  "data": {
    "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
    "type": "user_settings",
    "attributes": {
      "version": 1,
      "android_purchases": [
        {
          "purchase_token": "some-purchase-token",
          "product_id": "premium",
        }
      ],
      "ios_purchases": [
        {
          "receipt_data": "some-receipt-data",
          "original_transaction_id": "some-transaction-id",
        }
      ]
    },
    "relationships": {
      "vehicle": {
        "data": {
          "type": "car",
          "id": "122cd2d-f29d-4107-b087-6d4f75261cc3"
        }
      },
      "available_vehicles": {
        "data": [
          {
            "type": "car",
            "id": "122cd2d-f29d-4107-b087-6d4f75261cc3"
          }
        ]
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
      },
      "favourite_stations": {
        "data": [
          {
            "type": "charging_station",
            "id": "987cd2d-f29d-4107-b087-6d4f75261123"
          }
        ]
      }
    }
  }
}

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
      "available_vehicles": {
        "data": [
          {
            "type": "car",
            "id": "122cd2d-f29d-4107-b087-6d4f75261cc3"
          }
        ]
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
      },
      "favourite_stations": {
        "data": [
          {
            "type": "charging_station",
            "id": "987cd2d-f29d-4107-b087-6d4f75261123"
          }
        ]
      }
    }
  },
  "meta": {
      "products": [
          "web_pro",
          "mobile_premium"
      ]
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

##### 409 Conflict

* An Invalid version was sent in the request
* The provided ID is already in use
* The provided ID doesn't match the ID of the existing settings of the user

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

```json
{
  "errors": [
    {
      "status": "409",
      "code": "ID_CONFLICT",
      "title": "Id conflict"
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