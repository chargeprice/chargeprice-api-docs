# PUT /v1/companies/:company_id/charging_stations/:charging_station_id/charge_points/:charge_point_id/status [DRAFT]

Update the status of a charge point

This API follows the https://jsonapi.org specification.

## Headers

* `API-Key: <your_api_key>` (contact sales@chargeprice.net to get access)
* `Content-Type: application/json`

## Request

# Path Parameters

| **Name**            | **Type** | **Example**                            | **Description**                                                                 |
| ------------------- | -------- | -------------------------------------- | ------------------------------------------------------------------------------- |
| company_id          | UUID     | "1e49b853-36fc-47ed-9826-97828b5b2fdd" | Chargeprice-internal ID of CPO (charge point operator)                          |
| charging_station_id | String   | "123abc"                               | Chargeprice-internal ID **OR** external ID (defined by CPO) of the charging station |
| charge_point_id     | String   | "321cdf                                | External ID (defined by CPO) of the charge point which status should be changed |


# Body


| **Name**    | **Type**             | **Presence** | **Example**           | **Description**                                                                                                                                                |
| ----------- | -------------------- | ------------ | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type        | String               | required     | "charge_point_status" | Type of the resource. Needs to be `charge_point_status`.                                                                                                       |
| status      | String               | required     | "available"           | Status of the status. Possible values: `available`, `occupied`, `reserved`, `maintainance`, `unknown`                                                          |
| valid_until | Timestamp* or `null` | optional     | 1582898908000         | If given: Time until this status remains set. After that time the charge point will get `available` again (may get overwritten with another update). If not set: Status will stay until the next update. |

\* Milliseconds since 01.01.1970

## Example

### Request

```http
PUT http://example-base-url.com/v1/companies/1e49b853-36fc-47ed-9826-97828b5b2fdd/charging_stations/123/charge_points/456/status
Content-Type: application/json
Api-Key: my-secret-key

{
  "data": {
    "type": "charge_point_status",
    "attributes": {
      "status": "occupied",
      "valid_until": 1582898908000
    }
  }
}
```

### Response

#### 204 No Content

Status got updated successfully.

##### 400 Bad Request

Client provided invalid request.

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
      "title": "Some error message..."
    }
  ]
}
```