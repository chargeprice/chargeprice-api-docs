
# Tariff History Export

This document describes the data format of the tariff history export in the CSV
format.

The export contains historical prices of specifc EMP (E-Mobility Providers)
tariffs at each CPO (Charge Point Operator) they are supporting.

It is not available via the API or Data Platform and can be provided on demand.

## Data Structure

Given an example tariff "EnBW Ladetarif M" (5.99€/Month) with the following prices:
- Fastned (CPO) in Germany:
  - AC: 0.49€/kWh + 0.1€/minute after 180 minutes
  - DC: 0.59€/kWh + 0.1€/minute after 90 minutes 

Every price displayed above becomes a "tariff component" and every tariff component is represent by a single row in the export.
Hence the above pricing would result in 4 rows:

| Valid From | Valid To  | Country | CPO     | EVSE Operator IDs | EMP  | Tariff      | Total Monthly Fee | Currency of Monthly Fee | Updated At | Energy Type | Power Start (gte) | Power End (lte) | Dimension | Unit Price | Range Start (gte) | Range End (lt) | Billing Increment | Currency of Price | Time of Day Start | Time of Day End |
|------------|-----------|---------|---------|-------------------|------|-------------|-------------------|-------------------------|------------|-------------|-------------------|-----------------|-----------|------------|-------------------|----------------|-------------------|-------------------|-------------------|-----------------|
| some date  | some date | DE      | Fastned | AT\*FAS           | EnBW | Ladetarif M | 5.99              | EUR                     | some date  | AC          |                   |                 | kwh       | 0.49       |                   |                | 0.01              | EUR               |                   |                 |
| some date  | some date | DE      | Fastned | AT\*FAS           | EnBW | Ladetarif M | 5.99              | EUR                     | some date  | AC          |                   |                 | minute    | 0.1        | 180               |                | 0.01              | EUR               |                   |                 |
| some date  | some date | DE      | Fastned | AT\*FAS           | EnBW | Ladetarif M | 5.99              | EUR                     | some date  | DC          |                   |                 | kwh       | 0.59       |                   |                | 0.01              | EUR               |                   |                 |
| some date  | some date | DE      | Fastned | AT\*FAS           | EnBW | Ladetarif M | 5.99              | EUR                     | some date  | DC          |                   |                 | minute    | 0.1        | 90                |                | 0.01              | EUR               |                   |                 |

Hence if you want to find out all the tariff components of "EnBW Ladetarif M" at Fastned in Germany at AC you need to filter the list for:
- Country: DE
- CPO: Fastned
- EMP: EnBW
- Tariff: Ladetarif M
- Energy Type: AC or Empty (Empty includs both AC and DC, see below for details)

## Historical Data

All tariff components are defined with a date range when they have been valid.

Whenever any single tariff component of a whole tariff is changing, there will
be new entry for the whole tariff!

Example: The EnBW tariff from above is changing from
- AC: 0.49€/kWh + **0.1€/minute** after 180 minutes
- DC: 0.59€/kWh + 0.1€/minute after 90 minutes 

to

- AC: 0.49€/kWh + **0.2€/minute** after 180 minutes
- DC: 0.59€/kWh + 0.1€/minute after 90 minutes 

Even though only the price of the time component of AC has changed, there will
be four new rows in the export!


## Attributes

