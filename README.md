# Chargeprice API Documentation

Documentation to access the **Chargeprice API**.

The API offers data around the electric vehicle market including detailed
charging tariffs, charge point operators (CPOs), e-mobility providers (EMPs), charging stations and electric vehicle specs.

**You want the data, but don't want to code? Check out our [data
platform](./data_platform.md)!**

## Getting Access

Get in touch via sales@chargeprice.net and tell us about your application:

* What do you want to build?
* Which data do you need?

In the meantime feel free to test and play around with the [staging version of
our API](./test_the_api.md).

You might also be interested in our
[pricing](https://www.chargeprice.net) and [terms of use](./terms.md).

## Getting started guides

Depending on your use case, you might want to start with one of the following guides:

* [Syncing tariff or data to your Backend](/guides/syncing_tariff_data_to_your_system.md)
* [Directly use the Chargeprice API in your Client Application](./guides/integrate_charge_prices.md)
* [Exporting all Charging Stations](/guides/exporting_all_charging_stations.md)
* [Adding your Data to Chargeprice](/guides/adding_your_data_to_chargeprice.md)

## Endpoints

### V1

* Charge Prices
  * [Index](./api/v1/charge_prices/index.md)
  * [Bulk Index](./api/v1/charge_prices/bulk/index.md)
* Charging Stations
  * [Index](./api/v1/charging_stations/index.md)
  * [Show](./api/v1/charging_stations/show.md)
* Companies
  * [Delete](./api/v1/companies/delete.md)
  * [Index](./api/v1/companies/index.md)
  * [Upsert](./api/v1/companies/upsert.md)
* Tariff Details
  * [Index](./api/v1/tariff_details/index.md)
* Tariffs
  * [Index](./api/v1/tariffs/index.md)
* User Feedback
  * [Create](./api/v1/user_feedback/create.md)
* Country Statistics
  * [Index](./api/v1/country_statistics/index.md)
* ~~Vehicles~~ (*deprecated*)
  * [Index](./api/v1/vehicles/index.md)

### V2

* Vehicles
  * [Index](./api/v2/vehicles/index.md)
* Vehicle Brands
  * [Index](./api/v2/vehicle_brands/index.md)