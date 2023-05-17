# GET /v1/my_best_tariffs/:id

Get the results of one "my best tariffs" calculation.

This API follows the https://jsonapi.org specification.

## Authorization Group

`ExecuteMyBestTariffs`

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

# Request Parameters

**id** of my best tariffs calculation that should be returned.

## Response Body

### Description

The best tariffs are always calculated per "charging use case": Charging use
cases could be "charge at home", "charge at work", "charge during trips". The
actual use cases that are part of the response, depend on the user input.

For every use case the response contains all details about all tariffs that make
sense for this use case.

Additionally there are the recommendations, which are made of the best tariff
results of every use case or if possible even combinding those results.

### Details

The following table lists the `attributes` of these objects:

| **Name**                                | **Type**        | **Example**                              | **Description**                                                                                           |
| --------------------------------------- | --------------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| input.battery_range                     | Array[Integer]  | [20,80]                                  |                                                                                                           |
| input.power_limit                       | Integer or null | 80                                       |                                                                                                           |
| input.winter_mode                       | Boolean or null | true                                     |                                                                                                           |
| input.vehicle                           | Relationship    | { "id": "123", "type": "car"}            |                                                                                                           |
| input.home_location                     | Location        | { "latitude": 47.12, "longitude": 12.34} |                                                                                                           |
| input.work_location                     | Location        | { "latitude": 47.12, "longitude": 12.34} |                                                                                                           |
| input.can_charge_at_home                | Boolean         | true                                     |                                                                                                           |
| input.can_charge_at_work                | Boolean         | true                                     |                                                                                                           |
| input.include_flat_rates                | Boolean         | true                                     |                                                                                                           |
| input.include_provider_customer_tariffs | Boolean         | true                                     |                                                                                                           |
| input.max_trip_distance                 | Integer         | 15000                                    | In Meters                                                                                                 |
| input.distance_per_month                | Integer         | 15000                                    | In Meters                                                                                                 |
| input.driving_use_cases                 | Array[String]   | ["trips"]                                | Posible values: "commute_to_work", "trips"                                                                |
| input.home_country                      | String          | "AT"                                     |                                                                                                           |
| input.currency                          | String          | "EUR"                                    |                                                                                                           |
| input.language                          | String          | "en"                                     |                                                                                                           |
| input.creation_application_id           | String          | "123"                                    |                                                                                                           |
| use_cases                               | Array           | See `Use Cases`                          | Detailed results per charging use case.                                                                   |
| recommendations                         | Array           | See `Recommendation`                     | Recommended tariffs for the user.                                                                         |
| status                                  | String          | "completed"                              | "pending": Still calculating, fetch the result in a few seconds again. "completed": Calculation complete. |

### Recommendation

