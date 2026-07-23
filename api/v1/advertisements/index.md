# GET /v1/advertisements

Get a list of advertisements that can be displayed together with other ad providers (e.g. AdMob)

This API follows the https://jsonapi.org specification.

## Authorization Group

`ViewAdvertisements`

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`
* `Accept-Language: en`: [Valid Languages](../../enums.md). Used to localize the response, default language is `en`.

## Request

The following query parameters are available.

| **Name**              | **Type** | **Presence** | **Example** | **Description**                                                                                                                                        |
|-----------------------|----------|--------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| filter[country]       | String   | required     | "AT"        | ISO 3166 country code of the user.                                                                                                                     |
| filter[type]       | String   | optional     | "banner_advertisement"        | Type of advertisement to receive. Default: "banner_advertisement". Options: "banner_advertisement", "crm_push_notification"                                                                                                                    |
| ~~exclusive_ad_provider~~ | Boolean  | optional     | true        | ~~true: There is no other ad provider (e.g. AdMob). In this case more ads are served. false: Used in combination with other ad providers.~~ Default: true. As there is no Google Ads anymore, it can't be configured anymore.  |

## Response Body

### Banner Advertisement

| **Name**           | **Type** | **Example**                                   | **Description**                                                                                                           |
|--------------------|----------|-----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| cta_url            | String   | "https://test.com"                            | Link that should be opened when clicking on the ad                                                                        |
| deeplink_target            | String   | "premium"                            | Deep link within the app.                                                                        |
| banner_image_url   | String   | "https://www.chargeprice.app/images/test.png" | URL of Banner Image (=the Ad) that should be displayed.                                                                   |
| placement_location | String   | "map"                                         | Defines where in the app this ad should be displayed. Possible values: "map", "price_list1", "price_list2", "price_list3" |

### CRM Push Notification

| **Name**      | **Type**         | **Example**                        | **Description**                                                                    |
|---------------|------------------|-------------------------------------|--------------------------------------------------------------------------------------|
| sending_time  | String (ISO8601) | "2026-07-23T10:00:00Z"              | Time at which this notification was/will be sent.                                    |
| title         | String           | "New feature available"             | Title of the push notification.                                                      |
| content       | String           | "Check out the new trip planner!"   | Body text of the push notification.                                                  |
| image_url     | String           | "https://www.chargeprice.app/images/test.png" | URL of an image to display with the notification.                          |
| buttons       | Array<Button>    |                                      | Buttons to display with the notification.                                            |

#### Button

| **Name**       | **Type** | **Example**            | **Description**                                                     |
|----------------|----------|-------------------------|-----------------------------------------------------------------------|
| title          | String   | "Learn more"             | Title of the button.                                                   |
| url            | String   | "https://test.com"       | Link that should be opened when clicking on the button.                |
| deeplink_target| String   | "premium"                | Deep link within the app.                                              |

## Example

### Request

```http
GET http://example-base-url.com/v1/advertisements?filter[country]=AT
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
        "deeplink_target": "premium",
        "banner_image_url": "https://www.chargeprice.app/images/test.png",
        "placement_location": "map"
      }
    },
    {
      "id": "b8f2e6f0-9f2a-4b6b-8f0d-6e6c8f2a4b6b",
      "type": "crm_push_notification",
      "attributes": {
        "sending_time": "2026-07-23T10:00:00Z",
        "title": "New feature available",
        "content": "Check out the new trip planner!",
        "image_url": "https://www.chargeprice.app/images/test.png",
        "buttons": [
          {
            "title": "Learn more",
            "url": "https://test.com",
            "deeplink_target": "premium"
          }
        ]
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