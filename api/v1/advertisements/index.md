# GET /v1/advertisements

Get a list of advertisements. 

This API follows the https://jsonapi.org specification.

## Authorization Group

`ViewAdvertisements`

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`
* `Accept-Language: en`: [Valid Languages](../../enums.md). Used to localize the response, default language is `en`.

## Response Body

The API might return different types of advertisements in the future. Right now only the type `banner_advertisement` is available!

The following table lists the `attributes` of these objects:

| **Name**           | **Type** | **Example**                                   | **Description**                                                                                                           |
| ------------------ | -------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| cta_url            | String   | "https://test.com"                            | Link that should be opened when clicking on the ad                                                                        |
| banner_image_url   | String   | "https://www.chargeprice.app/images/test.png" | URL of Banner Image (=the Ad) that should be displayed.                                                                   |
| placement_location | String   | "map"                                         | Defines where in the app this ad should be displayed. Possible values: "map", "price_list1", "price_list2", "price_list3" |

## Example

### Request

```http
GET http://example-base-url.com/v1/advertisements
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
      "id": "40006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "banner_advertisement",
      "attributes": {
        "cta_url": "https://test.com",
        "banner_image_url": "https://www.chargeprice.app/images/test.png",
        "placement_location": "map"
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