# Exporting all Charging Stations

Needed Endpoint: [Index Charging Stations](../api/v1/charging_stations/index.md)

Per default the charging stations API is only returning 400 stations. Via pagination, you can however fetch all stations.

Simply set the `export=true` parameter to get all stations without the need to define additional filters.
Also you can increase the page size to 1000 stations per page by setting `page[size]=1000` in order to reduce the number of requests.
With these basic request parameter + pagination you can walk through all pages as long
as the
response contains `meta.more_available=true`.

The requests might then look like:

1) Page: `<base-url>/v1/charging_stations?export=true&page[number]=1&page[size]=1000`
  * Response: `meta.more_available=true`
2) Page: `<base-url>/v1/charging_stations?export=true&&page[number]=2&page[size]=1000`
  * Response: `meta.more_available=true`
3) Page: `<base-url>/v1/charging_stations?export=true&&page[number]=3&page[size]=1000`
  * Response: `meta.more_available=true`
4) ... more requests here ...
5) Page: `<base-url>/v1/charging_stations?export=true&page[number]=123&page[size]=1000`
  * Response: `meta.more_available=false`
6) Everything fetched.


## Considerations
Please call the API sequentially and not in parallel to avoid stressing the
database too much.