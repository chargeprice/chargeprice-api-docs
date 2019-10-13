# GET /v1/vehicles

Returns all supported electric vehicles. The data is based on the [Open EV data project](https://github.com/hoenic07/open-ev-data).

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

No parameters.

## Response Body

A response contains 0 to many vehicles. At the moment vehicles of type `car` are supported.
The following table lists the `attributes` of a `car`:

| **Name**        | **Type**      | **Example**         | **Description**                                                                                |
| --------------- | ------------- | ------------------- | ---------------------------------------------------------------------------------------------- |
| name            | String        | "Kona 64kWh (2019)" | Name of the car including variant and release year, if available                               |
| brand           | String        | "Hyundai"           | Brand of the car                                                                               |
| dc_charge_ports | Array<String> | ["ccs","tesla_ccs"] | All DC charge ports, that the car is capable to charge with. Possible values: `ccs`, `chademo` |

## Example

### Request

```http
GET http://example-base-url.com/v1/vehicles
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
      "type": "car",
      "attributes": {
        "name": "Kona 64kWh (2018)",
        "brand": "Hyundai",
        "dc_charge_ports": [
          "ccs"
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
      "status": "403",
      "title": "api_key missing"
    }
  ]
}
```
