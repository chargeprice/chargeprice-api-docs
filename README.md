# Chargeprice API Documentation

Documentation to access the **Chargeprice API**.

The API offers data around the electric vehicle market including detailed
charging tariffs, charge point operators (CPOs), e-mobility providers (EMPs),
charging stations and electric vehicle specs.

**You want the data, but don't want to code? Check out our [data
platform](./data_platform.md)!**

## How to get access?

### Commercial Access

If you are interested in a commercial license, we offer a variety of plans to
suit your needs.

Please get in touch via sales@chargeprice.net. 

### Demo Access

To get started quickly, **you can request a free demo API access via [this
form](https://tally.so/r/w4pJAX)**. You will receive an email with the
instructions automatically. 

*Please be aware that data is limited in the demo version and any commercial use
is prohibited.*

## Getting started

To execute your first API requests as quickly as possible, you can use [this
Postman Collection](./Chargeprice_API.postman_collection.json).
[Postman](https://www.postman.com/) is a popular tool that allows you to easily
test and debug your API requests.

Depending on your use case, you might want to continue with one of the following
guides:

* [Syncing tariff or data to your
  Backend](/guides/syncing_tariff_data_to_your_system.md)
* [Directly use the Chargeprice API in your Client
  Application](./guides/integrate_charge_prices.md)
* [Exporting all Charging Stations](/guides/exporting_all_charging_stations.md)
* [Adding your Data to Chargeprice](/guides/adding_your_data_to_chargeprice.md)

If you have any questions, feel free to reach out to us at
contact@chargeprice.net!

## OCPI Compatibility 
While our API uses a proprietary format to support a more extensive set of
attributes than the OCPI standard allows, we recognize the importance of
interoperability in the e-mobility ecosystem. If you require OCPI-compatible
data, we are happy to provide it upon request.

## Changelog

See the [changelog](./changelog.md) for recent changes to the API.

## Terms of Use

By using the API, you comply to these [terms of use](./terms.md).

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