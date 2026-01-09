# Import Tariffs: CSV API per EVSE Party ID

This documents specifies the API which an EMSP needs to provide in order to have
the EMPS's tariffs integrated into Chargeprice.

If your prices vary only by charge point operator (=EVSE Party ID), this is the right format to exchange data.
If your prices vary by location, we provide a different format that you can check [here](./csv_api.md).

## Verbs

Please check this section in [this document](csv_api.md). It also applies here.

## General

Please check this section in [this document](csv_api.md). It also applies here.

## Authentication

Please check this section in [this document](csv_api.md). It also applies here.

## Response Attributes

The response **MUST** have the following attributes in the defined order.

| **Name**      | **Required** | **Data Type** | **Example**    | **Description**                                                                                                                                                                                                                                             |
|---------------|--------------|---------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| evse_party_id | TRUE         | String(6)     | AT\*ION        | eMI3 party ID. It needs to contain the country code, an asterisk (*) and 3-character party code. The asterisk always needs to be included, even if the EVSE IDs of the connector might not include it.                                                      |
| energy_type   | TRUE         | String(2)     | AC             | Energy type that this price applies to. Possible values: AC, DC.                                                                                        |
| power_start   | FALSE        | Float         | 11             | If the price only applies to a specific power range, you can specify it here. If the price applies to all power levels, leave the field empty. This field represents the inclusive lower end of the range.                                                  |
| power_end     | FALSE        | Float         | 11             | If the price only applies to a specific power range, you can specify it here. If the price applies to all power levels, leave the field empty. This field represents the inclusive upper border of the range.                                               |
| country_code  | TRUE         | String(2)     | AT             | The price is valid for charging stations in this country. ISO 3166-1 alpha-2                                                                                                                                                                                |
| currency      | TRUE         | String(3)     | EUR            | Currency in which the price is defined. ISO 4217                                                                                                                                                                                                            |
| dimension     | TRUE         | String        | ENERGY         | Dimension of the Price. Possible values:<br>FLAT (preferred) or SESSION (alternativly) = price per charging session<br>ENERGY = price per charged kWh<br>TIME = price per hour charging<br>PARKING_TIME = price per hour not charging                       |
| price         | TRUE         | Float         | 0.5            | B2C price incl. VAT                                                                                                                                                                                                                                         |
| min_duration  | FALSE        | Integer       | 3600           | Only valid if Dimension is TIME or PARKING_TIME.<br>Defines when this unit price is applied from (in seconds) Default: 0                                                                                                                                    |
| max_duration  | FALSE        | Integer       | 7200           | Only valid if Dimension is TIME or PARKING_TIME.<br>Defines until when this unit price is applied to (in seconds) Default: unlimited                                                                                                                        |
| start_time    | FALSE        | String        | 08:00:00       | Time of day when this unit price is applied from (HH:MM:SS) Default: not set (Price is valid any time of the day)                                                                                                                                           |
| end_time      | FALSE        | String        | 20:00:00       | Time of day until when this unit price is applied to (HH:MM:SS) Default: not set (Price is valid any time of the day)                                                                                                                                       |
| step_size     | FALSE        | Float         | 900            | If Dimension is TIME or PARKING_TIME and step size is 60, the customer is charged in blocks of any started 60 seconds. For Dimension ENERGY the unit is Wh. For FLAT it **SHOULD** never be set. Default: 60 seconds (TIME and PARKING_TIME), 1 Wh (ENERGY) |
| start_date    | FALSE        | String        | 2024-01-01     | Date when this unit price is applied from (YYYY-MM-DD) Default: Not applied.                                                                                                                                                                                |
| end_date      | FALSE        | String        | 2024-31-12     | Date until when this unit price is applied to (YYYY-MM-DD) Default: Not applied.                                                                                                                                                                            |
| days_of_week  | FALSE        | String        | MONDAY,TUESDAY | Which day(s) of the week this element is active. Default: All days. Comma separated list. Possible values: MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY                                                                                   |

### Additional Rules

- If a Start Time is defined, also an End Time **MUST** be defined.
- If an attribute, that is not required, is not defined, the value **MUST** be an empty string.
- If a price applies to both AC and DC, you need to specifiy separate entries for AC and DC. 
- If power start or end are defined for an evse_party_id + energy_type combination, it **MUST** be defined for all entries of this evse_party_id + energy_type combination. If a price applies to all power levels, it needs to be defined for each power level.

## Examples

### Example 1: kWh price with blocking fee and session fee

