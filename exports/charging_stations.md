
# Charging Stations (POI) Export

This document describes the data format of the charging stations export in the CSV
format.

The export contains a list of connectors of charging stations.

This data can also be fetched via the [/v1/charging_stations API](../api/v1/charging_stations/index.md).

## Attributes

| Name             | Type    | Example                              | Description                                                                                                                               |
|------------------|---------|--------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| ID               | String  | e2817a8f-8332-4638-8f0b-8ac3a3549386 | Chargeprice Internal ID of the Charging Station. Can be used to group all connectors of a station.                                        |
| CPO ID           | String  | 96bfecee-bf97-4ffb-af39-b66e1593e7ab | Chargeprice Internal ID of the CPO. Can be used group all stations of a specific CPO, because the EVSE Operator ID is not unique per CPO. |
| CPO              | String  | IONITY                               | Name of the charging station operator.                                                                                                    |
| EVSE Operator ID | String  | AT\*ION                              | EMI3 Party ID (Country and Party ID part of the full EVSE ID)                                                                             |
| EVSE ID          | String  | AT\*ION\*E1234                       | EMI3 EVSE ID of the connector                                                                                                             |
| Address          | String  | Teslastraße 1, 8010 Graz, AT         | Full address of the station                                                                                                               |
| Latitude         | Float   | 43.345                               | Latitude component of the location                                                                                                        |
| Longitude        | Float   | 12.443                               | Longitude component of the location                                                                                                       |
| Country          | String  | AT                                   | ISO 3166 country code of the location                                                                                                     |
| Power            | Float   | 350.0                                | Max. power (kW)                                                                                                                           |
| Plug             | String  | ccs                                  | Plug of the connector. [See supported values](../../enums.md#plugs)                                                                                                                   |
| Energy Type      | String  | DC                                   | Energy Type of the connector: AC or DC                                                                                                    |
| Created At       | String  | 2024-07-22T17:53:23.000Z             | Time when the station has been added to the Chargeprice Database. In ISO 8601 format.                                                     |
| Updated At       | String  | 2024-07-22T17:53:23.000Z             | Time when the station has been updated the last time in the Chargeprice Database. In ISO 8601 format.                                     |
| Count            | Integer | 1                                    | For Stations with an EVSE this is usually 1. For stations without EVSE IDs, this indicates how many connectors the station has.           |