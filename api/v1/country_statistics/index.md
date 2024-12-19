
# GET /v1/country_statistics

Overall statistics of the charging infrastructure per country.
Contains current and historical data 

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadCountryStatistics`

## Request

### Query Parameters

The following query parameters are available:

| **Name**        | **Type**         | **Presence** | **Example** | **Description**                                                            |
|-----------------|------------------|--------------|-------------|----------------------------------------------------------------------------|
| filter[country] | CSV              | optional     | `AT,DE`     | A list of countries (ISO 3166) for which the statistics should be fetched. |
| filter[year]    | Integer          | optional     | `2020`      | Year for which the statistics should be fetched.                           |
| filter[month]   | Integer          | optional     | `9`         | Month for which the statistics should be fetched. January = 1.             |
| page[size]      | Positive Integer | optional     | 2           | Max no. of elements in the current response. Default: 100, Max value: 100  |
| page[number]    | Positive Integer | optional     | 10          | Current page number. Default: 1                                            |

### Pagination

Use the `meta.overall_count` to determine if you have already fetched all pages.
If no pagination parameters are provided you will get the first page.

## Response Body

A response contains 0 to many entries.
The following table lists the available `attributes`:

| **Name**            | **Type** | **Example**                        | **Description**                                                                         |
|---------------------|----------|------------------------------------|-----------------------------------------------------------------------------------------|
| country             | String   | "AT"                               | ISO 3166 country code                                                                   |
| year                | Integer  | 2020                               | Year of the statistics                                                                  |
| month               | Integer  | 9                                  | Month of the statistics                                                                 |
| emp_count           | Integer  | 500                                | Number of E-Mobility Service Providers (EMPs) that operate in the country.              |
| cpo_count           | Integer  | `{"id": "123", "type": "company"}` | Number of Charge Point Operators (CPOs) that operate stations in the country.           |
| charge_points       | Array    | -                                  | Statistics per type of charge points.                                                   |
| charge_points.type  | String   | `dc`                               | Type of charge points. Possible values: `total`, `ac`, `dc` (< 150 kW), `hpc` (150+ kW) |
| charge_points.count | Integer  | 34                                 | Number of charge points (connectors) of this type in the country.                       |

The example below also describes the response in this way.

## Example

```http
GET http://example-base-url.com/v1/country_statistics?filter[country]=FR
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
            "type": "country_statistics",
            "attributes": {
                "country": "FR",
                "emp_count": 432,
                "cpo_count": 750,
                "charge_points": [
                  {
                    "type": "total",
                    "count": 12
                  },
                  {
                    "type": "ac",
                    "count": 6
                  },
                  {
                    "type": "dc",
                    "count": 4
                  },
                  {
                    "type": "hpc",
                    "count": 3
                  }
                ]
            }
        }
    ],
    "meta": {
        "overall_count": 1
    }
}
```
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