# Open data charging prices Switzerland

Current prices at public charging stations in Switzerland.

Good to know: This open data API currently only contains data that has been directly provided by Charge Point Operators (CPOs) or E-Mobility Service Providers (EMPs). The [full Chargeprice API](https://github.com/chargeprice/chargeprice-api-docs) contains a higher coverage of data in Switzerland and Europe.  

The data in this API only contains information about charging prices. You can connect these prices with open data around charging stations using https://www.ich-tanke-strom.ch/. 

This API follows the <https://jsonapi.org> specification.

## Access URL

https://api.chargeprice.app/v1/opendata/charging_prices_ch

## Rate Limit

This API is rate limited to 2 requests per 24 hours per IP address.
Syncing the prices once per day is sufficient to keep your data up to date.

## Headers

* `Content-Type: application/json`

## Response Body

The following table lists the `attributes` of these objects:

| **Name**                            | **Type** | **Example**        | **Description**                                                                                                                                                                                                                                             |
|-------------------------------------|----------|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| country_code                        | String   | `CH`               | ISO 3166 code of the country for which the prices are defined.                                                                                                                                                                                              |
| evse_id                             | String   | `CH*ION*E0001*123` | eMI3 Roaming ID of the connector                                                                                                                                                                                                                            |
| currency                            | String   | `CHF`              | Currency in which the price is defined. ISO 4217                                                                                                                                                                                                            |
| elements                            | Object   | -                  | List of Tariff Elements. Compliant to [OCPI TariffElement](https://github.com/ocpi/ocpi/blob/master/mod_tariffs.asciidoc#144-tariffelement-class)                                                                                                           |
| elements.price_components           | Array    | -                  | List of Price Components that each describe how a certain dimension is priced.                                                                                                                                                                              |
| elements.price_components.type      | String   | `ENERGY`           | Dimension of the Price. Possible values:<br>FLAT = price per charging session<br>ENERGY = price per charged kWh<br>TIME = price per hour charging<br>PARKING_TIME = price per hour not charging                       |
| elements.price_components.price     | Float    | 0.5                | B2C price incl. VAT                                                                                                                                                                                                                                         |
| elements.price_components.step_size | Integer  | 1                  | If Dimension is TIME or PARKING_TIME and step size is 60, the customer is charged in blocks of any started 60 seconds. For Dimension ENERGY the unit is Wh. For FLAT it **SHOULD** never be set. Default: 60 seconds (TIME and PARKING_TIME), 1 Wh (ENERGY) |
| elements.restrictions               | Object   | -                  | Restrictions that describe under which circumstances the Price Components of this Tariff Element apply.                                                                                                                                                     |
| elements.restrictions.start_time    | String   | `08:00:00`         | Time of day when this unit price is applied from (HH:MM:SS). If `null`: Price is valid any time of the day                                                                                                                                                  |
| elements.restrictions.end_time      | String   | `20:00:00`         | Time of day until when this unit price is applied to (HH:MM:SS). If `null`: Price is valid any time of the day                                                                                                                                              |
| elements.restrictions.min_duration  | Integer  | 3600               | Only valid if Dimension is TIME or PARKING_TIME.<br>Defines when this unit price is applied from (in seconds) Default: 0                                                                                                                                    |
| elements.restrictions.max_duration  | Integer  | 7200               | Only valid if Dimension is TIME or PARKING_TIME.<br>Defines until when this unit price is applied to (in seconds) Default: unlimited                                                                                                                        |

The following table lists the `relationships` and their values in the `included` section:

| **Name**    | **Type**     | **Example**                            | **Description**                                                                                                                          |
|-------------|--------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| tariff      | Relationship | `{"id": "some-uuid", type:"tariff" }`  | The tariff for which the details are given.                                                                                              |
| tariff.name | String       | `easyFlex`                             | Name of the tariff                                                                                                                       |
| emp         | Relationship | `{"id": "some-uuid", type:"company" }` | The EMP (E-Mobility Service Provider) who offers the tariff. In many cases this is also the CPO (Charge Point Operator) of this station. |
| emp.name    | String       | `Energie Steiermark`                   | Company name of the EMP                                                                                                                  |

### Response

#### 200 Ok

The example response below describes the price for the charging station:
- EVSE ID `CH*ION*E1234`
- Price: 0.7 CHF / kWh + 0.1 CHF / minute between 1h and 10h of charging
- E-Mobility Service Provider: IONITY 
- Tariff: Direct 

Body:

```json
{
  "data": [
    {
      "id": "75e1a0d6-5707-489d-a730-c89999b0be9b",
      "type": "evse_tariff",
      "attributes": {
        "evse_id": "CH*ION*E1234",
        "currency": "CHF",
        "country_code": "CH",
        "elements": [
          {
            "price_components": [
              {
                "type": "ENERGY",
                "price": 0.7,
                "step_size": 1
              }
            ],
            "restrictions": {
              "start_time": null,
              "end_time": null,
              "min_duration": null,
              "max_duration": null
            }
          },
          {
            "price_components": [
              {
                "type": "TIME",
                "price": 0.1,
                "step_size": 60
              }
            ],
            "restrictions": {
              "start_time": "08:00:00",
              "end_time": "20:00:00",
              "min_duration": 3600,
              "max_duration": 36000
            }
          }
        ]
      },
      "relationships": {
        "tariff": {
          "data": {
            "id": "29440f56-9763-4fde-a025-451b80c336f1",
            "type": "tariff"
          }
        },
        "emp": {
          "data": {
            "id": "456",
            "type": "company"
          }
        }
      }
    }
  ],
  "includes": [
    {
      "id": "29440f56-9763-4fde-a025-451b80c336f1",
      "type": "tariff",
      "attributes": {
        "name": "Direct"
      }
    },
    {
      "id": "7f255408-92eb-4996-9787-7d15c33c183f",
      "type": "company",
      "attributes": {
        "name": "IONITY"
      }
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
