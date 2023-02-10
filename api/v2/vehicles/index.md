# GET /v2/vehicles

Returns all supported electric vehicles with all details

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`ReadVehicles`

## Request

The following query parameters are available.

| **Name**                         | **Type**         | **Presence** | **Example**     | **Description**                                                                                                                    |
| -------------------------------- | ---------------- | ------------ | --------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| filter[id]                       | CSV              | optional     | "123,456"       | Only vehicles with the given ids are returned                                                                                      |
| filter[manufacturer.id]          | CSV              | optional     | "123,456"       | Only the vehicles which belong to these manufacturers are returned                                                                 |
| fields[car] or fields[motorbike] | CSV              | optional     | "model,variant" | Only the given fields of a vehicle are returned                                                                                    |
| page[size]                       | Positive Integer | optional     | 2               | Max no. of elements in the current response. Default: 100, Max value: 100                                                          |
| page[number]                     | Positive Integer | optional     | 10              | Current page number. Default: 1                                                                                                    |
| q                                | String           | optional     | "VW ID3"        | Fuzzy search for `manufacturer.name`, `model` and `variant`. Can't be combined with any other filter and it gives max. 25 results. |

### Pagination

Use the `meta.overall_count` to determine if you have already fetched all pages.
If no pagination parameters are provided you will get the first page.

## Response Body

A response contains 0 to many vehicles.
The following table lists the available `attributes`:

| **Name**                               | **Type**      | **Example**                            | **Description**                                                                                                                                                                                                                     |
| -------------------------------------- | ------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                                     | String        | "4ddddfde-fba1-4d1f-b1f4-a825ec463781" | Random UUID                                                                                                                                                                                                                         |
| type                                   | String        | "car"                                  | Allowed values: `car`, `motorbike`                                                                                                                                                                                                  |
| ev_type                                | String        | "bev"                                  | `bev`: Full-electric, `phev`: Plug-in Hybrid                                                                                                                                                                                        |
| model                                  | String        | "Kona"                                 | Model name                                                                                                                                                                                                                          |
| release_year                           | Integer       | 2021                                   | Mainly to distinquish models with the same name.                                                                                                                                                                                    |
| variant                                | String        | "64 kWh 11 kW-AC"                      | To distinquish vehicles of the same model that have e.g. a bigger battery, optional faster on-board charger etc.                                                                                                                    |
| usable_battery_size                    | Float         | 39.2                                   | net battery size in kWh                                                                                                                                                                                                             |
| ac_phases                              | Integer       | 3                                      | No. of usable phases for AC charging. Allowed values: 1,2,3                                                                                                                                                                         |
| ac_ports                               | Array<String> | ["type1","type2"]                      | Allowed values: `type1`, `type2`                                                                                                                                                                                                    |
| ac_max_power                           | Float         | 11.0                                   | maximum power in kW that the vehicle can charge at the fastest AC station                                                                                                                                                           |
| ac_power_per_charging_point            | Hash          | { "22.0": 11.0 }                       | Charging power at common charging points. Key and Value in kW.                                                                                                                                                                      |
| dc_ports                               | Array<String> | ["ccs"]                                | Allowed values: `ccs`, `chademo`, `tesla_suc`, `tesla_ccs`                                                                                                                                                                          |
| dc_max_power                           | Float or null | 220.0                                  | maximum power in kW that the vehicle can charge at the fastest DC station                                                                                                                                                           |
| dc_charging_curve                      | Hash          | -                                      | Simplified charging behaviour based on various charging curve charts (e.g. Fastned). If no charging curve data is available, the default curve is assumed to be: 0%: 95% of max. DC power, 75%: max. DC power, 100%: max. AC power. |
| dc_charging_curve.percentage           | Integer       | 40                                     | Charging level of battery in percentage                                                                                                                                                                                             |
| dc_charging_curve.power                | Float         | 200.0                                  | power level at the given percentage in kW                                                                                                                                                                                           |
| dc_default_charging_curve              | Boolean       | false                                  | `true` if the charging curve is based on the default curve instead of real measured data.                                                                                                                                           |
| energy_consumption.average_consumption | Float         | 15.4                                   | in kWh/100km. Usually the WLTP value.                                                                                                                                                                                               |
| dc_charge_ports                        | Array<String> | ["ccs","tesla_ccs"]                    | All DC charge ports, that the vehicle is capable to charge with. Possible values: `ccs`, `chademo`                                                                                                                                  |
| manufacturer                           | Relationship  | { id: "..", type: "brand" }            | Manufacturer (Brand) of the vehicle                                                                                                                                                                                                 |
| manufacturer.name                      | String        | "Opel"                                 | Name of the brand                                                                                                                                                                                                                   |

## Example

### Request

```http
GET http://example-base-url.com/v2/vehicles
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
            "id": "4ddddfde-fba1-4d1f-b1f4-a825ec463781",
            "type": "car",
            "attributes": {
                "ev_type": "bev",
                "model": "Ioniq 5",
                "release_year": 2021,
                "variant": "Standard Range",
                "usable_battery_size": 58.0,
                "ac_phases": 3,
                "ac_ports": [
                    "type2"
                ],
                "ac_max_power": 11.0,
                "ac_power_per_charging_point": {
                    "2.0": 2.0,
                    "2.3": 2.3,
                    "3.7": 3.7,
                    "7.4": 7.4,
                    "11.0": 11.0,
                    "16.0": 11.0,
                    "22.0": 11.0,
                    "43.0": 11.0
                },
                "dc_ports": [
                    "ccs"
                ],
                "dc_max_power": 232.0,
                "dc_charging_curve": [
                    {
                        "percentage": 0.0,
                        "power": 200.0
                    },
                    {
                        "percentage": 47.0,
                        "power": 175.0
                    },
                    {
                        "percentage": 100.0,
                        "power": 11.0
                    }
                ],
                "dc_default_charging_curve": false,
                "energy_consumption": {
                    "average_consumption": 19.4
                }
            },
            "relationships": {
                "manufacturer": {
                    "data": {
                        "type": "vehicle_brand",
                        "id": "9771afb8-9e25-4ae6-a5b3-b2a5b9f363f0"
                    }
                }
            }
        }
    ],
    "included": [
        {
            "id": "9771afb8-9e25-4ae6-a5b3-b2a5b9f363f0",
            "type": "vehicle_brand",
            "attributes": {
                "name": "Hyundai"
            }
        }
    ],
    "meta": {
        "overall_count": 1
    }
}
```

##### 400 Bad Request

Client provided invalid request.

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