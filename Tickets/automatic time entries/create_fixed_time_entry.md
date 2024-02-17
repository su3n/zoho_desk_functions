# Zoho Desk Custom Function Documentation

This documentation provides an explanation of the custom function written in Deluge Script for Zoho Desk. The function is designed to create a time entry record for a specific ticket.

**Please note**: A custom field must be created so that time entries can be marked as automatic entries! The name must be stored in the `timeEntryCustomFieldName` setting! Further details below.

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

## Error Handling

### E-Mail

Settings for error handling and notifications:

- `sendMailOnErrors`: Boolean value controlling email notifications on errors. Requires defined values for:
  - `emailRecipients`: Comma-separated list of email recipients.
- `writeDataToAnalyticsOnError`: Boolean value indicating logging errors in Zoho Analytics. Requires defined values for:
  - `zohoAnalyticsApiBaseUrl`: Base URL for Zoho Analytics API.
  - `zohoAnalyticsWorkspaceId`: Workspace ID in Zoho Analytics.
  - `zohoAnalyticsViewId`: View ID in Zoho Analytics.
  - `zohoAnalyticsConnectionName`: Connection name for Zoho Analytics. Requires scope: `ZohoAnalytics.data.create`.
  - `zohoAnalyticsOrgId`: Organization ID in Zoho Analytics.

### Zoho Analytics

Columns needed for logging in Zoho Analytics:

- `debug_level` (Plain Text): Indicates the level of detail for logging (e.g., DEBUG, INFO, ERROR).
- `app` (Plain Text): Holds the name of the application for logging.
- `location` (Plain Text): Basic information about the context of the logging.
- `location_details` (Multi-Line Text): Additional context details for logging.
- `created_on` (Date): Timestamp of the logging event.
- `message` (Multi-Line Text): General details or error messages.
- `user_mail` (E-mail): User's email associated with the logging event.

## Process / Workflow

### Validate Agent ID

- Validate the specified agent ID format (18 digits).
- If no agent ID is provided, retrieve ticket data to obtain the ticket owner's ID. If the ticket is not assigned, the process is canceled.

### Evaluate Time Entries of the Ticket

- Retrieve time entries for the specified ticket.
- Search for automatically added time entries associated with the specified agent.
- Cancel the process if an automatic time entry is found for the agent (as defined in the settings or based on the current ticket owner).

### Create Time Entry

- Validate and construct the time entry using specified parameters.
- A special flag is used to indicate the automated nature of time entries and to avoid duplicates. This requires a custom field (checkbox) in the time entry module, which is preferably visible to users (see permission settings). The entry is marked as an automatic entry via the checked box. Although adding this field to layouts is optional, it is of course useful for users. For functional reasons, its presence in layouts is not mandatory!