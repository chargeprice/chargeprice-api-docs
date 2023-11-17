# Integrate Charge Prices into your Application

At the moment the following POI (Charging Station) Provider APIs are supported
as data adapters for the Charge Prices API:

* [GoingElectric.de](https://www.goingelectric.de/stromtankstellen/api/)
* [OpenChargeMap.org](https://openchargemap.org/site/develop#api) (Beta)
* [Chargeprice](/api/v1/charging_stations/index.md)

You **need** to integrate at least one of the above data sources to use the
Chargeprice API and fetch prices.

Get familiar with your chosen API(s) before you continue with the next step.

### Map POI API with Chargeprice API

Here you can find the documention of the [ Prices
endpoint](../api/v1/charge_prices/index.md) of the Chargeprice API.

Since the API is designed to support multiple sources, the data of the chosen
source now needs to be provided in the correct format.

#### Chargeprice

You will need to map the data from the Charging Stations API to the following
attributes in the Charge Prices API:

| **Chargeprice (Charging Stations)** | **Chargeprice (Charge Prices)** | **Transformation** |
| ----------------------------------- | ------------------------------- | ------------------ |
| country                             | station.country                 | -                  |
| operator.id                         | station.network                 | -                  |
| longitude                           | station.longitude               | -                  |
| latitude                            | station.latitude                | -                  |
| charge_points.power                 | station.charge_points.power     | -                  |
| charge_points.plug                  | station.charge_points.plug      | -                  |

Set the `data_adapter` attribute also to `"chargeprice"`.

#### Going Electric

You will need to map the data of Going Electric to the following attributes in
Chargeprice:

| **Going Electric** | **Chargeprice**             | **Transformation** |
| ------------------ | --------------------------- | ------------------ |
| address.country    | station.country             | -                  |
| network            | station.network             | false -> null      |
| coordinates.lng    | station.longitude           | -                  |
| coordinates.lat    | station.latitude            | -                  |
| chargepoints.power | station.charge_points.power | -                  |
| chargepoints.type  | station.charge_points.plug  | -                  |

Set the `data_adapter` attribute also to `"going_electric"`.

#### Open Charge Map (Beta)

You will need to map the data of OpenChargeMap to the following attributes in
Chargeprice:

| **Open Charge Map**                                         | **Chargeprice**             | **Transformation**                                                                          |
| ----------------------------------------------------------- | --------------------------- | ------------------------------------------------------------------------------------------- |
| AddressInfo.Country.ISOCode                                 | station.country             | -                                                                                           |
| OperatorID                                                  | station.network             | convert to string: 23 => `"23"`                                                             |
| AddressInfo.Longitude                                       | station.longitude           | -                                                                                           |
| AddressInfo.Latitude                                        | station.latitude            | -                                                                                           |
| Connections.PowerKW                                         | station.charge_points.power | -                                                                                           |
| Connections.ConnectionTypeID <br> Connections.CurrentTypeID | station.charge_points.plug  | "ConnectionTypeID,CurrentTypeID", e.g. ConnectionTypeID: 27, CurrentTypeID: 30 => `"27,30"` |

Set the `data_adapter` attribute also to `"open_charge_map"`.

Have a look at [this example API request from
OpenChargeMap](https://api.openchargemap.io/v3/poi/?output=json&countrycode=US&maxresults=10)
and find the above attributes.