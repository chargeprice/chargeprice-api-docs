# POST /v1/tariff_details

Get details of multiple tariffs given a charge at...

* operator
* in a country

This API follows the <https://jsonapi.org> specification.

## Authorization Group

`ViewPriceBenchmark`

## Headers

* `API-Key: <your_api_key>` (contact <sales@chargeprice.net> to get access)
* `Content-Type: application/json`

## Request

The following fields are to be sent in the request body, in the `attributes` section:

| **Name**                         | **Type**      | **Presence** | **Example**                            | **Description**                                                                                                                                                                                                                                                                                                                                                                                              |
|----------------------------------|---------------|--------------|----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| station                          | Object        | required     |                                        | Station configuration root object.                                                                                                                                                                                                                                                                                                                                                                           |
| station.country                  | String        | optional*    | `AT`                                   | ISO 3166 code of the country where the prices should be fetched                                                                                                                                                                                                                                                                                                                                              |
| station.id                       | Array<String> | optional*    | `["some-uuid"]`                        | Return prices for the given stations. If not provided, only CPO based prices will be returned. Max. number of IDs: 50                                                                                                                                                                                                                                                                                        |
| station.operator                 | Object        | optional*    |                                        | Operator of the station                                                                                                                                                                                                                                                                                                                                                                                      |
| station.operator.id              | String        | required     | `20006f18-3ed4-4715-92b5-08e37e6dd18c` | ID of the operator company                                                                                                                                                                                                                                                                                                                                                                                   |
| station.operator.type            | String        | required     | `company`                              | Type of the company (always `company` for now)                                                                                                                                                                                                                                                                                                                                                               |
| station.charge_point             | Object        | optional     |                                        | If provided, the result will only contain segments that match the given charge point. If not provided, all segments for the CPO (or station) x tariff combination will be returned.                                                                                                                                                                                                                          |
| station.charge_point.power       | Float         | required     | `22.0`                                 | Filter prices for only this power value.                                                                                                                                                                                                                                                                                                                                                                     |
| station.charge_point.plug        | String        | required     | `"type2"`                              | Filter prices for only this plug. [See allowed values](../../enums.md#plugs)                                                                                                                                                                                                                                                                                                                                 |
| filter.brand_restricted_tariffs  | Boolean       | optional     | `true`                                 | Include tariffs restricted to specific vehicle brands. Default: `true`.                                                                                                                                                                                                                                                                                                                                      |
| filter.foreign_tariffs           | Boolean       | optional     | `true`                                 | Include tariffs restricted to specific countries. Default: `true`.                                                                                                                                                                                                                                                                                                                                           |
| filter.provider_customer_tariffs | Boolean       | optional     | `true`                                 | Include tariffs restricted to provider customers. Default: `false`.                                                                                                                                                                                                                                                                                                                                          |
| filter.tariffs_without_prices    | Boolean       | optional     | `true`                                 | Include tariffs, which are available at the provided CPO, but no price information is available. Default: `false`.                                                                                                                                                                                                                                                                                           |
| filter.average_prices            | Boolean       | optional     | `true`                                 | For CPOs that have different prices for each station, we optionally provide an average of these POI-based prices to make analytics easier. If `true` these average prices will be returned in the response. If `false` they won't be included. This option is only relevant when a `station.operator` (CPO) is passed. If station IDs are provided there can anyway not be average prices. Default: `false`. |
| filter.dimensions                | Array<String> | optional     | `["minute","kwh","session"]`           | Only return `restricted_segments` with the given dimensions. Possible values: See `restricted_segments.dimension`. Default: `["minute","kwh","session"]`                                                                                                                                                                                                                                                     |

\* Allowed combinations for the station configuration:
- country + operator.id + operator.type
- id

The following table lists the `relationships` section:

| **Name** | **Type**               | **Presence** | **Example**                             | **Description**                                   |
|----------|------------------------|--------------|-----------------------------------------|---------------------------------------------------|
| tariffs  | Array of Relationships | optional     | `[{"id": "some-uuid", type:"tariff" }]` | Details for the those tariffs should be returned. |

### Includes

The `emp` and `tariff` relationship are included per default.
See the response of the `included` section. 

### Restrictions when requesting with multiple station IDs

If the `station.id` attribute in the request contains more than 1 station IDs it is also required to provide a list of tariff IDs and the filter will be ignored.
The reason for this is that the response could be extremely huge if you request tariff details for many stations without limiting the tariffs.

The main use case for requesting tariff details for multiple stations is to fetch station-based prices for a small set of tariffs. 
Example: Fetching the Tesla Membership and Non-Membership prices for a set of Tesla stations.

## Response Body

The following table lists the `attributes` of these objects:

| **Name**                                        | **Type**        | **Example**         | **Description**                                                                                                                                                                                                                                                                                                                                                                                                       |
|-------------------------------------------------|-----------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| country                                         | String          | `AT`                | ISO 3166 code of the country for which the prices are defined.                                                                                                                                                                                                                                                                                                                                                        |
| updated_at                                      | Timestamp       | 1664446527000       | Time when the tariff has been updated                                                                                                                                                                                                                                                                                                                                                                                 |
| is_roaming                                      | Boolean         | `true`              | True if the tariff is from a roaming EMP, false if it's from the CPO directly.                                                                                                                                                                                                                                                                                                                                        |
| tariff_level                                    | String          | `cpo`               | At which level this tariff applies: `country`: the default tariff for all CPOs in this country, `cpo`: tariff applies to all EVSEs of this CPO, `evse`: tariff applies only to a specific EVSE or location.                                                                                                                                                                                                           |
| restricted_segments                             | Array<Object>   | -                   |                                                                                                                                                                                                                                                                                                                                                                                                                       |
| restricted_segments.dimension                   | String          | `minute`            | Either "minute" (while charging), "parking_minute" (after charging), "kwh" or "session"                                                                                                                                                                                                                                                                                                                               |
| restricted_segments.price                       | Float           | 0.39                | The price per dimension.                                                                                                                                                                                                                                                                                                                                                                                              |
| restricted_segments.range_gte                   | Integer         | 60                  | From which dimension value the price applies. e.g. from 60 minutes on.                                                                                                                                                                                                                                                                                                                                                |
| restricted_segments.range_lt                    | Integer         | 120                 | Until which dimension value the price applies.      e.g. until 120 minutes.                                                                                                                                                                                                                                                                                                                                           |
| restricted_segments.billing_increment           | Float           | 10                  | e.g. if dimension is `minute` and billing increment is 10, the customer is charged in blocks of any started 10 minutes.                                                                                                                                                                                                                                                                                               |
| restricted_segments.currency                    | String          | `EUR`               | The currency of this component. Overrules the `currency` of the tariff.                                                                                                                                                                                                                                                                                                                                               |
| restricted_segments.time_of_day_start           | Integer         | 720                 | Time of day when this segment starts to match. In minutes.                                                                                                                                                                                                                                                                                                                                                            |
| restricted_segments.time_of_day_end             | Integer         | 1200                | Time of day when this segment stops to match. In minutes.                                                                                                                                                                                                                                                                                                                                                             |
| restricted_segments.charge_point_powers         | Array<Float>    | [0.0,22.0]          | List of power value restrictions of a charge point. If empty, all power values match. Also see `charge_point_power_is_range` on how to use this field.                                                                                                                                                                                                                                                                |
| restricted_segments.charge_point_energy_type    | String          | `ac`                | Matching power/phase. Either "ac", "dc" or `null` if it applies for both.                                                                                                                                                                                                                                                                                                                                             |
| restricted_segments.car_ac_phase                | Integer         | 3                   | **Deprecated: This field is not used anymore, you can ignore it.**<br>Matching AC phases of the car (legacy attribute, practically not used anymore by EMSPs).                                                                                                                                                                                                                                                        |
| restricted_segments.charge_point_power_is_range | Boolean         | `true`              | `true`: List of `charge_point_powers` has two values, which define a range of matching power values (Example: [11,22] => e.g. 11, 15 and 22 kW charge points match). `false`: Only specifc values are matching (Example: [11,22] => e.g. 11 and 22 kW charge points match, 15 kW not).                                                                                                                                |
| restricted_segments.use_consumed_charging_power | Boolean         | `false`             | **Note: This field is currently only used for a very small amount of tarrifs in France. If you don't need data from France, you can ignore it.**<br>if `true`: The consumed power of the car defines the price, instead of the maximum power of the station. `false`: Maximum power of the station defines the price.                                                                                                 |
| restricted_segments.is_average_price            | Boolean         | `false`             | If true, the price is not applied to the whole network of a CPO, but it is just an average price from all POIs.                                                                                                                                                                                                                                                                                                       |
| restricted_segments.occupancy_gte               | Integer or null | `20`                | Inclusive minimum occupancy rate (in percent) of the charging site to activate this segment. If `null`, segment is active always.                                                                                                                                                                                                                                                                                               |
| restricted_segments.occupancy_lt                | Integer or null | `80`                | Exclusive maximum occupancy rate (in percent) of the charging site to activate this segment. If `null`, segment is active always.                                                                                                                                                                                                                                                                                              |
| no_price_reason                                 | String or null  | `price_per_station` | If this value is set, it indicates the reason why the `restricted_segments` attribute doesn't contain price information for a specifc charge point type. It can happen that a price is available for a 50 kW DC station, but not for a 22 kW AC station. The value of `no_price_reason` is only relevant when no price is available, in this case for 22 kW AC. [See possible values](../../enums.md#no-price-reason) |
| prices_per_station_available                    | Boolean         | `false`             | Indicates if prices per station are available for this CPO and tariff.                                                                                                                                                                                                                                                                                                                                                |

The following table lists the `relationships` and their values in the `included` section:

| **Name**                          | **Type**           | **Example**                                     | **Description**                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------------------------------|--------------------|-------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tariff                            | Relationship       | `{"id": "some-uuid", type:"tariff" }`           | The tariff for which the details are given.                                                                                                                                                                                                                                                                                                                                                                              |
| tariff.name                       | String             | `easyFlex`                                      | Name of the tariff                                                                                                                                                                                                                                                                                                                                                                                                       |
| tariff.total_monthly_fee          | Float              | 10.0                                            | Monthly fee incl. a 12th of any yearly fee.                                                                                                                                                                                                                                                                                                                                                                              |
| tariff.is_direct_payment          | Boolean            | `true`                                          | This tariff can be used without registration                                                                                                                                                                                                                                                                                                                                                                             |
| tariff.is_card_payment            | Boolean            | `true`                                          | This tariff applies to card payments at stations with a terminal                                                                                                                                                                                                                                                                                                                                                         |
| tariff.provider_customer_only     | Boolean            | `true`                                          | If true, tariff is only available for customers of a provider (e.g. electricity provider for the home).                                                                                                                                                                                                                                                                                                                  |
| tariff.existing_customer_only     | Boolean            | `false`                                         | If true, tariff is only available for existing customers and a registration for new customers is not possible anymore.                                                                                                                                                                                                                                                                                                   |
| tariff.allowed_customer_countries | Array              | `["AT"]`                                        | Customers from these countries are allowed to subscribe to this tariff or at least the target customer is from this country. EMPs sometimes strictly enforce this, but often not. If the array is empty, customers from all countries can subscribe. Example: If value is ["IT","DE"] only customers living in Italy and Germany are allowed to subscribe to this tariff, even if you can charge with it also in France. |
| tariff.currency                   | String             | `EUR`                                           | Main currency of the tariff. Applies to e.g. the monthly fee. Currency of prices can vary country by country.                                                                                                                                                                                                                                                                                                            |
| tariff.url                        | String             | `http://www.google.at`                          | Website of the tariff.                                                                                                                                                                                                                                                                                                                                                                                                   |
| tariff.vehicle_brands             | Relationship Array | -                                               | Only owners of these vehicle brands are allowed to subscribe to this tariff.                                                                                                                                                                                                                                                                                                                                             |
| emp                               | Relationship       | `{"id": "some-uuid", type:"company" }`          | The EMP (E-Mobility Service Provider) who offers the tariff.                                                                                                                                                                                                                                                                                                                                                             |
| emp.name                          | String             | `Energie Steiermark`                            | Company name of the EMP                                                                                                                                                                                                                                                                                                                                                                                                  |
| cpo                               | Relationship       | `{"id": "some-uuid", type:"company" }`          | The CPO (Charge Point Operator) to which this tariff applies.                                                                                                                                                                                                                                                                                                                                                            |
| station                           | Relationship       | `{"id": "some-uuid", type:"charging_station" }` | The charging station to which this tariff applies. Only set if `station.id` was provided in the request.                                                                                                                                                                                                                                                                                                                 |

Timestamp = Millis since 1.1.1970

### OCPI Attribute Mapping

My users of the API will be used to the OCPI standard, so we provide a mapping of the Chargeprice attributes to the OCPI Tariffs attributes.
For attributes that are not in this table, there is no 100% matching equivalent in OCPI, so we don't map them to avoid confusion.

| **Chargeprice Attribute**             | **OCPI Tariffs Attribute** | **Value Mapping**                                                                                                                                                                                                                                                           |
|---------------------------------------|----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| restricted_segments.dimension         | price_components.type      | minute=TIME<br>kwh=ENERGY<br>session=FLAT<br>parking_minute=PARKING_TIME                                                                                                                                                                                                                                   |
| restricted_segments.price             | price_components.price     | Chargeprice attribute includes VAT, while OCPI attribute doesn't include VAT.                                                                                                                                                                                               |
| restricted_segments.billing_increment | price_components.step_size | Dimension `minute` => Billing Increment in minutes. OCPI attribute is in seconds for TIME. Multiple by 60 to get the OCPI equivalent.<br>Dimension `kwh` => Billing Increment is in kWh. OCPI attribute is in Watt for ENERGY. Multiple by 1000 to get the OCPI equivalent. |
| restricted_segments.range_gte         | restrictions.min_duration  | Chargeprice attribute is in minutes, while OCPI attribute is in seconds. Multiple by 60 to get the OCPI equivalent.                                                                                                                                                         |
| restricted_segments.range_lt          | restrictions.max_duration  | Chargeprice attribute is in minutes, while OCPI attribute is in seconds. Multiple by 60 to get the OCPI equivalent.                                                                                                                                                         |
| restricted_segments.time_of_day_start | restrictions.start_time    | Chargeprice attribute is in minutes since the start of the day. OCPI attribute is a string in format `HH:mm`. Integer divide by 60 to get the hourse (`HH`) and do a modulo operation to get the minutes (`mm`) part.                                                       |
| restricted_segments.time_of_day_end   | restrictions.end_time      | Chargeprice attribute is in minutes since the start of the day. OCPI attribute is a string in format `HH:mm`. Integer divide by 60 to get the hourse (`HH`) and do a modulo operation to get the minutes (`mm`) part.                                                       |

### Unit prices for a specifc charge point

The `restricted_segments` array contains a list of all unit prices that define an EMP tariff at a specific CPO for all charge points.

In order to get the unit prices for a specifc charge point (e.g. 22 kW AC) you need to filter the `restricted_segments` and only use those array items, that are matching the charging station configuration. Note that one segment can be used by multiple charging station configurations. e.g. there could be a session fee, which applies to all power levels and it might only listed once in the `restricted_segments`.

To find all matching segments you need to filter using the following attributes:

* charge_point_energy_type
* charge_point_powers
* charge_point_power_is_range
* use_consumed_charging_power
* car_ac_phase

## Examples

### Example 1: Request CPO based prices of all tariffs for the given operator ID in Austria

```http
POST http://example-base-url.com/v1/tariff_details
Content-Type: application/json
Api-Key: my-secret-key
```

Body:

```json
{
  "data": {
    "attributes": {
      "station": {
        "country": "AT",
        "operator": {
          "id": "50006f18-3ed4-4715-92b5-08e37e6dd18c",
          "type": "company"
        }
      },
      "filter": {
        "brand_restricted_tariffs": true,
        "foreign_tariffs": true,
        "provider_customer_tariffs": true,
        "tariffs_without_prices": false
      }
    }
  }
}
```

### Example 2: Request prices of the given tariff at the given station

```http
POST http://example-base-url.com/v1/tariff_details
Content-Type: application/json
Api-Key: my-secret-key
```

Body:

```json
{
  "data": {
    "attributes": {
      "station": {
        "id": ["71006f18-3ed4-4715-92b5-08e37e6dd180"],
      }
    },
    "relationships": {
      "tariffs": {
        "data": [
          {
            "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
            "type": "tariff"
          }
        ]
      }
    }
  }
}
```

### Example 3: Request prices of the given tariff at the given station and only for the specified power and plug

```http
POST http://example-base-url.com/v1/tariff_details
Content-Type: application/json
Api-Key: my-secret-key
```

Body:

```json
{
  "data": {
    "attributes": {
      "station": {
        "id": ["71006f18-3ed4-4715-92b5-08e37e6dd180"],
        "charge_point": {
          "power": 22.0,
          "plug": "type2"
        }
      }
    },
    "relationships": {
      "tariffs": {
        "data": [
          {
            "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
            "type": "tariff"
          }
        ]
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
      "id": "40006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "station_tariff_details",
      "attributes": {
        "updated_at": 1546300800000,
        "tariff_level": "country",
        "is_roaming": true,
        "restricted_segments": [
          {
            "dimension": "kwh",
            "price": 2.0,
            "range_gte": null,
            "range_lt": null,
            "billing_increment": 0.01,
            "currency": "EUR",
            "time_of_day_start": null,
            "time_of_day_end": null,
            "charge_point_powers": [
              0.0,
              22.0
            ],
            "charge_point_energy_type": "ac",
            "car_ac_phase": 2,
            "charge_point_power_is_range": true,
            "use_consumed_charging_power": false,
            "is_average_price": false,
            "occupancy_gte": 20,
            "occupancy_lt": 80
          }
        ],
        "no_price_reason": "prices_per_station",
        "prices_per_station_available": false
      },
      "relationships": {
        "emp": {
          "data": {
            "type": "company",
            "id": "10006f18-3ed4-4715-92b5-08e37e6dd18c"
          }
        },
        "tariff": {
          "data": {
            "type": "tariff",
            "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c"
          }
        },
        "cpo": {
          "data": {
            "type": "company",
            "id": "50006f18-3ed4-4715-92b5-08e37e6dd18c"
          }
        },
        "station": {
          "data": {
            "type": "charging_station",
            "id": "95006f18-3ed4-4715-92b5-08e37e6dd1aa"
          }
        }
      }
    }
  ],
  "included": [
    {
      "id": "10006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "company",
      "attributes": {
        "name": "Energie Steiermark",
      }
    },
    {
      "id": "20006f18-3ed4-4715-92b5-08e37e6dd18c",
      "type": "tariff",
      "attributes": {
        "name": "easyFlex",
        "total_monthly_fee": 5.0,
        "is_direct_payment": false,
        "is_card_payment": false,
        "provider_customer_only": false,
        "existing_customer_only": false,
        "allowed_customer_countries": ["AT"],
        "currency": "EUR",
        "url": "http://www.google.at"
      },
      "relationships": {
        "vehicle_brands": {
          "data": [
            {
              "id": "50006f11-3ed4-4715-92b5-08e37e6dd185",
              "type": "brand"
            }
          ]
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
