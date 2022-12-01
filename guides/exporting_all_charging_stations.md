# Exporting all Charging Stations

Needed Endpoint: [Index Charging Stations](../api/v1/charging_stations/index.md)

Per default the charging stations API is only returning 400 stations. Via the
pagination options (described in the stations API docs also) you can however
fetch all stations, which match the given criteria.

In general, the charging stations API supports multiple types of searches. There is no
search type that is explicitely designed for exporting all stations, but this
use case is possible by using the **Radius Search**, with for example the
following parameter:

* `filter[longitude]=0`
* `filter[latitude]=0`
* `filter[radius]=50000000`

*This is a workaround, that searches from the equator in a radius of more
than the circumference of the earth. You can use any combination that covers the
area that you need. In the future there might be other options as well.*

With these basic request parameter + pagination you can walk through all pages as long
as the
response contains `meta.more_available=true`.

The requests might then look like:

1) Page: `<base-url>/v1/charging_stations?filter[longitude]=0&filter[latitude]=0&filter[radius]=50000000&page[number]=1`
  * Response: `meta.more_available=true`
2) Page: `<base-url>/v1/charging_stations?filter[longitude]=0&filter[latitude]=0&filter[radius]=50000000&page[number]=2`
  * Response: `meta.more_available=true`
3) Page: `<base-url>/v1/charging_stations?filter[longitude]=0&filter[latitude]=0&filter[radius]=50000000&page[number]=3`
  * Response: `meta.more_available=true`
4) ... more requests here ...
5) Page: `<base-url>/v1/charging_stations?filter[longitude]=0&filter[latitude]=0&filter[radius]=50000000&page[number]=123`
  * Response: `meta.more_available=false`
6) Everything fetched.


## Considerations
Please call the API sequentially and not in parallel to avoid stressing the
database too much.