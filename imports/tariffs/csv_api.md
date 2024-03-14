# Import Tariffs: CSV API

This documents specifies the API which an EMSP needs to provide in order to have
the EMPS's tariffs integrated into Chargeprice.

## Verbs

- **MUST** are requirements. If these requirements are not fulfilled, the tariffs won't be imported.
- **SHOULD** are recommendations. If not fulfilled, the tariffs will still be imported.
- **MIGHT** are options that are up to the EMSP.

## General

The EMSP needs to provide an API with the following structure:

- Transport Protocol
  - The API **MUST** be hosted by the EMSP and Chargeprice will regularly consume it to
    update the prices.
  - API **MUST** be available via HTTP GET.
  - The URL and path are up to the EMSP and need to be provided to Chargeprice.
  - Response **MUST** be in CSV format: `Content-Type: text/csv`.
  - Seperator for the CSV **MUST** be a `;` (semicolon).
  - The CSV **MIGHT** be a 
  - First line **MUST** contain headers.
  - The headers **SHOULD** be the names of the response attributes.
  - API **MUST** return all prices of all EVSEs in a single request. There is no
    pagination.
 - If the EMSP is not able to provide the tariff data via an API, the data
   **MIGHT** alternatively also be uploaded as a CSV file to the
   Chargeprice System. Note that this needs to happen with every price change
   manually to ensure up to date data on Chargeprice!  