| Name                    | Type                            | Example              | Description                                                                                                                                                                                                           |
|-------------------------|---------------------------------|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Valid From              | ISO 8601 Date                   | 2021-05-21T07:57:38Z | Timestamp from which the tariff is valid.                                                                                                                                                                             |
| Valid To                | ISO 8601 Date or Empty          | 2021-05-21T07:57:38Z | Timestamp till the tariff is no longer valid. If empty, it's still valid until further notice.                                                                                                                        |
| Country                 | ISO 3166-1 alpha-2 Country Code | AT                   | Country for which the tariff is defined                                                                                                                                                                               |
| CPO Name                | String                          | IONITY               | Name of the Charge Point Operator for which this tariff is defined.                                                                                                                                                   |
| CPO ID                  | UUID                            | IONITY               | Chargeprice Internal ID                                                                                                                                                                                               |
| EVSE Operator IDs       | String                          | AT\*ION,AT\*ABC      | List of EVSE Operator IDs that belong to this CPO in current country separated by comma.                                                                                                                              |
| EMP Name                | String                          | EnBW                 | Name of the E-Mobility Provider which is offering the tariff.                                                                                                                                                         |
| EMP ID                  | UUID                            | EnBW                 | Chargeprice Internal ID                                                                                                                                                                                               |
| Tariff Name             | String                          | Ladetarif M          | Name of the Tariff                                                                                                                                                                                                    |
| Tariff ID               | UUID                            | Ladetarif M          | Chargeprice Internal ID                                                                                                                                                                                               |
| Total Monthly Fee       | Float                           | 5.99                 | Monthly fee incl. 1/12 of any yearly fees                                                                                                                                                                             |
| Currency of Monthly Fee | ISO 4217 Currency Code          | EUR                  | Currency used for the total monthly fee                                                                                                                                                                               |
| Tariff Level            | String                          | `cpo`                | At which level this tariff applies: `country`: the default tariff for all CPOs in this country, `cpo`: tariff applies to all EVSEs of this CPO, `evse` (not implemented yet): tariff applies only to a specific EVSE. |
| Updated At              | ISO 8601 Date                   | 2021-05-21T07:57:38Z | Timestamp when the tariff has last been updated in the Chargeprice system                                                                                                                                             |
| Energy Type             | "AC", "DC" or Empty             | AC                   | Energy Type for which this tariff component is applied. If not defined, it is valid for both AC and DC.                                                                                                               |
| Power Start (gte)       | Float or Empty                  | 22                   | Minimum power level for which this tariff component is applied. If not defined it is 0.                                                                                                                               |
| Power End (lte)         | Float or Empty                  | 50                   | Maximum power level for which this tariff component is applied. If not defined there is no upper limit.                                                                                                               |
| Dimension               | "kwh", "minute" or "session"    | kwh                  | Is this price billed per kWh, minute or is it a session fee?                                                                                                                                                          |
| Unit Price              | Float                           | 0.59                 | The actual price of this tariff component.                                                                                                                                                                            |
| Range Start (gte)       | Float or Empty                  | 180                  | Only applicable if dimension=minute. Defines from which charging duration this tariff component is charged. If empty, it is charged right from the start.                                                             |
| Range End (lt)          | Float or Empty                  | 360                  | Only applicable if dimension=minute. Defines until which charging duration this tariff component is charged. If empty, it is charged right from the start.                                                            |
| Billing Increment       | Float or Empty                  | 0.01                 | e.g. if dimension=minute and billing increment is 10, the customer is charged in blocks of any started 10 minutes. Can also apply to dimension=kwh                                                                    |
| Currency of Price       | ISO 4217 Currency Code          | EUR                  | Currency of the Unit Price                                                                                                                                                                                            |
| Time of Day Start       | Integer or Empty                | 720                  | Time of day when this segment starts to count or gets active. In minutes. If not set, the price is valid the whole day.                                                                                               |
| Time of Day End         | Integer or Empty                | 1200                 | Time of day when this segment stops to count or be active. In minutes. If not set, the price is valid the whole day.                                                                                                  |

# Maintaining the history after receiving this export

If you want to keep maintaining the history of the prices in the future as well,
we recommend to use the above export as a backfill and the [TariffDetails API](../api/v1/tariff_details/index.md) to periodically sync the current
prices to build up the history yourself. In order to include new CPOs that may
have not been part of the export yet as well, you can fetch all CPOs of a
country using the [Charging Station Statistics API](../api/v1/charging_stations/statistics/index.md).

The table below shows the mapping of the Columns of the Historical Export to the
attributes in APIs:

| Column in Export        | Attribute in Tariff Details API              | Attribute in Charging Station Statistics API        |
|-------------------------|----------------------------------------------|-----------------------------------------------------|
| Valid From              | n/a                                          | n/a                                                 |
| Valid To                | n/a                                          | n/a                                                 |
| Country                 | country                                      |                                                     |
| CPO Name                |                                              | operator.name                                       |
| CPO ID                  | cpo.id                                       |                                                     |
| EVSE Operator IDs       |                                              | operator.external_source_mapping.evse_operator_ids* |
| EMP Name                | emp.name                                     |                                                     |
| EMP ID                  | emp.id                                       |                                                     |
| Tariff Name             | tariff.name                                  |                                                     |
| Tariff ID               | tariff.id                                    |                                                     |
| Total Monthly Fee       | tariff.total_monthly_fee                     |                                                     |
| Currency of Monthly Fee | tariff.currency                              |                                                     |
| Tariff Level            | tariff_level                                 |                                                     |
| Updated At              | updated_at                                   |                                                     |
| Energy Type             | restricted_segments.charge_point_energy_type |                                                     |
| Power Start (gte)       | restricted_segments.charge_point_powers[0]   |                                                     |
| Power End (lte)         | restricted_segments.charge_point_powers[1]   |                                                     |
| Dimension               | restricted_segments.dimension                |                                                     |
| Unit Price              | restricted_segments.price                    |                                                     |
| Range Start (gte)       | restricted_segments.range_gte                |                                                     |
| Range End (lt)          | restricted_segments.range_lt                 |                                                     |
| Billing Increment       | restricted_segments.billing_increment        |                                                     |
| Currency of Price       | restricted_segments.currency                 |                                                     |
| Time of Day Start       | restricted_segments.time_of_day_start        |                                                     |
| Time of Day End         | restricted_segments.time_of_day_end          |                                                     |

\* The EVSE Operator IDs in the Historical Export are already filtered by
country (using the first to characters of the party ID), while the ones in the
Charging Station Statistics API are not filtered yet!