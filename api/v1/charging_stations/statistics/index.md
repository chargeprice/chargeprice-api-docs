# GET /v1/charging_stations/statistics

Statistics about charging stations on a charge point level and per country, CPO
(operator), power and plug type.

Various sources of charging stations are combined. It doesn't only consider the
charging stations of the [GET /v1/charging_stations](../index.md) endpoint.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadChargePointStatistics`

## Request

### Query Parameters

The following query parameters are available:

| **Name**        | **Type** | **Presence** | **Example** | **Description**                                                 |
| --------------- | -------- | ------------ | ----------- | --------------------------------------------------------------- |
| filter[country] | CSV      | optional     | `AT,DE`     | A list of countries for which the statistics should be fetched. |
| include         | CSV      | optional     | `operator`  | Data to include in the response. Possible values: `operator`    |


## Response Body

A response contains `charge_point_statistics` items. There is no pagination, all
results will be returned. The following table lists the `attributes` and
`relationships` of these objects:

| **Name**      | **Type**     | **Example**                        | **Description**                                                                                |
| ------------- | ------------ | ---------------------------------- | ---------------------------------------------------------------------------------------------- |
| country       | String       | "AT"                               | ISO 3166 country code                                                                          |
| power         | Float        | 50.0                               | Power level                                                                                    |
| plug          | Float        | 12.443                             | Type of plug (`ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc`) |
| count         | Integer      | 5                                  | Number of charge points with these parameters.                                                 |
| operator      | Relationship | `{"id": "123", "type": "company"}` | CPO (Charge Point Operator)                                                                    |
| operator.name | String       | `IONITY`                           | CPO Name                                                                                       |

You can read this as: There are `<count>` number of charge points in `<country>`
operated by `<operator>` with a power level of `<power>` and plug type `<plug>`.

The name of the operator can be fetched via [GET
/v1/companies](../../companies/index.md)

The example below also describes the response in this way.

## Example

```http
GET http://example-base-url.com/v1/charging_stations?filter[country]=FR&include=operator
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
            "id": "93b0b907-68b7-417c-8113-afeaabb44fc1",
            "type": "charge_point_statistics",
            "attributes": {
                "country": "FR",
                "power": 350.0,
                "plug": "ccs",
                "count": 143
            },
            "relationships": {
                "operator": {
                    "data": {
                        "type": "company",
                        "id": "c6bc64ab-8ff8-4623-898b-4a20632e686a"
                    }
                }
            }
        }
    ],
    "included": [
      {
        "type": "company",
        "id": "c6bc64ab-8ff8-4623-898b-4a20632e686a",
        "attributes": {
          "name": "IONITY"
        }
      }
    ]
}
```

This means:
There are 143 charge points in France operated by
`c6bc64ab-8ff8-4623-898b-4a20632e686a` (e.g. this could be IONITY) with a power
level of 350 kW and plug type CCS.

##### 400 Bad Request

Client provided invalid request parameters.

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