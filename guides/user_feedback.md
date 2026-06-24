# User Feedback

The new web based user feedback form is now available. Depending on the feedback type, use the following links and parameters to access the form:

## Base Query Parameters

The following query parameters are required for all types of feedback:

| **Name**       | **Type**   | **Presence** | **Example**               | **Description**                                                                                                                                                                                                   |
|----------------|------------|--------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `lang`         | String     | required     | `en`                      | Language code for the response content. Defaults to `en`.                                                                                                                                                         |
| `appPlatform`  | String     | required     | `android`                 | The platform of the client app (e.g. `android`, `ios`).                                                                                                                                                           |
| `appVersion`   | String     | required     | `3.2.1`                   | The version of the client app.                                                                                                                                                                                    |
| `userProducts` | CSV-String | required     | `mobile_premium,product2` | List of products the user has activated. [See supported values](../api/enums.md#user-products). If a user doesn't have a chargeprice account, but has an active premium subscription, also send "mobile_premium". |
| `hideTitle`    | Boolean    | optional     | `false`                   | Whether to hide the title of the feedback form. Default is `false`. Allowed values are `true` or `false`.                                                                                                         |

## Wrong Price

Base URL: https://cms.chargeprice.net/userFeedback/wrongPrice

Query Parameters:

| **Name**    | **Type** | **Presence** | **Example** | **Description**                                    |
|-------------|----------|--------------|-------------|----------------------------------------------------|
| `poiId`     | String   | required     | `abc123`    | ID of the POI (charging station) currently viewed. |
| `tariffId`  | String   | required     | `xyz456`    | ID of the tariff currently viewed.                 |
| `plug`      | String   | required     | `ccs`       | Plug type currently selected.                      |
| `power`     | Float    | required     | `50.0`      | Charging power in kW.                              |
| `userToken` | String   | required     | `tok_abc`   | Authentication token of the current user.          |

## Station Feedback or Missing Price

Base URL: https://cms.chargeprice.net/userFeedback/stationFeedbackOrMissingPrice

Query Parameters:

| **Name**    | **Type** | **Presence** | **Example** | **Description**                                    |
|-------------|----------|--------------|-------------|----------------------------------------------------|
| `poiId`     | String   | required     | `abc123`    | ID of the POI (charging station) currently viewed. |
| `plug`      | String   | required     | `ccs`       | Plug type currently selected.                      |
| `power`     | Float    | required     | `50.0`      | Charging power in kW.                              |
| `userToken` | String   | required     | `tok_abc`   | Authentication token of the current user.          |

## Missing Station

Base URL: https://cms.chargeprice.net/userFeedback/missingStation

Query Parameters:

| **Name**    | **Type** | **Presence** | **Example** | **Description**                           |
|-------------|----------|--------------|-------------|-------------------------------------------|
| `longitude` | Float    | optional     | `12.3456`   | Longitude of the missing station.         |
| `latitude`  | Float    | optional     | `65.4321`   | Latitude of the missing station.          |
| `userToken` | String   | optional     | `tok_abc`   | Authentication token of the current user. |

## Missing Vehicle

Base URL: https://cms.chargeprice.net/userFeedback/missingVehicle

Query Parameters:

| **Name**    | **Type** | **Presence** | **Example** | **Description**                           |
|-------------|----------|--------------|-------------|-------------------------------------------|
| `userToken` | String   | optional     | `tok_abc`   | Authentication token of the current user. |

## Missing Vehicle, or Other Feedback

Base URL: https://cms.chargeprice.net/userFeedback/other
- Missing Vehicle: https://cms.chargeprice.net/userFeedback/missingVehicle

Query Parameters:

| **Name**    | **Type** | **Presence** | **Example**                     | **Description**                                                                                   |
|-------------|----------|--------------|---------------------------------|---------------------------------------------------------------------------------------------------|
| `userToken` | String   | optional     | `tok_abc`                       | Authentication token of the current user.                                                         |
| `type`      | String   | optional     | `other`                         | Type of feedback provided by the user. Possible values: `reportPhoto`, `other`. Default: `other`. |
| `photoUrl`  | String   | optional     | `https://example.com/photo.jpg` | URL of the photo provided by the user. Required when type is `reportPhoto`.                       |
