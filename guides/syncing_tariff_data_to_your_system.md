# Syncing tariff data to your system

There are many cases where it makes sense to copy all tariff data from
Chargeprice to your own system, instead of always needing to call the
Chargeprice API. This document shows you how to ensure that you always have the
latest data.

The synchronisation happens in multiple steps:
1. [Preparation] Fetch all CPOs of the countries you are interested
1. Fetch all CPO-based tariffs for these CPOs per country
1. Fetch all Charging Stations of CPOs with POI pricing (optional)
1. Fetch all POI-based  tariffs (optional)

## Step 1: [Preparation] Fetch all CPOs of the countries you are interested

Use the [Charging Station Statistics API](../api/v1/charging_stations/statistics/index.md) to find out which CPOs are operating in each of the countries.
This API might give you more information than you need: It also provides the number of charge points for all power levels and plug types. If you are e.g. only interested in CPOs with DC stations, you can do the filtering at this point. If you are however just interested in all CPOs, simply get all the `operator.id`s from the result and remove duplicates.

After this step you should have a list of Chargeprice-internal CPO (operator) IDs and their countries.
Example:
- CPO ID: 1, Country: AT
- CPO ID: 1, Country: DE
- CPO ID: 2, Country: FR 

Note: For simplicity the examples use numeric IDs (1,2,3..). Actually UUIDs (e.g. "c6bc64ab-8ff8-4623-898b-4a20632e686a") are used.

## Step 2: Fetch all CPO-based tariffs for these CPOs per country

Using the [Tariff Details API](../api/v1/tariff_details/index.md) you can now send one request per CPO x country combination. 

The station part of the request could look like this:
```
"station": {
  "country": "AT",
  "operator": {
    "id": "1",
    "type": "company"
  }
}
```

The result will contain all possible tariffs (Ad-hoc and EMP) that are supported at this CPO.
All the prices that are returned here are CPO-based prices, which means that the prices of the tariff applies to all stations of this CPO in the current country.

In some cases the prices are however different per station (also called "POI pricing", example: Tesla).
Per default tariffs with POI pricing are not returned in this API. If you are however interested in fetching POI prices or at least want to know that some EMP supports charging at a CPO, even though there is no pricing information available, you can set the `tariffs_without_prices: true` filter in the request. The result will contain a `no_price_reason` that explains why a price is missing. Additionally the `prices_per_station_available` attribute indicates if POI prices are available for this tariff.

Now you should have successfully synced the CPO-based tariffs to your system, which covers already ~98% of all prices!

# Step 3: Fetch all Charging Stations of CPOs with POI pricing (optional)

For many use cases the CPO-based tariffs are already good enough and you don't need to sync the POI-based tariffs. If your use case however requires also this level of detail, read further:

After fetching the data in step 2 using the `tariffs_without_prices: true` filter, you need to collect all tariffs with `prices_per_station_available: true`. You should have a list similar to this then:
- CPO ID: 3, Tariff IDs: [10,11], Country: AT
- CPO ID: 4, Tariff IDs: [12], Country: NL 

In order to find POI prices, you need to first fetch all relevant charging stations (POIs). Use the [Charging Stations API](../api/v1/charging_stations/index.md) with the `operator.id` and `country` filter to fetch the POIs. You need to send one request per CPO and country combination. One request returns max. 400 stations. If a CPO operates more than 400 stations in a country, you need to pagination to fetch everything.

After doing this for all CPO and country combinations, you should have all the charging station IDs:
- CPO ID: 3, Tariff IDs: [10,11], Country: AT, Charging Station IDs: [20,21]
- CPO ID: 4, Tariff IDs: [12], Country: NL, Charging Station IDs: [23,24]

# Step 4: Fetch all POI-based  tariffs (optional)

In the final step you need the [Tariff Details API](../api/v1/tariff_details/index.md) again, but this time to fetch the prices for each of the charging stations you get in step 3.

The request looks like this:
```
"attributes": {
  "station": {
    "id": ["20","21],
  }
},
"relationships": {
  "tariffs": {
    "data": [
      {
        "id": "10",
        "type": "tariff"
      },
      {
        "id": "11",
        "type": "tariff"
      }
    ]
  }
}
```
In one request you can provide up to 50 station IDs.

Once you have fetched all the combinations, you have all the prices synced!

## Frequently asked questions

### How often should I run the sync?

Our price database is updated twice a week, but for most cases it should be fine
to only run the sync once or twice per week.

### Is it possible to just sync the changes or do I have the always sync everything

Right now a "delta sync" is not supported, you always need to fetch the whole
dataset.

### How "fast" can I run the sync?

Please run the sync sequentially and DON'T do parallel requests, to avoid stressing the system too much.
If you sync whole Europe, it shouldn't take longer than 1-2 hours to get all the data.

### This looks quite complex. Is there also an easier way, like a database dump?

Currently only this method is available, but we are exploring ways to make it easier in the future.