| **Name**                    | **Type**      | **Example**                      | **Description**                                                            |
| --------------------------- | ------------- | -------------------------------- | -------------------------------------------------------------------------- |
| supported_charging_stations | Array[Object] | See `Charging Station Reference` | All charging stations in this area that work with the tariff.              |
| coverage                    | Float         | 0.66                             | How many charging stations in this area is this tariff covering? 0.5 = 50% |
| total_monthly_cost          | Float         | 120.5                            |                                                                            |
| tariff                      | Object        | See `Tariff`                     |                                                                            |
| price_distribution          | Object        | See below                        |                                                                            |
| price_distribution.session  | Float         | 0.4                              | Note: Key not always present!                                              |
| price_distribution.kwh      | Float         | 0.3                              | Note: Key not always present!                                              |
| price_distribution.min      | Float         | 0.3                              | Note: Key not always present!                                              |
| charges_blocking_fee        | Boolean       | true                             |                                                                            |
| covered_use_cases           | Array[String] | ["charge_at_home]                | Possible values: "charge_at_work","charge_at_home","trips"                 |

### Use Cases

| **Name**                            | **Type**      | **Example**                      | **Description**                                                            |
| ----------------------------------- | ------------- | -------------------------------- | -------------------------------------------------------------------------- |
| name                                | String        | "charge_at_work"                 | Possible values: "charge_at_work","charge_at_home","trips"                 |
| is_public_charging                  | Boolean       | true                             |                                                                            |
| charging_stations                   | Array[Object] | See `Charging Station Reference` |                                                                            |
| area                                | Object        | See `Geo Areas`                  |                                                                            |
| tariffs                             | Array[Object] | See below                        | All tarifs that have been considered for this charging use case.           |
| tariffs.supported_charging_stations | Array[Object] | See `Charging Station Reference` | All charging stations in this area that work with the tariff.              |
| tariffs.coverage                    | Float         | 0.66                             | How many charging stations in this area is this tariff covering? 0.5 = 50% |
| tariffs.total_monthly_cost          | Float         | 120.5                            |                                                                            |
| tariffs.tariff                      | Object        | See `Tariff`                     |                                                                            |
| tariffs.price_distribution          | Object        | See below                        |                                                                            |
| tariffs.price_distribution.session  | Float         | 0.4                              | Note: Key not always present!                                              |
| tariffs.price_distribution.kwh      | Float         | 0.3                              | Note: Key not always present!                                              |
| tariffs.price_distribution.min      | Float         | 0.3                              | Note: Key not always present!                                              |
| tariffs.charges_blocking_fee        | Boolean       | true                             |                                                                            |

### Charging Station Reference

| **Name** | **Type** | **Example**                       | **Description**                                                        |
| -------- | -------- | --------------------------------- | ---------------------------------------------------------------------- |
| id       | String   | "123"                             |
| type     | String   | "going_electric_charging_station" | Possible values: "going_electric_charging_station", "charging_station" |

### Tariff

| **Name**                         | **Type**       | **Example**                 | **Description**                                                                                                                                                   |
| -------------------------------- | -------------- | --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                               | String         | "123"                       |
| type                             | String         | "tariff"                    |                                                                                                                                                                   |
| name                             | String         | "Ladetarif S"               | Name of tariff.                                                                                                                                                   |
| monthly_min_sales                | Float          | 6.0                         |                                                                                                                                                                   |
| total_monthly_fee                | Float          | 5.9                         |                                                                                                                                                                   |
| is_flat_rate                     | Boolean        | true                        |                                                                                                                                                                   |
| is_direct_payment                | Boolean        | true                        |                                                                                                                                                                   |
| provider_customer_only           | Boolean        | true                        |                                                                                                                                                                   |
| branding                         | Object or null | See below                   |                                                                                                                                                                   |
| branding.background_color        | String         | "#ff0000"                   | Brand color to be used as background color to show the tariff.                                                                                                    |
| branding.text_color              | String         | "#000000"                   | Color to be used as text color to show the tariff.                                                                                                                |
| branding.logo_url                | String         | "https://logo.com/test.png" | Brand logo to be displayed next to the price.                                                                                                                     |
| branding.energy_type_restriction | String or null | "dc"                        | Possible values: null, "ac", "dc".                                                                                                                                |
| tags                             | Array          | -                           | Tags that are shown next to the tariff. E.g. "New Prices since 01.09.", "Club Membership required"                                                                |
| tags.kind                        | String         | "info"                      | Find all valid value [here](../../enums.md).                                                                                                                      |
| tags.text                        | String         | "Hello"                     | The main text of the tag.                                                                                                                                         |
| tags.url                         | String         | "http://xyz.com"            | A URL where the tag links to. Might contain `{locale}` (e.g. `http://test.com/{locale}/post.html`), which should be replaced by the current locale by the client. |
| emp                              | Object         | See below                           |                                                                                                                                                                   |
| emp.name                         | String         | "EnBW"                      | Name of EMP that provides the tariff.                                                                                                                             |
| emp.url                          | String         | "http://xyz.com"            |                                                                                                                                                                   |

### Geo Area

The attributes of a geo area depend on the type. The following types are
supported right now: 

#### Circle Geo Area

| **Name** | **Type** | **Example**                              | **Description** |
| -------- | -------- | ---------------------------------------- | --------------- |
| type     | String   | "circle_geo_area"                        |
| center   | Location | { "latitude": 47.12, "longitude": 12.34} |
| radius   | Integer  | 1000                                     | In Meters       |

## Example

### Request

```http
GET http://example-base-url.com/v1/my_best_tariffs/682b8b0e-ae9d-45ca-9cac-fc46566538dd
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 200 Ok

Body:
```json
{
    "data": {
        "id": "682b8b0e-ae9d-45ca-9cac-fc46566538dd",
        "type": "my_best_tariffs_result",
        "attributes": {
            "input": {
                "battery_range": [
                    20,
                    80
                ],
                "power_limit": null,
                "winter_mode": false,
                "vehicle": {
                    "id": "7de25a64-e9fa-484f-bf99-d02b02cfb17d",
                    "type": "car"
                },
                "home_location": {
                    "latitude": 47.02233175,
                    "longitude": 15.50879515
                },
                "work_location": {
                    "latitude": 46.8500596,
                    "longitude": 15.53169528
                },
                "can_charge_at_home": false,
                "can_charge_at_work": false,
                "include_flat_rates": false,
                "include_provider_customer_tariffs": false,
                "max_trip_distance": 150000,
                "distance_per_month": 1000000,
                "driving_use_cases": [
                    "commute_to_work",
                    "trips"
                ],
                "home_country": "AT",
                "currency": "EUR",
                "language": "en",
                "creation_application_id": "cc056786-54c7-4168-8abd-98d6b333998c"
            },
            "use_cases": [
                {
                    "name": "charge_at_home",
                    "is_public_charging": true,
                    "charging_stations": [
                        {
                            "id": "22813",
                            "type": "going_electric_charging_station"
                        }
                    ],
                    "area": {
                        "type": "circle_geo_area",
                        "center": {
                            "latitude": 47.02233175,
                            "longitude": 15.50879515
                        },
                        "radius_m": 2000
                    },
                    "tariffs": [
                        {
                            "supported_charging_stations": [
                                {
                                    "id": "22813",
                                    "type": "going_electric_charging_station"
                                }
                            ],
                            "coverage": 0.6666666666666666,
                            "total_monthly_cost": 172.6175,
                            "tariff": {
                                "id": "91bc4055-f4ae-4df2-a0e3-76c2d6d8c088",
                                "type": "tariff",
                                "name": "Tanke EXPERT",
                                "monthly_min_sales": 0,
                                "total_monthly_fee": 34.9,
                                "is_flat_rate": false,
                                "is_direct_payment": false,
                                "provider_customer_only": false,
                                "branding": {
                                   "background_color": "#e9f2d2",
                                    "text_color": "#000000",
                                    "logo_url": "https://www.chargeprice.app/themes/emc/logo.png",
                                    "energy_type_restriction": null
                                },
                                "tags": [],
                                "emp": {
                                    "name": "Wien Energie",
                                    "url": "https://api.chargeprice.app/v1/affiliate_redirect?token=dXJsPWh0dHBzJTNBJTJGJTJGd3d3LnRhbmtlLXdpZW5lbmVyZ2llLmF0JTJGdGFyaWZ1ZWJlcnNpY2h0JTJGJmNvbXBhbnlfaWQ9MGRlZDJkMTgtMzViMi00NjEwLTgwYmItNjc2OTRkNDcwYzBmJmFwcF9pZD1jYzA1Njc4Ni01NGM3LTQxNjgtOGFiZC05OGQ2YjMzMzk5OGM="
                                }
                            },
                            "price_distribution": {
                                "minute": 1
                            },
                            "charges_blocking_fee": false
                        }
                    ],
                    "charge_count": 4,
                    "total_charged_energy": 195.36
                },
                {
                    "name": "trips",
                    "is_public_charging": true,
                    "charging_stations": [
                        {
                            "id": "a9708239-3489-4f00-9f95-5eda72372d78",
                            "type": "charging_station"
                        },
                        {
                            "id": "48427",
                            "type": "going_electric_charging_station"
                        }
                    ],
                    "area": {
                        "type": "circle_geo_area",
                        "center": {
                            "latitude": 47.02233175,
                            "longitude": 15.50879515
                        },
                        "radius_m": 75000
                    },
                    "tariffs": [
                        {
                            "supported_charging_stations": [
                                {
                                    "id": "a9708239-3489-4f00-9f95-5eda72372d78",
                                    "type": "charging_station"
                                }
                            ],
                            "coverage": 0.21333333333333335,
                            "total_monthly_cost": 43.78683249999998,
                            "tariff": {
                                "id": "24df192d-f400-4173-80ad-94ff888f7862",
                                "type": "tariff",
                                "name": "Chargemap",
                                "monthly_min_sales": 0,
                                "total_monthly_fee": 0,
                                "is_flat_rate": false,
                                "is_direct_payment": false,
                                "provider_customer_only": false,
                                "branding": null,
                                "tags": [],
                                "emp": {
                                    "name": "Chargemap",
                                    "url": "https://api.chargeprice.app/v1/affiliate_redirect?token=dXJsPWh0dHAlM0ElMkYlMkZjaGFyZ2VtYXAuY29tJTJGJmNvbXBhbnlfaWQ9MTE2NmZlNTktMTVlYy00MjM0LWI2OWQtNjhiNGVmNzRjMWFkJmFwcF9pZD1jYzA1Njc4Ni01NGM3LTQxNjgtOGFiZC05OGQ2YjMzMzk5OGM="
                                }
                            },
                            "price_distribution": {
                                "kwh": 0.936197791785349,
                                "minute": 0.06380220821465094
                            },
                            "charges_blocking_fee": true
                        }
                    ],
                    "charge_count": 1,
                    "total_charged_energy": 48.84
                }
            ],
            "recommendations": [
                {
                    "supported_charging_stations": [
                        {
                            "id": "22813",
                            "type": "going_electric_charging_station"
                        }
                    ],
                    "coverage": 0.6666666666666666,
                    "total_monthly_cost": 60.152,
                    "tariff": {
                        "id": "62878907-189a-403b-aa03-a5ceb2276a8f",
                        "type": "tariff",
                        "name": "OMV Mobility Card - Pauschal",
                        "monthly_min_sales": 0,
                        "total_monthly_fee": 5,
                        "is_flat_rate": false,
                        "is_direct_payment": false,
                        "provider_customer_only": false,
                        "branding": null,
                        "tags": [
                            {
                                "kind": "lock",
                                "text": "Only for businesses",
                                "url": ""
                            }
                        ],
                        "emp": {
                            "name": "OMV E-Mobility",
                            "url": "https://api.chargeprice.app/v1/affiliate_redirect?token=dXJsPWh0dHBzJTNBJTJGJTJGd3d3Lm9tdi5hdCUyRmRlLWF0JTJGYnVzaW5lc3Nsb2VzdW5nZW4lMkZ0YW5ra2FydGVuJTJGb212LWUtbW9iaWxpdHktY2FyZCZjb21wYW55X2lkPTRlM2YxMmM5LTAxMjktNDE2ZC1iNmFhLWM5ZjMzNmI4YTE4MiZhcHBfaWQ9Y2MwNTY3ODYtNTRjNy00MTY4LThhYmQtOThkNmIzMzM5OThj"
                        }
                    },
                    "price_distribution": {
                        "session": 1
                    },
                    "charges_blocking_fee": false,
                    "covered_use_cases": [
                        "charge_at_home"
                    ]
                },
                {
                    "supported_charging_stations": [
                        {
                            "id": "a9708239-3489-4f00-9f95-5eda72372d78",
                            "type": "charging_station"
                        }
                    ],
                    "coverage": 0.94,
                    "total_monthly_cost": 31.912048226950418,
                    "tariff": {
                        "id": "dba260fc-f006-4ea8-881e-6b9f3f4e0528",
                        "type": "tariff",
                        "name": "EnBW Ladetarif S",
                        "monthly_min_sales": 0,
                        "total_monthly_fee": 0,
                        "is_flat_rate": false,
                        "is_direct_payment": false,
                        "provider_customer_only": false,
                        "branding": {
                            "background_color": "#000099",
                            "text_color": "#ffffff",
                            "logo_url": "https://www.chargeprice.app/img/emps/enbw.png",
                            "energy_type_restriction": "dc"
                        },
                        "tags": [
                            {
                                "kind": "info",
                                "text": "Register now for free",
                                "url": "https://api.chargeprice.app/v1/affiliate_redirect?token=dXJsPWh0dHBzJTNBJTJGJTJGYXBwLmFkanVzdC5jb20lMkYxOWV6a21tJTNGZmFsbGJhY2slM0RodHRwcyUyNTNBJTI1MkYlMjUyRnd3dy5lbmJ3LmNvbSUyNTJGZWxla3Ryb21vYmlsaXRhZXQlMjUyRnByb2R1a3RlJTI1MkZsYWRldGFyaWZlJmNvbXBhbnlfaWQ9OGMyMWMyZGEtZWUwNy00OTMwLWJhZDItOWMwODYwZGFjNjJmJmFwcF9pZD1jYzA1Njc4Ni01NGM3LTQxNjgtOGFiZC05OGQ2YjMzMzk5OGM="
                            }
                        ],
                        "emp": {
                            "name": "EnBW",
                            "url": "https://api.chargeprice.app/v1/affiliate_redirect?token=dXJsPWh0dHBzJTNBJTJGJTJGYXBwLmFkanVzdC5jb20lMkYxOWV6a21tJTNGZmFsbGJhY2slM0RodHRwcyUyNTNBJTI1MkYlMjUyRnd3dy5lbmJ3LmNvbSUyNTJGZWxla3Ryb21vYmlsaXRhZXQlMjUyRnByb2R1a3RlJTI1MkZsYWRldGFyaWZlJmNvbXBhbnlfaWQ9OGMyMWMyZGEtZWUwNy00OTMwLWJhZDItOWMwODYwZGFjNjJmJmFwcF9pZD1jYzA1Njc4Ni01NGM3LTQxNjgtOGFiZC05OGQ2YjMzMzk5OGM="
                        }
                    },
                    "price_distribution": {
                        "kwh": 0.9994063564080551,
                        "minute": 0.0005936435919449525
                    },
                    "charges_blocking_fee": true,
                    "covered_use_cases": [
                        "trips"
                    ]
                }
            ],
            "status": "completed"
        },
        "links": {
            "refresh": "https://api.chargeprice.app/v1/my_best_tariffs/682b8b0e-ae9d-45ca-9cac-fc46566538dd"
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