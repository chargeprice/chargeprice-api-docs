# POST /v1/partner_feedback

**[WORK IN PROGRESS]**

Send different kinds of partner feedback to the Chargeprice Team.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

The following types of feedback can be sent:

* `tariff_update`

All of these types have the following generic and required attributes. In
brackets the max. length of the string.

* `content` (100,000): Free text about the change of the tariff
* `language` (2): ISO-639-1 language code (e.g. en, de, nl, fr)

### Attributes per type

All of them are of type `string` and are mandatory (but can have an empty
value). In brackets the max. length of the string.

* `tariff_update`:
  * `tariff` (1000): Name of the user facing tariff, to which the update applies to
  * `cpo` (10,000): Names of the CPOs (comma separated) to which this tariff applies to. If update applies to all CPOs or the CPOs are unknown, it can be left empty.
  * `emp` (1000): Name of the EMP which ownes the tariff.

## Example

### Request

```http
POST http://example-base-url.com/v1/partner_feedback
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "type": "tariff_update",
    "attributes": {
      "content": "AC 0.3€/kWh",
      "tariff": "KiWhi Pass CONFORT",
      "cpo": "IONITY,EnBW",
      "emp": "KiWhi Pass",
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
      "title": "some error"
    }
  ]
}
```