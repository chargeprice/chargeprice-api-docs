# Add Your Start Charging Button to Chargeprice

Chargeprice can show a "Start Charging" button for your stations, that lets the
user directly start a charging session, or navigate to a page to do so. The
button links to a URL provided by you, which can e.g. open your app via a
deeplink, or open a direct payment website.

In order to show this button, Chargeprice needs to be able to resolve a URL for
a given EVSE ID. There are multiple ways to provide this URL to Chargeprice,
depending on your setup. Pick whichever fits best - if in doubt, get in touch
with us.

## Option 1: URL Pattern (Preferred)

If your URL can be built from the EVSE ID (e.g. as a query parameter or path
segment), you only need to provide us with the URL pattern, e.g.:

```
https://www.xyz.com?evseId={evseId}
```

Chargeprice will then replace `{evseId}` with the EVSE ID of the station (e.g.
`DE*ABC*123`) to build the link. This is the simplest option, since it requires
no further integration on your side.

## Option 2: API

If the URL can't be derived from a fixed pattern (e.g. it contains a token, internal ID or
session information), you can provide Chargeprice with an API to fetch the
start charging link.

The API needs to return the URL for a given EVSE ID, or alternatively for a
given location (latitude/longitude), if you don't work with EVSE IDs.

## Option 3: Price Exchange CSV/API

If you already provide your tariffs to Chargeprice via our
[CSV API](/imports/tariffs/csv_api.md), you can include the start
charging link directly in the `start_charging_link` column of that format.
This way no separate integration is needed, since the link is imported
together with your prices.

Note: This option is only available if you provide prices in the per-EVSE CSV
format, not in the [per EVSE Party format](/imports/tariffs/csv_per_evse_party_api.md).
