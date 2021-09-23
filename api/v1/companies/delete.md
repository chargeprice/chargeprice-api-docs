# DELETE /v1/companies/:company_id

Delete a company.

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact contact@chargeprice.net to get access)
* `Content-Type: application/json`

## Authorization Group

`WriteTariffs`

# Request Parameters

**company_id** Id of company that should be deleted.

## Example

### Request

```http
DELETE http://example-base-url.com/v1/companies/2e49b853-36fc-47ed-9826-97828b5b2fdd
Content-Type: application/json
Api-Key: my-secret-key
```

### Response

#### 204 No Content

##### 400 Bad Request

- Client provided invalid request.
- Company still has tariffs. Delete or move the tariffs before deleting the
  company.

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