- Tariff Structure
  - Prices **MUST** be provided for every single EVSE. It is not possible to
    group prices for multiple EVSEs.
  - One row contains a single price component of an EVSE. If the tariff of an
    EVSE contains multiple price components (e.g. kWh and session fee), every
    price component needs to be a separate row. See examples below.
  - The tariff structure is kept as simple as possible, but still trying to follow the [OCPI tariffs module](https://evroaming.org/app/uploads/2020/06/OCPI-2.2-d2.pdf) as good as possible.
- Update frequency: Chargeprice will per default call the API once per week. The
  frequency is configurable.

## Authentication

The API **MIGHT** be protected by HTTP request headers that the EMSP can
provide to Chargeprice. There is no restriction on which headers need to be
used. One possibility is basic authentication:

```http
Authorization: Basic <credentials>
```

## Response Attributes

The response **MUST** have the following attributes in the defined order.

| **Name**     | **Required** | **Data Type** | **Example**        | **Description**                                                                                                                                                                                    |
|--------------|--------------|---------------|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| evse_id      | TRUE         | String        | AT\*ION\*E0001*123 | eMI3 Roaming ID of the connector                                                                                                                                                                   |
| country_code | TRUE         | String(2)     | AT                 | Country in which the EVSE is deployed. ISO 3166-1 alpha-2                                                                                                                                          |
| currency     | TRUE         | String(3)     | EUR                | Currency in which the price is defined. ISO 4217                                                                                                                                                   |
| dimension    | TRUE         | String        | ENERGY             | Dimension of the Price. Possible values:<br>SESSION = price per charging session<br>ENERGY = price per charged kWh<br>TIME = price per hour charging<br>PARKING_TIME = price per hour not charging |
| price        | TRUE         | Float         | 0.5                | B2C price incl. VAT                                                                                                                                                                                |
| min_duration | FALSE        | Integer       | 3600               | Only valid if Dimension is TIME.<br>Defines when this unit price is applied from (in seconds) Default: 0                                                                                           |
| max_duration | FALSE        | Integer       | 7200               | Only valid if Dimension is TIME.<br>Defines until when this unit price is applied to (in seconds) Default: unlimited                                                                               |
| start_time   | FALSE        | String        | 08:00:00           | Time of day when this unit price is applied from (HH:MM:SS) Default: 00:00:00                                                                                                                      |
| end_time     | FALSE        | String        | 20:00:00           | Time of day until when this unit price is applied to (HH:MM:SS) Default: 00:00:00                                                                                                                  |
| step_size    | FALSE        | Float         | 900                | If Dimension is TIME and step size is 60, the customer is charged in blocks of any started 60 seconds.                                                                                             |
| start_date   | FALSE        | String        | 2024-01-01         | Date when this unit price is applied from (YYYY-MM-DD) Default: Not applied.                                                                                                                       |
| end_date     | FALSE        | String        | 2024-31-12         | Date until when this unit price is applied to (YYYY-MM-DD) Default: Not applied.                                                                                                                   |
| days_of_week | FALSE        | String        | MONDAY,TUESDAY     | Which day(s) of the week this element is active. Default: All days. Comma separated list. Possible values: MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY                          |

### Additional Rules

- If a Start Time is defined, also an End Time **MUST** be defined.
- If an attribute, that is not required, is not defined, the value **MUST** be an empty string.

## Examples

### Example 1: kWh price with blocking fee and session fee

* EVSE: AT\*ION\*E0001
* Country: Austria
* Tariff:
  * 0.35€/Session
  * 0.5€/kWh 
  * 0.1€/min after 1h until 3h

| evse_id        | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week |
|----------------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|--------------|
| AT\*ION\*E0001 | AT           | EUR      | SESSION   | 0.35  |              |              |            |          |           |            |          |              |
| AT\*ION\*E0001 | AT           | EUR      | ENERGY    | 0.5   |              |              |            |          |           |            |          |              |
| AT\*ION\*E0001 | AT           | EUR      | TIME      | 6     | 3600         | 10800        |            |          |           |            |          |              |

### Example 2: Day and night price

* EVSE: FR\*FR1\*E001
* Country: France
* Tariff:
  * 0.2€/min 06:00-22:00
  * 0.1€/min 22:00-06:00

| evse_id       | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week |
|---------------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|--------------|
| FR\*FR1\*E001 | FR           | EUR      | TIME      | 12    |              |              | 06:00:00   | 22:00:00 |           |            |          |              |
| FR\*FR1\*E001 | FR           | EUR      | TIME      | 6     |              |              | 22:00:00   | 06:00:00 |           |            |          |              |

### Example 3: Time blocks

* EVSE: FR\*FR1\*E002
* Country: France
* Tariff:
  * 1.5€ for the first 15 min (-> 1.5€/15min = 0.1€/min)
  * 0.3€/min after the first 15 min

| evse_id       | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week |
|---------------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|--------------|
| FR\*FR1\*E002 | FR           | EUR      | TIME      | 6     |              | 900          |            |          | 900       |            |          |              |
| FR\*FR1\*E002 | FR           | EUR      | TIME      | 18    | 900          |              |            |          |           |            |          |              |

### Example 4: Different price during the weekend

* EVSE: AT\*ION\*E0001
* Country: Austria
* Tariff:
  * 0.5€ during weekdays
  * 0.6€ during the weekend

| evse_id        | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week                             |
|----------------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|------------------------------------------|
| AT\*ION\*E0001 | AT           | EUR      | ENERGY    | 0.5   |              |              |            |          |           |            |          | MONDAY,TUESDAY,WEDNESDAY,THURSDAY,FRIDAY |
| AT\*ION\*E0001 | AT           | EUR      | ENERGY    | 0.6   |              |              |            |          |           |            |          | SATURDAY,SUNDAY                          |

### Example 5: Price change on a dedicated date

* EVSE: AT\*ION\*E0001
* Country: Austria
* Tariff:
  * 0.5€ until 31.12.2024
  * 0.6€ from 01.01.2025

| evse_id        | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size  | start_date | end_date | days_of_week |
|----------------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|------------|------------|----------|--------------|
| AT\*ION\*E0001 | AT           | EUR      | ENERGY    | 0.5   |              |              |            |          |            | 31.12.2024 |          |              |
| AT\*ION\*E0001 | AT           | EUR      | ENERGY    | 0.6   |              |              |            |          | 01.01.2025 |            |          |              |


### Checklist of what the EMSP need to provide to Chargeprice

* URL of the API
* Headers for Authentiation