* EVSE Party ID: AT\*ION
* Energy Type: DC
* Power Start & End: All
* Country: Austria
* Tariff:
  * 0.35€/session
  * 0.5€/kWh 
  * 0.1€/min after 1h until 3h

| evse_party_id | energy_type | power_start | power_end | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week |
|---------------|-------------|-------------|-----------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|--------------|
| AT\*ION       | DC          |             |           | AT           | EUR      | FLAT      | 0.35  |              |              |            |          |           |            |          |              |
| AT\*ION       | DC          |             |           | AT           | EUR      | ENERGY    | 0.5   |              |              |            |          |           |            |          |              |
| AT\*ION       | DC          |             |           | AT           | EUR      | TIME      | 6     | 3600         | 10800        |            |          |           |            |          |              |

### Example 2: Day and night price

* EVSE Party ID: FR\*FR1
* Energy Type: AC
* Power Start & End: 11.1 - 22 kW
* Country: France
* Tariff:
  * 0.2€/min 06:00-22:00
  * 0.1€/min 22:00-06:00

| evse_party_id | energy_type | power_start | power_end | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week | start_charging_link |
|---------------|-------------|-------------|-----------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|--------------|---------------------|
| FR\*FR1       | DC          | 11.1        | 22        | FR           | EUR      | TIME      | 12    |              |              | 06:00:00   | 22:00:00 |           |            |          |              |                     |
| FR\*FR1       | DC          | 11.1        | 22        | FR           | EUR      | TIME      | 6     |              |              | 22:00:00   | 06:00:00 |           |            |          |              |                     |

### Example 3: Time blocks

* EVSE Party ID: FR\*ION
* Energy Type: DC
* Power Start & End: All
* Country: France
* Tariff:
  * 1.5€ for the first 15 min (-> 1.5€/15min = 0.1€/min)
  * 0.3€/min after the first 15 min

| evse_party_id | energy_type | power_start | power_end | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week | start_charging_link |
|---------------|-------------|-------------|-----------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|--------------|---------------------|
| FR\*ION       | DC          |             |           | FR           | EUR      | TIME      | 6     |              | 900          |            |          | 900       |            |          |              |                     |
| FR\*ION       | DC          |             |           | FR           | EUR      | TIME      | 18    | 900          |              |            |          |           |            |          |              |                     |

### Example 4: Different price during the weekend

* EVSE Party ID: AT\*ION
* Energy Type: DC
* Power Start & End: All
* Country: Austria
* Tariff:
  * 0.5€ during weekdays
  * 0.6€ during the weekend

| evse_party_id | energy_type | power_start | power_end | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size | start_date | end_date | days_of_week                             | start_charging_link |
|---------------|-------------|-------------|-----------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|-----------|------------|----------|------------------------------------------|---------------------|
| AT\*ION       | DC          |             |           | AT           | EUR      | ENERGY    | 0.5   |              |              |            |          |           |            |          | MONDAY,TUESDAY,WEDNESDAY,THURSDAY,FRIDAY |                     |
| AT\*ION       | DC          |             |           | AT           | EUR      | ENERGY    | 0.6   |              |              |            |          |           |            |          | SATURDAY,SUNDAY                          |                     |

### Example 5: Price change on a dedicated date

* EVSE Party ID: AT\*ION
* Energy Type: DC
* Power Start & End: All
* Country: Austria
* Tariff:
  * 0.5€ until 31.12.2024
  * 0.6€ from 01.01.2025

| evse_party_id | energy_type | power_start | power_end | country_code | currency | dimension | price | min_duration | max_duration | start_time | end_time | step_size  | start_date | end_date | days_of_week | start_charging_link |
|---------------|-------------|-------------|-----------|--------------|----------|-----------|-------|--------------|--------------|------------|----------|------------|------------|----------|--------------|---------------------|
| AT\*ION       | DC          |             |           | AT           | EUR      | ENERGY    | 0.5   |              |              |            |          |            | 31.12.2024 |          |              |                     |
| AT\*ION       | DC          |             |           | AT           | EUR      | ENERGY    | 0.6   |              |              |            |          | 01.01.2025 |            |          |              |                     |


### Production Example

A final data delivery can simply look like [this Google Sheet](https://docs.google.com/spreadsheets/d/1qNZRIHW0q_DIU3R1YqaqPa6U_VM5OmR_mYxnwvlQBv0/edit).
Google Sheets are CSV compatible and can be written in an automated way by the provider.

### Checklist of what the EMSP need to provide to Chargeprice

Please check this section in [this document](csv_api.md). It also applies here.