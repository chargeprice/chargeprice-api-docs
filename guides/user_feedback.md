# User Feedback

The new web based user feedback form is now available. Depending on the feedback type, use the following links and parameters to access the form:

## Base Query Parameters

The following query parameters are required for all types of feedback:

| **Name**      | **Type** | **Presence** | **Example** | **Description**                                           |
|---------------|----------|--------------|-------------|-----------------------------------------------------------|
| `lang`        | String   | required     | `en`        | Language code for the response content. Defaults to `en`. |
| `appPlatform` | String   | required     | `android`   | The platform of the client app (e.g. `android`, `ios`).   |
| `appVersion`  | String   | required     | `3.2.1`     | The version of the client app.                            |

## Wrong Price

Base URL: https://cms.chargeprice.net/userFeedback/wrongPrice

Query Parameters:

| **Name**      | **Type** | **Presence** | **Example** | **Description**                                           |
|---------------|----------|--------------|-------------|-----------------------------------------------------------|
| `poiId`       | String   | required     | `abc123`    | ID of the POI (charging station) currently viewed.        |
| `tariffId`    | String   | required     | `xyz456`    | ID of the tariff currently viewed.                        |
| `plug`        | String   | required     | `ccs`       | Plug type currently selected.                             |
| `power`       | Float    | required     | `50.0`      | Charging power in kW.                                     |
| `userToken`   | String   | required     | `tok_abc`   | Authentication token of the current user.                 |

## Station Feedback or Missing Price

Base URL: https://cms.chargeprice.net/userFeedback/stationFeedbackOrMissingPrice

Query Parameters:

| **Name**      | **Type** | **Presence** | **Example** | **Description**                                           |
|---------------|----------|--------------|-------------|-----------------------------------------------------------|
| `poiId`       | String   | required     | `abc123`    | ID of the POI (charging station) currently viewed.        |
| `plug`        | String   | required     | `ccs`       | Plug type currently selected.                             |
| `power`       | Float    | required     | `50.0`      | Charging power in kW.                                     |
| `userToken`   | String   | required     | `tok_abc`   | Authentication token of the current user.                 |

## Missing Station, Missing Vehicle, or Other Feedback

Base URLs: 
- Missing Station: https://cms.chargeprice.net/userFeedback/missingStation
- Missing Vehicle: https://cms.chargeprice.net/userFeedback/missingVehicle
- Other Feedback: https://cms.chargeprice.net/userFeedback/other

Query Parameters:

| **Name**      | **Type** | **Presence** | **Example** | **Description**                                           |
|---------------|----------|--------------|-------------|-----------------------------------------------------------|
| `userToken`   | String   | optional     | `tok_abc`   | Authentication token of the current user.                 |