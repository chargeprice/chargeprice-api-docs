# Using the API for Market Analytics

Data served by the Chargeprice API works perfectly to analyze the e-mobility
market.

Most of the time the entry point will be [Charging Station Statistics](../api/v1/charging_stations/statistics/index.md). 
With this you will get all available CPOs of one or many countries.

The next steps depend on the use case you want to fulfill:

## Get total costs of a charging session at a specific CPO or station

Needed endpoints:
  * [Index Vehicles](../api/v2/vehicles/index.md)
  * [Index Tariff Overview (optional)](../api/v1/tariffs/index.md)
  * [Prices](../api/v1/charge_prices/index.md)

A common use case of the Chargeprice API: Get the total costs of a charging
session at a specific CPO given a specific vehicle and battery range.

The Guide [Integrate Charge Prices into your Application](./integrate_charge_prices.md) 
will show you how to implement it.

Hint: Most of the time the price at a specific station is not relevant. Hence
you can just pass `longitude: 0 and latitude: 0` in the request of the Prices
endpoint.

## Get price components of a charging session at a specific CPO

Needed endpoints:
  * [Index Tariff Overview (optional)](../api/v1/tariffs/index.md)
  * [Tariff Details](../api/v1/tariff_details/index.md)

**Tariff Details** provides you more details on the price components of a
specific charging session.

You will get a list of price components (with kWh, minute, session fee,
day/night...) for a specific charging session with a specific vehicle and
battery range.

Examples of what can you analyse with it:
- How many EMPs operate in Germany?
- How many tariffs do have a monthly fee
- For which CPOs do EMPs have special tariffs that are different from their
  standard pricing (e.g. IONITY tariff)?