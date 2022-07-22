# GET /v1/vehicles

Returns all supported electric vehicles.

If you want to get all details of vehicles, please use
[/v2/vehicles](../../v2/vehicles/index.md).

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadVehicles`

## Request

No parameters.

## Response Body

A response contains 0 to many vehicles. The following table lists the available `attributes`:

| **Name**            | **Type**      | **Example**                 | **Description**                                                                                |
| ------------------- | ------------- | --------------------------- | ---------------------------------------------------------------------------------------------- |
| name                | String        | "Kona 64kWh (2019)"         | Name of the car including variant and release year, if available                               |
| brand               | String        | "Hyundai"                   | Brand of the car                                                                               |
| dc_charge_ports     | Array<String> | ["ccs","tesla_ccs"]         | All DC charge ports, that the car is capable to charge with. Possible values: `ccs`, `chademo` |
| usable_battery_size | Float         | 39.2                        | net battery size in kWh                                                                        |
| ac_max_power        | Float         | 11.0                        | maximum power in kW that the vehicle can charge at the fastest AC station                      |
| dc_max_power        | Float or null | 220.0                       | maximum power in kW that the vehicle can charge at the fastest DC station                      |
| manufacturer        | Relationship  | { id: "..", type: "brand" } | Manufacturer (Brand) of the car                                                                |

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
        "name": "Kona 64 kWh 7,2 kW-AC (2018)",
        "brand": "Hyundai",
        "dc_charge_ports": [
            "ccs"
        ],
        "ac_max_power": 7.2,
        "dc_max_power": 77,
        "usable_battery_size": 64
      },
      "relationships": {
        "manufacturer": {
          "data": {
            "type": "brand",
            "id": "3e49b853-36fc-47ed-9826-97828b5b2fd1"
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
