# POST /v1/charge_prices

Gets the prices of available tariffs for a single charge, given a set of options (duration, kwh).

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (sales@chargeprice.net to get access)
* `Content-Type: application/json`
* `Accept-Language: en`: [Valid Languages](../../enums.md). Used to localize the response, default language is `en`.

## Authorization Group

`ReadCosts`

## Request

The following fields are to be sent in the request body, in the `attributes` section of a `charge_price_request` object:

| **Name**                                  | **Type**      | **Presence**                      | **Example**                            | **Description**                                                                                                                                               |
|-------------------------------------------|---------------|-----------------------------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| data_adapter                              | string        | required                          | "chargeprice"                          | The source of the charging station data. In most cases "chargeprice". Other supported values: "going_electric", "open_charge_map"                             |
| station                                   | Object        | required                          |                                        | Station related request data                                                                                                                                  |
| station.longitude                         | Float         | required                          | 43.123                                 | Longitude coordinate of the station                                                                                                                           |
| station.latitude                          | Float         | required                          | 123.456                                | Latitude coordinate of the station                                                                                                                            |
| station.country                           | String        | required                          | "DE"                                   | Country in which the station is located. For data adapter "chargeprice": ISO 3166 country code.                                                               |
| station.network                           | String        | required                          | "6e62cd2d-f29d-4107-b087-6d4f75261cce" | Network/CPO to which the station belongs to. For data adapter "chargeprice": The CPO/Operator ID.                                                             |
| station.charge_points                     | Array[Object] | required                          |                                        | All kinds of connectors that are available at a station.                                                                                                      |
| station.charge_points.power               | Float         | required                          | 22                                     | In kW                                                                                                                                                         |
| station.charge_points.plug                | String        | required                          | "ccs"                                  | Type of plug at charge point. Possible values for data adapter "chargeprice": `ccs`, `chademo`, `type2`, `type1`, `type3`, `schuko`, `tesla_ccs`, `tesla_suc` |
| options                                   | Object        | required                          |                                        | Charge related request data                                                                                                                                   |
| options.max_monthly_fees                  | Float         | optional                          | 0                                      | Only returns tariffs, where `total_monthly_fee` + `monthly_min_sales` <= the given value.                                                                     |
| options.energy                            | Float         | See **Duration and Energy Input** | 30                                     | Total energy to be charged in kWh                                                                                                                             |
| options.duration                          | Float         | See **Duration and Energy Input** | 45                                     | Duration of the charge in minutes                                                                                                                             |
| options.battery_range                     | Array[Float]  | See **Duration and Energy Input** | [20.0,80.0]                            | Array with 2 values, that defines the battery start and end values for the charge in percentage.                                                              |
| options.car_ac_phases                     | Integer       | optional (default: 3)             | 3                                      | Number of AC phases the car can use to charge. Possible values: 1,3                                                                                           |
| options.currency                          | String        | optional (default: "EUR")         | "EUR"                                  | Currency in which the prices should be returned. Possible values are listed at the [eurofxref]                                                                |
| options.start_time                        | Integer       | optional (default: 12:00)         | 720 (=12:00)                           | Time of day in minutes when the charging session gets started. Min value: 0 (00:00), max value: 1439 (23:59)                                                  |
| options.allow_unbalanced_load             | Boolean       | optional (default: false)         | false                                  | If true, it allows higher powers for uniphase charging vehicles. If false, power is restricted to 4.5 kW.                                                     |
| options.provider_customer_tariffs         | Boolean       | optional (default: false)         | false                                  | Include prices of tariffs, that are only available for customers of a provider (e.g. electricity provider for the home).                                      |
| options.foreign_tariffs                   | Boolean       | optional (default: true)          | false                                  | Include prices of tariffs that are supposed to be used in other countries or by people from other countries.                                                  |
| options.show_price_unavailable            | Boolean       | optional (default: false)         | false                                  | Include tariffs, which are available at the provided CPO, but no price information is availble.                                                               |
| options.show_all_brand_restricted_tariffs | Boolean       | optional (default: false)         | false                                  | `false`: Include vehicle brand restricted tariffs only if the requested vehicle matches the brand. `true`: Include vehicle brand restricted tariffs always.   |

The following table lists the `relationships` section of a `charge_prices_request` object:

| **Name**       | **Type**               | **Presence**                                           | **Example**                             | **Description**                                                                                                                                                                                                                                                                        |
|----------------|------------------------|--------------------------------------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tariffs        | Array of Relationships | optional (default: all available tariffs are returned) | `[{"id": "some-uuid", type:"tariff" }]` | Only return charge prices for the given tariffs. See [GET v1/tariffs](../tariffs/index.md) for the valid options.                                                                                                                                                                      |
| tariff.include | String                 | optional (default: `filter`)                           | `"filter"`                              | `filter`: Only the tariffs listed should be returned and other filters should apply as well. `always`: The listed tariffs should always be returned, even if another filter would remove them. `exclusive`: Always only include the listed tariffs, eve if a filter would remove them. |
| vehicle        | Relationship           | See **Duration and Energy Input**                      | `{"id": "some-uuid", type:"car" }`      | Vehicle at charge. See [GET v1/vehicles](../vehicles/index.md) for the valid options.                                                                                                                                                                                                  |

