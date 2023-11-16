# Charging Stations CSV Import

CPOs can provide Chargeprice a CSV file in the following format to integrate POIs: 

## Attributes

| **Name**       | **Required** | **Data Type** | **Example**              | **Description**                                                                                        |
|----------------|--------------|---------------|--------------------------|--------------------------------------------------------------------------------------------------------|
| ID             | TRUE         | String        | 1234                     | ID of the location in the database of the CPO. Should be the same for all connectors of this location. |
| Name           | FALSE        | String        | Parking Salzburg Highway | Name of the location, if available                                                                     |
| Street address | FALSE        | String        | Example street 1         | Street address of the location, if available                                                           |
| City           | TRUE         | String        | Salzburg                 | City of the location                                                                                   |
| Postal code    | TRUE         | String        | 5020                     | Postal code of the location                                                                            |
| Country Code   | TRUE         | String(2)     | AT                       | ISO 3166 country code of the location                                                                  |
| Longitude      | TRUE         | Float         | 12.345                   | Latitude component of the location                                                                     |
| Latitude       | TRUE         | Float         | 43.567                   | Longitude component of the location                                                                    |
| EVSE ID        | FALSE        | String        | AT\*ION\*E0001           | EVSE ID of the connector, if available.                                                                |
| Power          | TRUE         | Float         | 150                      | Max. power of the connector                                                                            |
| Plug           | TRUE         | String        | ccs                      | Type of plug. Possible values: ccs, chademo, type2, type1, type3, schuko, tesla_ccs, tesla_suc         |

## Examples

| **ID** | **Name**         | **Street address** | **City**        | **Postal code** | **Country Code** | **Longitude** | **Latitude** | **EVSE ID**      | **Power** | **Plug** |
|--------|------------------|--------------------|-----------------|-----------------|------------------|---------------|--------------|------------------|-----------|----------|
| 1234   | Gemeine Lebring  | Dorfstraße 1       | Lebring         | 8403            | AT               | 15.436692     | 47.05594     | AT\*ION\*E1234*1 | 22        | type2    |
| 1234   | Gemeine Lebring  | Dorfstraße 1       | Lebring         | 8403            | AT               | 15.436692     | 47.05594     | AT\*ION\*E1234*2 | 22        | type2    |
| 1235   | Gasthaus Neuwirt | Zauchen 100        | Bad Mitterndorf | 8983            | AT               | 15.402449     | 47.070981    | AT\*ION\*E4443*1 | 50        | ccs      |
