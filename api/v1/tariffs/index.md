# GET /v1/tariffs

Get all available tariffs.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadSimpleTariffs`

## Request

The following query filter parameters are available: 

| **Name**               | **Type**   | **Presence** | **Example**                                                                 | **Description**                                                                                         |
|------------------------|------------|--------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| provider_customer_only | Boolean    | optional     | true                                                                        | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home). |
| direct_payment         | Boolean    | optional     | true                                                                        | This tariff can be used without registration                                                            |
| id                     | CSV of IDs | optional     | `cbe781a1-c16e-4af0-890a-b5f5943b6b77,1be781a1-c16e-4af0-890a-b5f5943b6b7a` | Only return tariffs with these IDs                                                                      |

## Response Body

A response contains 0 to many `tariff` objects.
The following table lists the `attributes` of these objects:

| **Name**                  | **Type**      | **Example**                 | **Description**                                                                                                        |
|---------------------------|---------------|-----------------------------|------------------------------------------------------------------------------------------------------------------------|
| provider                  | String        | "Maingau Energie"           | Name of the charge card provider                                                                                       |
| name                      | String        | "EinfachStromLaden"         | Name of the tariff                                                                                                     |
| total_monthly_fee         | Float         | 10.0                        | Monthly fee incl. a 12th of any yearly fee.                                                                            |
| currency                  | String        | "EUR"                       | Main currency of the tariff. Applies to e.g. the monthly fee.                                                          |
| is_direct_payment         | Boolean       | true                        | This tariff can be used without registration                                                                           |
| is_card_payment           | Boolean       | true                        | This tariff applies to card payments at stations with a terminal                                                       |
| existing_customer_only    | Boolean       | true                        | If true, tariff is only available for existing customers and a registration for new customers is not possible anymore. |
| provider_customer_tariff  | Boolean       | true                        | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).                |
| integration_status        | String        | "complete"                  | How far this tariff is integrated into Chargeprice. [See possible values](../../enums.md#integration-status).                                                      |
| branding                  | Hash or null  | -                           | If branding for the tariff is not available, then `null`                                                               |
| branding.background_color | String        | "#ff0000"                   | Brand color to be used as background color to show the tariff.                                                         |
| branding.text_color       | String        | "#000000"                   | Color to be used as text color to show the tariff.                                                                     |
| branding.logo_url         | String        | "https://logo.com/test.png" | Brand logo to be displayed next to the tariff.                                                                         |
| supported_countries       | Array<String> | ["AT","DE"]                 | List of countries where the tariff is mainly supported. If empty, it's supported in every country.                     |
| charge_card_id            | String        | "201"                       | GoingElectric charge card ID. If a tariff has none, it defaults to "0". (Legacy attribute)                             |
| emp                       | Relationship  | -                           | Owner EMP of the tariff.                                                                                               |

Timestamp = Millis since 1.1.1970

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
        "is_direct_payment": true,
        "is_card_payment": true,
        "existing_customer_only": false,
        "integration_status": "complete",
        "total_monthly_fee": 0.0,
        "currency": "EUR",
        "branding": {
          "background_color": "#ff0000",
          "text_color": "#000000",
          "logo_url": "http://www.cp.app/logo.png"
        },
        "supported_countries": ["AT"],
        "charge_card_id": "208"
      },
      "relationships": {
        "emp": {
          "data": {
            "type": "company",
            "id": "3be781a1-c16e-4af0-890a-b5f5943b6b75"
          }
        }
      }
    },
    {
      "id": "1be781a1-c16e-4af0-890a-b5f5943b6b73",
      "type": "tariff",
      "attributes": {
        "provider": "Plugsurfing",
        "provider_customer_only": false,
        "is_direct_payment": false,
        "is_card_payment": false,
        "existing_customer_only": false,
        "integration_status": "pending",
        "branding": null,
        "supported_countries": ["AT","DE"],
        "total_monthly_fee": 10.0,
        "currency": "EUR",
        "charge_card_id": "208"
      },
      "relationships": {
        "emp": {
          "data": {
            "type": "company",
            "id": "2be781a1-c16e-4af0-890a-b5f5943b6b71"
          }
        }
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