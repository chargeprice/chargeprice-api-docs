# Integrate Charge Prices into your Application

## Steps

### 1. Contact Chargeprice

Please contact contact@chargeprice.net and tell us about your application! We
provide you with an API-Key then.

### 2. Register + Integrate the Going Electric API

At the moment the price calculation of the Chargeprice API depends on charging
station data, that is provided by the Going Electric API:
https://www.goingelectric.de/stromtankstellen/api/

In the future other providers of station data will be supported.

Once your application can get `chargelocations` details, you can move on to step
2:

### 3. Map Going Electric API with Chargeprice API

Here you can find the interface documention of the [Charge Prices
API](../api/v1/charge_prices/index.md) of Chargeprice.

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
| chargecards.id     | charge_card_ids             | -                  |