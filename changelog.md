# Changelog

From September 2025 on we will summarize changes to the API in this changelog to make it easier for you to track changes.

## 2025-09-25 [Added] Average Price Indicator

New field `is_average_price` in `charge_point_prices` and `tariff_details` to indicate if the price is an average price for the whole network of a CPO or if it is a specific price for a POI.

## 2025-10-30 [Added] New Dimension "parking_minute"

New dimension `parking_minute` added to `v2/tariffs` and `v1/tariff_details` (optionally returned, not added per default) to support parking fees after charging.

## 2025-11-15 [Added] Rate Limit to Open Data API

This [Swiss Open Data API](./api/v1/opendata/charging_prices_switzerland.md) is rate limited to 2 requests per 24 hours per IP address.

## 2025-11-20 [Changed] Access to Swiss Open Data API

The Swiss Open Data API now requires an API key for access. Contact <sales@chargeprice.net> to get access.

## 2025-11-25 [Added] New Field "source_label" to Charging Stations

New field `source_label` added to the `charging_stations` resource to indicate the source of the data.