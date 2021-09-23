# Using the API for Market Analytics

Data served by the Chargeprice API works perfectly to analyze the e-mobility market.

Most of the time the entry point will be [Index Companies](../api/v1/companies/index.md).
With this you will get all available companies that are EMPs, CPOs or both.

The next steps depend on the use case you want to fulfill:

## Get total costs of a charging session at a specific CPO or station

Needed endpoints:
  * [Index Vehicles](../api/v1/vehicles/index.md)
  * [Index Tariff Overview (optional)](../api/v1/tariffs/index.md)
  * [Prices](../api/v1/charge_prices/index.md)

The most common use case of the Chargeprice API: Get the total costs of a charging session at a specific CPO or even station given a specific vehicle and battery range.

The Guide [Integrate Charge Prices into your Application](./integrate_charge_prices.md) will show you how to implement it.

Hint: Most of the time the price at a specific station is not relevant. Hence you can just pass `longitude: 0 and latitude: 0` in the request of the Prices endpoint.

## Get price components of a charging session at a specific CPO

Needed endpoints:
  * [Index Vehicles](../api/v1/vehicles/index.md)
  * [Index Tariff Overview (optional)](../api/v1/tariffs/index.md)
  * [Tariff Details](../api/v1/tariff_details/index.md)

**Tariff Details** provides you more details on the price components of a specific charging session.

You will get a list of price components (with kWh, minute, session fee, day/night...) for a specific charging session with a specific vehicle and battery range.

Hint: For technical reasons you need to provide the battery range, but in practice 20-80% will give you all data that you need.

## Get raw tariff details

Needed endpoints:
  * [Index Tariffs](../api/v2/tariffs/index.md)

This provides the most level of detail. You will receive the data in the format that we use internally to manage the data and you need to understand the data model very well to interpret it. We recommend a workshop with us, to explain everything.

Examples of what can you analyse with it:
- How many EMPs operate in Germany?
- How many tariffs are do have a monthly fee
- Which tariffs are restricted to specific vehicle brands?
- For which CPOs do EMPs have special tariffs that are different from their standard pricing (e.g. IONITY tariff)?