# POST /v1/crm/devices

Register or update a CRM device to receive push notifications from the Chargeprice backend.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>`
* `Authorization: Bearer <user_access_token>` (optional, only required if the user is logged in)
* `Content-Type: application/json`

## Authorization Group

`WriteCrmDevices`

## Request

The body can have the following attributes:

| **Name**               | **Type**      | **Presence** | **Example**                            | **Description**                                                                                                                                                                                                                                     |
|------------------------|---------------|--------------|----------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                     | UUID          | required     | "1e49b853-36fc-47ed-9826-97828b5b2fdd" | Client-side generated UUIDv4 that uniquely identifies this device.                                                                                                                                                                                  |
| type                   | String        | required     | "crm_device"                           | Type of the resource. Needs to be `crm_device`.                                                                                                                                                                                                     |
| fcmToken               | String        | required     | "some_device_token"                    | Firebase Cloud Messaging token for this device, used to deliver push notifications.                                                                                                                                                                 |
| country                | String        | required     | "DE"                                   | ISO 3166-1 alpha-2 country code of the user's current country.                                                                                                                                                                                      |
| language               | String        | required     | "de"                                   | ISO 639-1 language code of the user's preferred language.                                                                                                                                                                                           |
| app_version            | String        | required     | "1.0.0"                                | Semantic version string of the app installed on this device.                                                                                                                                                                                        |
| os_version             | String        | required     | "14.4"                                 | Operating system version of this device.                                                                                                                                                                                                            |
| user_products          | Array<String> | required     | ["mobile_premium"]                     | List of active product keys the user currently holds. Used to personalise notifications. [See allowed values](/api/enums.md#user-products)                                                                                                          |
| disabled_notifications | Array<String> | required     | ["promotion_external_generic"]         | List of notification type keys the user has opted out of. Notifications of these types will not be sent to this device. Allowed values: "promotion_external_generic", "promotion_external_personalized", "promotion_chargeprice", "product_updates" |

## Example

### Request

```http
POST http://example-base-url.com/v1/crm/devices
Authorization: Bearer tok_abc
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
    "type": "crm_device",
    "attributes": {
      "fcmToken": "some_device_token",
      "country": "DE",
      "language": "de",
      "app_version": "1.0.0",
      "os_version": "14.4",
      "user_products": ["mobile_premium"],
      "disabled_notifications": ["promotion_external_generic", "promotion_external_personalized", "promotion_chargeprice", "product_updates"]
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
    "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
    "type": "crm_device",
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