# GET /v1/companies/cpos

Returns CPO companies.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadSimpleTariffs`

## Request

The following query parameters are available.

| **Name**             | **Type** | **Presence** | **Example** | **Description**                                                                                                |
|----------------------|----------|--------------|-------------|----------------------------------------------------------------------------------------------------------------|
| filter[user_country] | String   | optional     | "AT"        | ISO 3166 country code of the location. If set, promotion info is added to the CPO, if promoted in the country. |

## Response Body

A response contains hundreds of CPO companies.
The following table lists the `attributes` of a `company`:

| **Name**               | **Type**     | **Example**            | **Description**                       |
|------------------------|--------------|------------------------|---------------------------------------|
| name                   | String       | "EnBW"                 | Name of the company                   |
| meta.promoted          | Boolean      | true                   | Indicates if the company is promoted. |
| meta.branding          | Hash or null | See below              | Branding information of the CPO.      |
| meta.branding.logo_url | String       | http://cp.com/logo.png | URL to the logo of the CPO.           |

## Example

### Request

```http
GET http://example-base-url.com/v1/companies?page[number]=1&page[size]=1
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
      "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
      "type": "company",
      "attributes": {
        "name": "EnBW"
      },
      "meta": {
        "promoted": true,
        "branding": {
          "logo_url": "http://cp.com/logo.png"
        }
      }
    },
    {
      "id": "2a49b853-36fc-47ed-9826-97828b5b2faa",
      "type": "company",
      "attributes": {
        "name": "IONITY"
      },
      "meta": {
        "promoted": false,
        "branding": null
      }
    }
  ],
  "meta": {
    "overall_count": 2
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