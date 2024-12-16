# GET /v1/companies

Returns companies. Companies can be EMPs, CPOs or both.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadTariffs`

## Request

The following query parameters are available.

| **Name**        | **Type**         | **Presence** | **Example** | **Description**                                                                          |
|-----------------|------------------|--------------|-------------|------------------------------------------------------------------------------------------|
| filter[id]      | CSV              | optional     | "123,456"   | If set, only companies with the given ids are returned                                   |
| filter[is_emp]  | Boolean          | optional     | "true"      | Filter for `is_emp` attribute. Possible values: true, false                              |
| filter[is_cpo]  | Boolean          | optional     | "true"      | Filter for `is_cpo` attribute.Possible values: true, false                               |
| fields[company] | CSV              | optional     | "name,url"  | If set, only the given fields of a company are returned                                  |
| page[size]      | Positive Integer | optional     | 2           | Max no. of elements in the current response. Default: 100, Max value: 100                |
| page[number]    | Positive Integer | optional     | 10          | Current page number. Default: 1                                                          |
| q               | String           | optional     | "IONITY"    | Search for `name`. Can't be combined with any other filter and it gives max. 10 results. |

## Response Body

A response contains 0 to many companies.
The following table lists the `attributes` of a `company`:

| **Name**                                  | **Type**     | **Example**          | **Description**                                                                                                                        |
|-------------------------------------------|--------------|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| name                                      | String       | "EnBW"               | Name of the company                                                                                                                    |
| created_at                                | Timestamp    | 1546297200000        | Creation time of the resource                                                                                                          |
| updated_at                                | Timestamp    | 1546297200000        | Last update of the resource                                                                                                            |
| version                                   | Integer      | 1                    | Current lock version                                                                                                                   |
| url                                       | String       | "http://www.enbw.de" | E-Mobility website of the company                                                                                                      |
| is_cpo                                    | Boolean      | true                 | True if the company is a charge point operator                                                                                         |
| is_emp                                    | Boolean      | true                 | True if the company is an e-mobility service provider                                                                                  |
| external_source_mapping                   | Hash         | -                    | Names or IDs in external data sources                                                                                                  |
| external_source_mapping.evse_operator_ids | Array        | ["AT*ION]            | All eMI3 EVSE Operator (CPO) IDs of this company                                                                                       |
| supported_emps                            | Array        | -                    | All EMP companies which are connected to this CPO (=customers of these EMPS are able to activate charging stations of the current CPO) |
| cpo_group                                 | Relationship | -                    | When a company is part (e.g. as sub CPO) of a bigger charging network.                                                                 |
| emp_group                                 | Relationship | -                    | When a company is shares the same roaming partners than another EMP.                                                                 |

Timestamp = Millis since 1.1.1970

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
        "name": "EnBW",
        "created_at": 1546297200000,
        "updated_at": 1546297200000,
        "version": 1,
        "url": "http://www.energy.com",
        "is_cpo": true,
        "is_emp": true,
        "external_source_mapping": {
          "plugsurfing_ids": null,
          "going_electric_names": [
            "EnBW"
          ],
          "going_electric_charge_card_ids": null,
          "google_sheet_names": null,
          "open_charge_map_operator_ids": null,
          "evse_operator_ids": ["AT*ION"]
        }
      },
      "relationships": {
        "supported_emps": {
          "data": [
            {
              "type": "company",
              "id": "2e49b853-36fc-47ed-9826-97828b5b2fdd"
            }
          ]
        },
        "cpo_group": {
          "data": {
            "id": "1c49b853-36fc-47ed-9826-97828b5b2faa",
            "type": "company"
          }
        },
        "emp_group": {
          "data": {
            "id": "1c49b853-36fc-47ed-9826-97828b5b2faa",
            "type": "company"
          }
        }
      }
    }
  ],
  "meta": {
    "overall_count": 3
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