\* Relationships are represented as id+type pairs. E.g.
```json
{
  "id": "123",
  "type": "tariff"
}
```

### Duration and Energy Input

One set of the following values needs to be given:

* `vehicle` + `battery_range`: Energy and duration depends on the capabilities of the car and each charge point.
* `energy` + `duration`: Energy and duration is the same - and equal to the input values - for all charge points.

If both or no values are given, an error is raised.


## Response Body

A response contains 0 to many `charge_price` objects, which define the prices per charge of a tariff per charge point.
The following table lists the `attributes` of these objects:

| **Name**                               | **Type**        | **Example**                       | **Description**                                                                                                                                                                                                                              |
|----------------------------------------|-----------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| provider                               | String          | "Maingau Energie"                 | Name of the charge card provider                                                                                                                                                                                                             |
| tariff_name                            | String or null  | "EinfachStromLaden"               | Name of the tariff, if available                                                                                                                                                                                                             |
| url                                    | String          | "http://my.tarif.at/prices"       | Link to tariff at the website of the provider                                                                                                                                                                                                |
| monthly_min_sales                      | Float           | 12.49                             | Minimum charging costs per month                                                                                                                                                                                                             |
| total_monthly_fee                      | Float           | 12.30                             | Any monthly fee + any yearly (service) fee (proportional)                                                                                                                                                                                    |
| flat_rate                              | Boolean         | true                              | Given a monthly fee, charging with this tariff is at no extra cost for a single charge                                                                                                                                                       |
| direct_payment                         | Boolean         | true                              | This tariff can be used without registration                                                                                                                                                                                                 |
| provider_customer_tariff               | Boolean         | true                              | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).                                                                                                                                      |
| currency                               | String          | "EUR"                             | Currency of the prices or fees                                                                                                                                                                                                               |
| tags                                   | Array           | -                                 | Tags that are shown next to the tariff. E.g. "New Prices since 01.09.", "Club Membership required"                                                                                                                                           |
| tags.kind                              | String          | "info"                            | Find all valid value [here](../../enums.md).                                                                                                                                                                                                 |
| tags.text                              | String          | "Hello"                           | The main text of the tag.                                                                                                                                                                                                                    |
| tags.url                               | String          | "http://xyz.com"                  | A URL where the tag links to. Might contain `{locale}` (e.g. `http://test.com/{locale}/post.html`), which should be replaced by the current locale by the client.                                                                            |
| start_time                             | Integer         | 720                               | Time of day in minutes when the charging session gets started.                                                                                                                                                                               |
| branding                               | Hash or null    | -                                 | If branding for the tariff is not available, then `null`                                                                                                                                                                                     |
| branding.background_color              | String          | "#ff0000"                         | Brand color to be used as background color to show the tariff.                                                                                                                                                                               |
| branding.text_color                    | String          | "#000000"                         | Color to be used as text color to show the tariff.                                                                                                                                                                                           |
| branding.logo_url                      | String          | "https://logo.com/test.png"       | Brand logo to be displayed next to the price.                                                                                                                                                                                                |
| charge_point_prices                    | Array[Object]   |                                   |                                                                                                                                                                                                                                              |
| charge_point_prices.plug               | String          | "ac"                              | Name of plug at charge point                                                                                                                                                                                                                 |
| charge_point_prices.power              | Float           | 22                                | In kW                                                                                                                                                                                                                                        |
| charge_point_prices.price              | Float or null   | 8.43                              | Cost of a single charge at the given connector, given the options of the request. `null` if the tariff is available, but no price could have been calculated. `null` will only be returned if `show_price_unavailable: true` in the request. |
| charge_point_prices.price_distribution | Hash            | { "session": 0.4, "minute": 0.6 } | Distribution of price in percentage of each dimension which applies (session, kwh and/or minute)                                                                                                                                             |
| charge_point_prices.blocking_fee_start | Integer or null | 240                               | If the tariffs includes a **blocking fee**, the attributes defines when it starts to count (in minutes from start of charge)                                                                                                                 |
| charge_point_prices.no_price_reason    | String or null  | "Prices per station"              | If `show_price_unavailable: true` is set in the request, tariffs which are available at this CPO, but no price could have been calculated, will be returned with a reason (translated) why no price could have been calculated.              |

The following table lists the `relationships`:

| **Name** | **Type**     | **Example**                           | **Description**                                                                                                     |
|----------|--------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| tariff   | Relationship | `{"id": "some-uuid", type:"tariff" }` | The tariff that was applied for this charge price. See [GET v1/tariffs](../tariffs/index.md) for the valid options. |

