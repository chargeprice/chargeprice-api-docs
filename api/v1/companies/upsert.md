# PUT /v1/companies/:company_id

Create or update a company, if already existing.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

The body can have the following attributes:

| **Name**                | **Type** | **Presence** | **Example**                            | **Description**                                                                                                                        |
| ----------------------- | -------- | ------------ | -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| id                      | UUID     | required     | "1e49b853-36fc-47ed-9826-97828b5b2fdd" | Create: Client-side generated UUID, Update: Existing Resource ID                                                                       |
| type                    | String   | required     | "company"                              | Type of the resource. Needs to be `company`.                                                                                           |
| name                    | String   | required     | "EnBW"                                 | Name of the company                                                                                                                    |
| version                 | Integer  | required     | 1                                      | Lock version. Needs to be 1 on create and for update 1 higher than the current version.                                                |
| url                     | String   | optional     | "http://www.enbw.de"                   | E-Mobility website of the company                                                                                                      |
| is_cpo                  | Boolean  | required     | true                                   | True if the company is a charge point operator                                                                                         |
| is_emp                  | Boolean  | required     | true                                   | True if the company is an e-mobility service provider                                                                                  |
| external_source_mapping | Hash     | required     | -                                      | Names or IDs in external data sources. Only the ones known need to be added to the request.                                            |
| supported_emps          | Array    | required     | -                                      | All EMP companies which are connected to this CPO (=customers of these EMPS are able to activate charging stations of the current CPO) |

## Response Body

The following table lists the `attributes` of a `company`:

| **Name**                | **Type**  | **Example**          | **Description**                                                                                                                        |
| ----------------------- | --------- | -------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| name                    | String    | "EnBW"               | Name of the company                                                                                                                    |
| created_at              | Timestamp | 1546297200000        | Creation time of the resource                                                                                                          |
| updated_at              | Timestamp | 1546297200000        | Last update of the resource                                                                                                            |
| version                 | Integer   | 1                    | Current lock version                                                                                                                   |
| url                     | String    | "http://www.enbw.de" | E-Mobility website of the company                                                                                                      |
| is_cpo                  | Boolean   | true                 | True if the company is a charge point operator                                                                                         |
| is_emp                  | Boolean   | true                 | True if the company is an e-mobility service provider                                                                                  |
| external_source_mapping | Hash      | -                    | Names or IDs in external data sources                                                                                                  |
| supported_emps          | Array     | -                    | All EMP companies which are connected to this CPO (=customers of these EMPS are able to activate charging stations of the current CPO) |

Timestamp = Millis since 1.1.1970

## Example

### Request

```http
PUT http://example-base-url.com/v1/companies/1e49b853-36fc-47ed-9826-97828b5b2fdd
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "id": "1e49b853-36fc-47ed-9826-97828b5b2fdd",
    "type": "company",
    "attributes": {
      "name": "EnBW",
      "version": 1,
      "url": "http://www.energy.com",
      "is_cpo": true,
      "is_emp": true,
      "external_source_mapping": {
        "going_electric_names": [
          "EnBW"
        ],
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
      }
    }
  }
}

```

### Response

#### 200 Ok (after update)
#### 201 Created (after create)

Body:
```json
{
  "data": {
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
        "google_sheet_names": null
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
      }
    }
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

##### 409 Conflict

An Invalid version was sent in the request.

```json
{
  "errors": [
    {
      "status": "409",
      "title": "Version conflict"
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