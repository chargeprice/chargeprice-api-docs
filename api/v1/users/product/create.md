# POST /v1/users/:user_id/product

Purchase a new product for a user, given a product source.

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

| **Name** | **Type** | **Presence** | **Example**    | **Description**                                                           |
|----------|----------|--------------|----------------|---------------------------------------------------------------------------|
| type     | String   | required     | "user_product" | Type of the resource. Needs to be `user_product`.                         |
| source   | Object   | required     | -              | Source how the product is activated. See possible values of source below. |

### EMC Membership

| **Name**      | **Type** | **Presence** | **Example**      | **Description**                                     |
|---------------|----------|--------------|------------------|-----------------------------------------------------|
| type          | String   | required     | "emc_membership" | Type of the resource. Needs to be `emc_membership`. |
| member_number | String   | required     | "123456789"      | The member number of the EMC membership.            |
| card_number   | String   | required     | "987654321"      | The card number of the EMC membership.              |

## Response Body

A response contains a `user_products` object.
The following table lists it's `attributes`:

| **Name**      | **Type** | **Example**          | **Description**                                                                       |
|---------------|----------|----------------------|---------------------------------------------------------------------------------------|
| products | Array    | `["mobile_premium"]` | List of products available to the user. Possible values: `mobile_premium`, `web_pro`. |

## Example

### Request

```http
POST http://example-base-url.com/v1/users/ae62cd2d-f29d-4107-b087-6d4f75261cca/products
Content-Type: application/json
Api-Key: my-secret-key
Authorization: Bearer user-access-token
```
Body:

```json
{
  "data": {
    "type": "user_products",
    "attributes": {
      "source": {
        "type": "emc_membership",
        "member_number": "123456789",
        "card_number": "987654321",
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
    "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
    "type": "user_products",
    "attributes": {
      "products" : [
        "web_pro",
        "mobile_premium"
      ]
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

##### 404 Invalid Product Source

Client provided invalid request.

```json
{
  "errors": [
    {
      "status": "404",
      "code": "PRODUCT_SOURCE_NOT_FOUND",
      "title": "Invalid product source"
    }
  ]
}
```

##### 409 Product Source Already Used

The provided product source is already used by the same or another user.

```json
{
  "errors": [
    {
      "status": "409",
      "code": "PRODUCT_SOURCE_ALREADY_USED",
      "title": "Product source already used"
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