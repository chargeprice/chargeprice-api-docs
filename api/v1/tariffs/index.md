# GET /v1/tariffs

Get all available tariffs.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

The following query filter parameters are available: 

| **Name**               | **Type** | **Presence** | **Example** | **Description**                                                                                         |
| ---------------------- | -------- | ------------ | ----------- | ------------------------------------------------------------------------------------------------------- |
| provider_customer_only | Boolean  | optional     | true        | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home). |
| direct_payment         | Boolean  | optional     | true        | This tariff can be used without registration                                                            |

## Response Body

A response contains 0 to many `tariff` objects.
The following table lists the `attributes` of these objects:

| **Name**                  | **Type**      | **Example**                 | **Description**                                                                                         |
| ------------------------- | ------------- | --------------------------- | ------------------------------------------------------------------------------------------------------- |
| provider                  | String        | "Maingau Energie"           | Name of the charge card provider                                                                        |
| name                      | String        | "EinfachStromLaden"         | Name of the tariff                                                                                      |
| direct_payment            | Boolean       | true                        | This tariff can be used without registration                                                            |
| provider_customer_tariff  | Boolean       | true                        | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home). |
| branding                  | Hash or null  | -                           | If branding for the tariff is not available, then `null`                                                |
| branding.background_color | String        | "#ff0000"                   | Brand color to be used as background color to show the tariff.                                          |
| branding.logo_url         | String        | "https://logo.com/test.png" | Brand logo to be displayed next to the tariff.                                                          |
| supported_countries       | Array<String> | ["AT","DE"]                 | List of countries where the tariff is mainly supported. If empty, it's supported in every country.      |
| charge_card_id            | String        | "201"                       | GoingElectric charge card ID                                                                            |

## Example

### Request

```http
GET http://example-base-url.com/v1/tariffs
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
      "id": "cbe781a1-c16e-4af0-890a-b5f5943b6b77",
      "type": "tariff",
      "attributes": {
        "provider": "Energie Graz",
        "name": "Energie Graz Direct",
        "provider_customer_only": false,
        "direct_payment": true,
        "branding": {
          "background_color": "#ff0000",
          "logo_url": "http://www.cp.app/logo.png"
        },
        "supported_countries": ["AT"],
        "charge_card_id": "208"
      }
    },
    {
      "id": "1be781a1-c16e-4af0-890a-b5f5943b6b73",
      "type": "tariff",
      "attributes": {
        "provider": "Plugsurfing",
        "provider_customer_only": false,
        "direct_payment": false,
        "branding": null,
        "supported_countries": ["AT","DE"],
        "charge_card_id": "208"
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