The following table lists the `links`. This section is optional and only available if at least one link is present.

| **Name**            | **Type** | **Example**                 | **Description**                                                                                                                |
|---------------------|----------|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| open_app_at_station | String   | `https://something.com/123` | An HTTP link that should deeplink into the application of the provider at the current charging station. This link is optional. |


The following table lists the `meta` data:

| **Name**               | **Type**      | **Example** | **Description**                                                                           |
|------------------------|---------------|-------------|-------------------------------------------------------------------------------------------|
| charge_points          | Array[Object] |             |                                                                                           |
| charge_points.plug     | String        | "ac"        | Type of plug at charge point (as provided in the request)                                 |
| charge_points.power    | Float         | 22          | In kW                                                                                     |
| charge_points.duration | Float         | 30.0        | Charging duration (minutes) based on the input data (duration or vehicle + battery range) |
| charge_points.energy   | Float         | 11.0        | Energy to be charged (kWh) based on the input data (duration or vehicle + battery range)  |

### Definitions

What is a *blocking fee*?
- There is a minute-based tariff component
- Which starts *at some point* after the charging session started (= NOT right
  from the start)
- There is **NO** other minute-based tariff component, which starts to count
  directly from the start

## Example

### Request

```http
POST http://example-base-url.com/v1/charge_prices
Content-Type: application/json
Api-Key: my-secret-key
Accept-Language: de
```

Body:

with `energy`, `duration` and `tariffs`.
using "chargeprice" data adapter.

```json
{
  "data": {
    "type":       "charge_price_request",
    "attributes": {
      "data_adapter": "chargeprice",
      "station":         {
        "longitude":  14.13117,
        "latitude":   48.289453,
        "country":    "AT",
        "network":    "6e62cd2d-f29d-4107-b087-6d4f75261cce",
        "charge_points": [
          {
            "power":  22,
            "plug":   "ccs"
          }
        ]
      },
      "options":         {
        "energy":   30,
        "duration": 30,
        "start_time": 720
      }
    },
    "relationships": {
      "tariffs": {
        "data": [
          {
            "id": "c6adf982-4e41-431b-833a-dfa89b484c75",
            "type": "tariff"
          }
        ],
        "meta": {
          "include": "filter"
        }
      }
    }
  }
}
```

with `vehicle` and `battery_range` given:
using "going_electric" data adapter.

```json
{
  "data": {
    "type":       "charge_price_request",
    "attributes": {
      "data_adapter": "going_electric",
      "station":         {
        "longitude":  14.13117,
        "latitude":   48.289453,
        "country":    "Österreich",
        "network":    "Wien Energie",
        "charge_points": [
          {
            "power":  22,
            "plug":   "CCS"
          }
        ]
      },
      "options":         {
        "battery_range": [20.0,80.0]
      }
    },
    "relationships": {
      "vehicle": {
        "data": {
          "id": "46adf982-4e41-431b-833a-dfa89b484c71",
          "type": "car"
        }
      }
    }
  }
}
```

### Response

#### 200 Ok

Body:
```json
{
  "data": [
    {
      "id": "dcb281b8-f4f3-470c-938b-5f94cb265f6a",
      "type": "charge_price",
      "attributes": {
        "provider": "Maingau Energie",
        "tariff_name": "EinfachStromLaden",
        "url": "https://www.maingau-energie.de/e-mobilit%C3%A4t/autostrom-tarif",
        "monthly_min_sales": 0,
        "total_monthly_fee": 0,
        "flat_rate": false,
        "direct_payment": false,
        "provider_customer_only": false,
        "start_time": 720,
        "currency": "EUR",
        "branding": {
          "background_color": "#ff0000",
          "text_color": "#000000",
          "logo_url": "http://www.cp.app/logo.png"
        },
        "charge_point_prices": [
          {
            "power":  22,
            "plug":   "ccs",
            "price":  12,
            "price_distribution": {
              "kwh": 0.3,
              "minute": 0.1,
              "session": 0.6
            },
            "no_price_reason": "This price is not yet listed in our system."
          }
        ],
        "tags": [
          {
            "kind": "alert",
            "text": "Hallo Welt",
            "url": "http://www.google.at"
          }
        ],
      },
      "relationships": {
        "tariff": {
          "data": {
            "id": "c6adf982-4e41-431b-833a-dfa89b484c75",
            "type": "tariff"
          }
        } 
      },
      "links": {
        "open_app_at_station": "https://something.com/123"
      }
    }
  ],
  "meta": {
    "charge_points": [
      {
        "power": 22,
        "plug": "ccs",
        "duration": 30.0,
        "energy": 12.0
      }
    ]
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

##### 404 Not Found

e.g. The provided currency doesn't exist in the system.

```json
{
  "errors": [
    {
      "status": "404",
      "code": "NOT_FOUND",
      "title": "No currency with iso ATS"
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

[eurofxref]: https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html
