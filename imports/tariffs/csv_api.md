# Import Tariffs: CSV API

This documents specifies the API which an EMSP needs to provide in order to have
the EMPS's tariffs integrated into Chargeprice.

The EMSP needs to provide an API with the following structure:

- Transport Protocol
  - The API is hosted by the EMSP and Chargeprice will regularly consume it to
    update the prices.
  - API is an HTTP GET request.
  - The URL and path are up to the EMSP and need to be provided to Chargeprice.
  - Response is in CSV format: `Content-Type: text/csv`.
  - Seperator for the CSV is a `;` (semicolon).
  - First line of the response contains the headers using the name of the
    response attributes.
  - API returns all prices of all EVSEs in a single request. There is no
    pagination.
- Tariff Structure
  - Prices need to be provided for every single EVSE. It is not possible to
    group prices for multiple EVSEs.
  - One row contains a single price component of an EVSE. If the tariff of an
    EVSE contains multiple price components (e.g. kWh and session fee), every
    price component needs to be a separate row. See examples below.
- Update frequency: Chargeprice will per default call the API once per week. The
  frequency is configurable.

## Authorization

The API can optionally be protected by HTTP request headers that the EMSP can
provide to Chargeprice. There is no restriction on which headers need to be
used. One possibility is basic authentication:

```http
Authorization: Basic <credentials>
```


## Response Attributes

| **Name**        | **Required** | **Data Type** | **Example**        | **Description**                                                                                                                |
|-----------------|--------------|---------------|--------------------|--------------------------------------------------------------------------------------------------------------------------------|
| EVSE ID         | TRUE         | String        | AT\*ION\*E0001*123 | eMI3 Roaming ID of the connector                                                                                               |
| Country Code    | TRUE         | String(2)     | AT                 | Country in which the EVSE is deployed. ISO 3166-1 alpha-2                                                                      |
| Currency        | TRUE         | String(3)     | EUR                | Currency in which the price is defined. ISO 4217                                                                               |
| Unit            | TRUE         | String        | ENERGY             | Unit of the Price. Possible values:<br>SESSION (= price per charging session)<br>ENERGY (= price per kWh)<br>TIME (= price per hour) |
| Price           | TRUE         | Float         | 0.5                | B2C price incl. VAT                                                                                                            |
| Limit Start     | FALSE        | Integer       | 3600               | Only valid if Unit is TIME.<br>Defines when this unit price is applied from (in seconds) Default: 0                                |
| Limit End       | FALSE        | Integer       | 7200               | Only valid if Unit is TIME.<br>Defines until when this unit price is applied to (in seconds) Default: unlimited                    |
| TimeOfDay Start | FALSE        | String        | 08:00:00           | Time of day when this unit price is applied from (HH:MM:SS) Default: 00:00:00                                                  |
| TimeOfDay End   | FALSE        | String        | 20:00:00           | Time of day until when this unit price is applied to (HH:MM:SS) Default: 00:00:00                                              |
| Step Size       | FALSE        | Float         | 900                | If Unit is TIME and step size is 60, the customer is charged in blocks of any started 60 seconds.                              |

## Example Response

### Example 1: kWh price with blocking fee and session fee

* EVSE: AT\*ION\*E0001
* Country: Austria
* Tariff:
  * 0.35€/Session
  * 0.5€/kWh 
  * 0.1€/min after 1h until 3h

| EVSE ID        | Country Code | Currency | Unit    | Price | Limit Start | Limit End | TimeOfDay Start | TimeOfDay End | Step Size |
|----------------|--------------|----------|---------|-------|-------------|-----------|-----------------|---------------|-----------|
| AT\*ION\*E0001 | AT           | EUR      | SESSION | 0.35  |             |           |                 |               |           |
| AT\*ION\*E0001 | AT           | EUR      | ENERGY  | 0.5   |             |           |                 |               |           |
| AT\*ION\*E0001 | AT           | EUR      | TIME    | 6     | 3600        | 10800     |                 |               |           |

### Example 2: Day and night price

* EVSE: FR\*FR1\*E001
* Country: France
* Tariff:
  * 0.2€/min 06:00-22:00
  * 0.1€/min 22:00-06:00

| EVSE ID       | Country Code | Currency | Unit | Price | Limit Start | Limit End | TimeOfDay Start | TimeOfDay End | Step Size |
|---------------|--------------|----------|------|-------|-------------|-----------|-----------------|---------------|-----------|
| FR\*FR1\*E001 | FR           | EUR      | TIME | 12    |             |           | 06:00:00        | 22:00:00      |           |
| FR\*FR1\*E001 | FR           | EUR      | TIME | 6     |             |           | 22:00:00        | 06:00:00      |           |

### Example 3: Time blocks

* EVSE: FR\*FR1\*E002
* Country: France
* Tariff:
  * 1.5€ for the first 15 min (-> 1.5€/15min = 0.1€/min)
  * 0.3€/min after the first 15 min

| EVSE ID       | Country Code | Currency | Unit | Price | Limit Start | Limit End | TimeOfDay Start | TimeOfDay End | Step Size |
|---------------|--------------|----------|------|-------|-------------|-----------|-----------------|---------------|-----------|
| FR\*FR1\*E002 | FR           | EUR      | TIME | 6     |             | 900       |                 |               | 900       |
| FR\*FR1\*E002 | FR           | EUR      | TIME | 18    | 900         |           |                 |               |           |
