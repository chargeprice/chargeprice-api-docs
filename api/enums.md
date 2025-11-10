# Enum Values

## Tag Kind

- star
- alert
- info
- lock

Please expect that new Tag Kinds might be added at any time!

## Languages (ISO-639-1)

- en
- de
- fr
- nl
- da

## Plugs

- ccs: CCS standard with Type 2 connector.
- type2
- chademo
- schuko
- type1
- type3 
- tesla_ccs: Used for Superchargers that use the CCS standard, but are only open to Tesla vehicles.
- tesla_suc: Used for Tesla Supercharger stations that are not open to other vehicles.
- ccs1: CCS standard with Type 1 connector.
- nacs: North American Charging Standard, formerly known as Tesla connector.

## No Price Reason

- prices_per_station: Prices are different for each station and not yet integrated on Chargeprice.
- not_public: Prices are not public and hence not available on Chargeprice.
- not_yet_listed: Prices are public, but not yet integrated into Chargeprice.
- no_reliable_data_available: Prices are public, but ambiguous, unclear or somebody reported them as wrong.

## Facilities

This list is based on the [facilities in OCPI](https://github.com/ocpi/ocpi/blob/master/mod_locations.asciidoc#mod_locations_facility_enum):

- hotel: Any kind of hotel, motel, hostel...
- restaurant: Any kind of restaurant incl. fast food.
- cafe
- supermarket
- fuel_station
- train_station
- car_dealership: Any kind of shop that sells or repairs cars.
- motorway_access: Close nearby you can enter a motorway.

New values might be added at any time!

## Parking Type

This list is based on the [parking types in OCPI](https://github.com/ocpi/ocpi/blob/master/mod_locations.asciidoc#mod_locations_parkingtype_enum):

- along_motorway: Service station/area.
- parking_garage
- underground_garage
- parking_lot

New values might be added at any time!

## Integration Status

- pending: No or very limited price coverage.
- in_progress: Little price coverage.
- complete: Most relevant or all prices are available.