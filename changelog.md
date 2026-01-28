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

## 2025-12-09 [Added] Added `is_card_payment` Field to v1/charge_prices Endpoint

New field `is_card_payment` added to the `v1/charge_prices` endpoint to indicate if this price is for card payments at a terminal.

## 2025-12-17 [Added] Occupancy Restrictions for Tariffs

New fields `occupancy_gte` and `occupancy_lt` added to the `tariffs` resource to allow for more granular pricing based on the occupancy of the charging site.

# 2026-01-20 [Added] Photos APIs

New APIs to upload and manage photos of charging stations
- `POST /v1/photos` to upload photos
- `DELETE /v1/photos/:photo_id` to delete a photo
- `GET /v1/charging_stations/:charging_station_id/photos` to get photo metadata

# 2026-01-21 [Added] Priority Field in User Feedback

# 2026-01-28 [Added] Max. power filter for Charging Stations API

[Charging Stations](./api/v1/charging_stations/index.md) can now be filtered by maximum power level, next to minimum power.