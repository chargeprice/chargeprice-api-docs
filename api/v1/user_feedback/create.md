# POST /v1/user_feedback

Send different kinds of user feedback to the Chargeprice Team.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`WriteUserFeedback`

## Request

The following types of feedback can be sent:

* `other_feedback`
* `missing_price`
* `missing_station`
* `missing_vehicle`
* `wrong_price`

All of these types have the following generic and required attributes. In
brackets the max. length of the string.

* `notes` (1000): Any text that the user can let us know.
* `email` (100): Email of the user, if he wants to get a response. Optional, but
  at least an empty string needs to be provided.
* `context` (500): Any text that gives more context of the current state of the
  app. e.g. Language, selected car model, selected battery state...
* `language` (2): ISO-639-1 language code (e.g. en, de, nl, fr)
* `priority`: (enum). Allowed values: `normal`, `high`, `critical`. Default is `normal`.
* `user_products`: (array of enum). Allowed values: `mobile_premium`.

### Attributes per type

Unless stated otherwise, attributes are of type `string`. Required attributes
must be present but can have an empty value. In brackets the max. length of the
string.

* `other_feedback`: No extra attributes
* `missing_price`
  * `tariff` (100, required): Name of the EMP + tariff, to which the price belongs to
  * `cpo` (200, required): Name of the CPO where the price is displayed
  * `price` (100, required): Price per kWh/minute etc.
  * `charging_station_id` (100, optional): ID of the Chargeprice charging station
  * `tariff_id` (100, optional): ID of the Chargeprice tariff
  * `is_direct_payment` (boolean, optional): Whether the price is an ad-hoc / direct payment price
  * `poi_link` (200, optional, deprecated): Use `charging_station_id` instead.
    Link to `chargeprice.app?poi_id=..&poi_source=...`. The `poi_id` is used as
    `charging_station_id` if `poi_source` is `chargeprice` (or missing). Ignored
    if `charging_station_id` is given.
* `missing_station`
  * `location` (200, required): Where the station is located. Either address or
    coordinates.
  * `cpo` (200, required): Operator name of the station.
  * `cpo_id` (100, optional): ID of the Chargeprice operator
  * `charge_points` (array, optional, max. 50 items): Charge points of the station
    * `plug` (string, required): [See supported values](../../enums.md#plugs)
    * `power` (number, required): Power in kW
    * `count` (integer, required): Number of charge points with this plug and power
* `missing_vehicle`
  * `brand` (100, required): Car brand (e.g. Tesla)
  * `model` (100, required): Car model (e.g. Model 3)
* `wrong_price`
  * `tariff` (100, required): Name of the EMP + tariff, to which the price belongs to
  * `cpo` (200, required): Name of the CPO where the price is displayed
  * `displayed_price` (500, required): Price displayed in the app.
  * `actual_price` (500, required): Either total price or price per kWh/minute. Whatever
    the user has at hand.
  * `charging_station_id` (100, optional): ID of the Chargeprice charging station
  * `tariff_id` (100, optional): ID of the Chargeprice tariff
  * `poi_link` (200, optional, deprecated): Same as for `missing_price`.

## Example

### Request

```http
POST http://example-base-url.com/v1/user_feedback
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "type": "other_feedback",
    "attributes": {
      "email": "test@test.at",
      "context": "FR, Tesla Model 3, 20-80%",
      "notes": "Hello Chargeprice!",
      "language": "en"
    }
  }
}

```

### Response

##### 204 No Content

Empty response.

##### 400 Bad Request

Client provided invalid request body.

```json
{
  "errors": [
    {
      "status": "400",
      "code": "BAD_REQUEST",
      "title": "..."
    }
  ]
}
```

##### 403 Forbidden

* API-Key is missing
* API-Key is invalid
* API-Key not authorized to perform action

```json
{
  "errors": [
    {
      "status": "403",
      "code": "FORBIDDEN",
      "title": "api_key missing"
    }
  ]
}
```

##### 500 Internal Server Error

An unexpected error happened.

```json
{
  "errors": [
    {
      "status": "500",
      "code": "INTERNAL_SERVER_ERROR",
      "title": "some error"
    }
  ]
}
```