# Adding your Data to Chargeprice

This guide describes how a CPO or EMSP can integrate their data (e.g. POIs or
prices) into the Chargeprice system.

## You are a Charge Point Operator (CPO)

### POI / Charging Station Data

#### OCPI *(Preferred)*

The easiert and quickest way to integrate your POIs into Chargeprice is via the
industry-standard [OCPI](https://evroaming.org/ocpi-background/). This requires
the CPO to provide an OCPI-compliant API, where Chargeprice connects to the
Locations module. The current supported versions are
[2.2.1](https://github.com/ocpi/ocpi/tree/2.2.1) and
[2.1.1](https://github.com/ocpi/ocpi/tree/2.1.1).

This is right now also the only option to integrate live status (availability of
a POI) into Chargeprice.

#### Custom API

If OCPI is not an option (e.g. CPO is not roaming and has no EVSE Party ID),
Chargeprice can also integrate your custom API.

Here the CPO needs to provide Chargeprice documentation and access to an API
hosted by the CPO.

Since this requires developments by Chargeprice, such an integration takes more
time than OCPI. Also live status is not supported yet.

#### Manual Import

If you don't install POIs frequently, there is also the option to provide
Chargeprice with a CSV file of POIs, that can be imported manually.

In order to ensure up-to-date data, this manual import needs to repeated either
with every newly installed station, or periodically (e.g. every quarter).

Since this requires manual work on a regular basis, this option is discouraged.

Additionally the manual import is strictly restricted to the following CSV
format. CSVs in any other format are not accepted by Chargeprice.

[View CSV definition](/imports/charging_stations/csv.md)

## You are an E-Mobility Service Provider (EMSP)

### Tariffs / Prices

Since there are different types of tariffs out there, there are different
options, depending on the complexity and structure of the tariff:

#### Tariffs with same price for (almost) all CPOs

These tariffs are usually not very complex, additionally they usually also only
change a few times per year. In such cases a technical integration via API
usually causes too much overhead.

Hence it's sufficient if the EMSP communicates price changes to Chargeprice via
email.

In order to display the prices at all supported CPOs, the EMSP should provide
Chargeprice with a simple list of supported EVSE Operator IDs (e.g. "AT*ION").

#### Tariffs with prices per CPO or EVSE

Such tariffs usually consist of multiple hundreds or thousands of price
definitions, which might change on a regular basis. 

##### OCPI

If you can provide B2C prices via the industry-standard
[OCPI](https://evroaming.org/ocpi-background/), it is the best way to integrate
your prices into Chargeprice. This requires the CPO to provide an OCPI-compliant
API, where Chargeprice connects to the Tariffs module. The current supported
versions are [2.2.1](https://github.com/ocpi/ocpi/tree/2.2.1) and
[2.1.1](https://github.com/ocpi/ocpi/tree/2.1.1).

This is mostly used to integrate CPO prices, as roaming prices are usually not
provided via OCPI.

##### Chargeprice Price Exchange Format

If you are an EMSP that provides roaming, we have developed a simply format to
exchange tariff data.

In order to integrate such tariffs into Chargeprice, the EMSP needs to provide
an API with the prices, that Chargeprice can consume:

[View CSV API definition](/imports/tariffs/csv_api.md)

Note: In case the prices don't change regularly, the data might also be
exchanged manually via email in the same CSV format as the API.

##### Custom API

In some cases the above integration isn't possible, e.g. if the EMSP provides
more dynamic prices that change very often. Here there is also the option for a
custom API integration. Since this requires developments by Chargeprice, such an
integration takes more time.


