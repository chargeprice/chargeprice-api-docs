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

### Attributes per type

All of them are of type `string` and are mandatory (but can have an empty
value). In brackets the max. length of the string.

* `other_feedback`: No extra attributes
* `missing_price`
  * `tariff` (100): Name of the EMP + tariff, to which the price belongs to
  * `cpo` (200): Name of the CPO where the price is displayed
  * `price` (100): Price per kWh/minute etc.
  * `poi_link` (200): Link to `chargeprice.app?poi_id=..&poi_source=...`
* `missing_station`
  * `location` (200): Where the station is located. Either address or
    coordinates.
  * `cpo` (200): Operator name of the station.
* `missing_vehicle`
  * `brand` (100): Car brand (e.g. Tesla)
  * `model` (100): Car model (e.g. Model 3)
* `wrong_price`
  * `tariff` (100): Name of the EMP + tariff, to which the price belongs to
  * `cpo` (200): Name of the CPO where the price is displayed
  * `displayed_price` (100): Price displayed in the app.
  * `actual_price` (100): Either total price or price per kWh/minute. Whatever
    the user has at hand.
  * `poi_link` (200): Link to `chargeprice.app?poi_id=..&poi_source=...`

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