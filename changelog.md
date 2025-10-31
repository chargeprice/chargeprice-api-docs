# Changelog

From September 2025 on we will summarize changes to the API in this changelog to make it easier for you to track changes.

## 2025-09-25 [Added] Average Price Indicator

New field `is_average_price` in `charge_point_prices` and `tariff_details` to indicate if the price is an average price for the whole network of a CPO or if it is a specific price for a POI.

## 2025-10-30 [Added] New Dimension "parking_minute"

New dimension `parking_minute` added to `v2/tariffs` and `v1/tariff_details` (optionally returned, not added per default) to support parking fees after charging.