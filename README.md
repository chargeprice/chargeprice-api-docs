# Chargeprice API Documentation

Documentation for 3rd party applications to access the **Chargeprice API**.

The API offers data around the electric vehicle market including detailed charging tariffs, EMPs, CPOs, charging stations and electric vehicle specs.

**You want the data, but don't want to code?
Check out our [data platform](./data_platform.md)!**

## Getting Access

Please check out our [Plans](./plans.md) as well as the [Terms Of Use](./terms.md).

Then contact sales@chargeprice.net (yes, even for the free plan) and tell us about your application:

* What do you want to build?
* Which data do you need?
* Is it a commercial, non-commercial or academic use case?

We will provide you an API key once all questions are answered and we agreed on the applied [plan](./plans.md).

## Endpoints

### V1

* Charge Prices
  * [Index](./api/v1/charge_prices/index.md)
* Charging Stations
  * [Index](./api/v1/charging_stations/index.md)
  * [Show](./api/v1/charging_stations/show.md)
* Companies
  * [Delete](./api/v1/companies/delete.md)
  * [Index](./api/v1/companies/index.md)
  * [Upsert](./api/v1/companies/upsert.md)
* Partner Feedback
  * [Create](./api/v1/partner_feedback/create.md)
* Tariff Details
  * [Index](./api/v1/tariff_details/index.md)
* Tariffs
  * [Index](./api/v1/tariffs/index.md)
* User Feedback
  * [Create](./api/v1/user_feedback/create.md)
* Vehicles
  * [Index](./api/v1/vehicles/index.md)

### V2

* Tariffs
  * [Delete](./api/v2/tariffs/delete.md)
  * [Index](./api/v2/tariffs/index.md)
  * [Upsert](./api/v2/tariffs/upsert.md)
* Vehicles
  * [Index](./api/v2/vehicles/index.md)
* Vehicle Brands
  * [Index](./api/v2/vehicle_brands/index.md)

## Guides

* [Integrate Charge Prices into your Application](./guides/integrate_charge_prices.md)
* [Using the API for Market Analytics](./guides/using_the_api_for_market_analytics.md)
