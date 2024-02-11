# Zoho Desk Custom Function Documentation

This documentation provides an explanation of the custom function written in Deluge Script for Zoho Desk. The function is designed to create a time entry record for a specific ticket.

## General Settings

The following general settings are defined at the beginning of the script:

- `zohoDeskApiBaseUrl`: The base URL for Zoho Desk API. [(click here to open the Zoho Desk API documentation)](https://desk.zoho.com/DeskAPIDocument)
- `zohoDeskConnectionName`: The name of the Zoho Desk connection. You can create a new connection under Settings > DEVELOPER SPACE > Connections. Note that the following scopes are required:
    - `Desk.tickets.READ`
    - `Desk.tickets.UPDATE`

## Time Entry Settings

These settings control the creation of time entries:

- `createOnlyOneTimeEntry`: Determines whether only one time entry per ticket should be created. If this option is not active, a time entry is generated for each assigned agent. Of course, only one per agent.
- `timeEntryCustomFieldName`: The custom field name for marking a time entry as automatically created. The field must be of type checkbox! You can check it under Settings > CUSTOMIZATION > Layouts > Time Entry. 
- `timeEntryMinutes`: The duration of the time entry in minutes.
- `timeEntryOwnerId`: The ID of the agent for the time entry owner. If the ticketowner should be used as the owner of the time entry, comment out this line! Otherwise enter the ID of the desired agent.
- `timeEntryChargeType`: The type of charge associated with the time entry. Please only enter values that exist in the pick list of the field or leave it empty (check it under Setup > Layout and Fields > Layouts > Time Entry > Layout)
- `timeEntryDescription`: The description text for the time entry. The description text is NOT saved as HTML! Line breaks can be inserted with `\n`
- `timeEntryIsBillable`: Boolean value indicating whether the time entry is billable or not.

Either the costs are fixed or the costs of the agent and any additional costs are specified and Zoho automatically calculates the total costs. So either only `timeEntryFixedCost` can be specified or `timeEntryAdditionalCost` and/or `timeEntryAgentCostPerHour`. Simply comment out or remove the things that are not required!

- `timeEntryFixedCost`: Fixed cost associated with the time entry. (Float value)
- `timeEntryAdditionalCost`: Additional cost associated with the time entry. (Float value)
- `timeEntryAgentCostPerHour`: Agent cost per hour for the time entry. (Float value)

## Error Handling Settings

Settings related to error handling and notifications:

- `sendMailOnErrors`: Boolean value to determine whether to send an email notification on errors. If this option is active, the following values must be defined. Otherwise they can be commented out or removed:
  - `emailRecipients`: Comma-separated list of email recipients for error notifications.
- `writeDataToAnalyticsOnError`: Boolean value indicating whether to log errors in Zoho Analytics. If this option is active, the following values must be defined. Otherwise they can be commented out or removed
  - `zohoAnalyticsApiBaseUrl`: The base URL for Zoho Analytics API. [(click here to open the Zoho Analytics API documentation)](https://www.zoho.com/analytics/api/v2/introduction.html)
  - `zohoAnalyticsWorkspaceId`: Workspace ID in Zoho Analytics. Open the table, you can see the ID in the URL. `https://analytics.zoho.{tld}/workspace/{#### this value ####}/view/{id}`
  - `zohoAnalyticsViewId`: View ID in Zoho Analytics. Open the table, you can see the ID in the URL. `https://analytics.zoho.{tld}/workspace/{id}/view/{#### this value ####}`
  - `zohoAnalyticsConnectionName`: Connection name for Zoho Analytics. You can create a new connection under Settings > DEVELOPER SPACE > Connections. Note that the following scopes are required:
    - `ZohoAnalytics.data.create`
  - `zohoAnalyticsOrgId`: Organization ID for Zoho Analytics. To find out the ID, open Analytics and navigate to Settings > General > Organization Details. You can see the ID in the URL. `https://analytics.zoho.{tld}/orgsettings/org-details/{#### this value ####}`

### Zoho Analytics Table

You need the following columns for logging in a Zoho Ananlytics table. If you want to use other columns, you can adapt the code yourself.

- `debug_level` Plain Text
- `app` Plain Text
- `cerated_on` Date
- `details` Multi Line Text
- `inputs_and_settings` Multi Line Text
- `user_mail` E-mail

## Variables and Timestamp

The script defines a `settings` map and initializes it. It also extracts the current timestamp and formats it for later use.

## Ticket Owner / Agent Processing

The script retrieves ticket data and validates the agent ID. If no agent ID is specified, it uses the ticket owner's ID.

## Evaluate Time Entries

The script checks existing time entries for the specified agent to determine if an automatic time entry already exists.

## Create Time Entry

Finally, the script validates, builds, and creates a new time entry based on the defined settings. It handles error checks and logs errors if necessary. If configured, it also logs errors in Zoho Analytics and sends email notifications.

## Note

- The script contains a workaround for reading connection names in the `invokeurl` task.
- The code includes detailed error handling and logging mechanisms.

Please make sure to replace placeholder values like `CHANGE_ME` with the actual values required for your Zoho Desk and Zoho Analytics configurations.