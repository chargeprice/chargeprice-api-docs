# Integrate Charge Prices into your Application

## Steps

### 1. Contact Chargeprice

Please contact contact@chargeprice.net and tell us about your application!
We provide you with an API-Key then.

### 2. Integrate the POI Provider of your choice

At the moment the following POI (Charging Station Providers) APIs are supported
as data adapters for the Chargeprice API:

* [GoingElectric.de](https://www.goingelectric.de/stromtankstellen/api/)
* [OpenChargeMap.org](https://openchargemap.org/site/develop#api) (Beta)

Chargeprice doesn't provide an API to fetch POIs (yet). You **need** to
integrate one of the above data sources to use the Chargeprice API and fetch prices.

Get familiar with your chosen API before you continue with the next step.

### 3. Map POI API with Chargeprice API

Here you can find the interface documention of the [Charge Prices
API](../api/v1/charge_prices/index.md) of Chargeprice.

Since the API is designed to support multiple sources, the data of the chosen
source now needs to be provided in the correct format.

#### Going Electric

You will need to map the data of Going Electric to the following attributes in Chargeprice:

| **Going Electric** | **Chargeprice**             | **Transformation** |
| ------------------ | --------------------------- | ------------------ |
| address.country    | station.country             | -                  |
| network            | station.network             | false -> null      |
| coordinates.lng    | station.longitude           | -                  |
| coordinates.lat    | station.latitude            | -                  |
| chargepoints.power | station.charge_points.power | -                  |
| chargepoints.type  | station.charge_points.plug  | -                  |

#### Open Charge Map (Beta)

You will need to map the data of OpenChargeMap to the following attributes in Chargeprice:

| **Open Charge Map**                                         | **Chargeprice**             | **Transformation**                                                                        |
| ----------------------------------------------------------- | --------------------------- | ----------------------------------------------------------------------------------------- |
| AddressInfo.Country.ISOCode                                 | station.country             | -                                                                                         |
| OperatorID                                                  | station.network             | convert to string: 23 => `"23"`                                                             |
| AddressInfo.Longitude                                       | station.longitude           | -                                                                                         |
| AddressInfo.Latitude                                        | station.latitude            | -                                                                                         |
| Connections.PowerKW                                         | station.charge_points.power | -                                                                                         |
| Connections.ConnectionTypeID <br> Connections.CurrentTypeID | station.charge_points.plug  | "ConnectionTypeID,CurrentTypeID", e.g. ConnectionTypeID: 27, CurrentTypeID: 30 => `"27,30"` |