# Zoho Desk Custom Function Documentation

This documentation outlines the functionality of a custom function designed for Zoho Desk. The primary purpose of this function is to parse the latest email and update the ticket status based on predefined criteria. Leveraging the Zoho Desk API, the function ensures seamless retrieval and updating of ticket information. Detailed error handling and logging capabilities are integrated to enhance reliability.

## Important Settings

### Information

The values within the `information` Map, while not essential for the core functionality, play a crucial role in error logging. By including metadata such as function name, creator name, and creation date, the function establishes traceability, documentation, and accountability.

### Parameters

The `parameters` Map encapsulates values crucial for the function's proper execution. This structuring simplifies logging, ensuring ease of tracking and analysis.

### General Settings

- `zohoDeskApiBaseUrl`: Base URL for the Zoho Desk API, facilitating interaction with ticket data.
- `zohoDeskConnectionName`: Name of the Zoho Desk connection, created under Settings > DEVELOPER SPACE > Connections. Requires specific scopes: `Desk.tickets.READ`, `Desk.tickets.UPDATE`, `Desk.basic.UPDATE`.

### Search Settings

`searchTemplate`: This parameter defines the template utilized to recognize the new ticket status within the email.

The `searchTemplate` employs a structure like `Status="~"` to search for instances of `Status="`*any content*`"`. In this search, the tilde (~) represents a string containing characters such as:
- lowercase a-z
- uppercase A-Z
- German umlauts (äöüÄÖÜß)
- spaces

For example:

- `Status=" Closed "` --> would be identified
- `Status="" Closed ""` --> would not be identified due to incompatible redundant quotation marks
- `status=" Closed "` --> would not be identified as the search is case-sensitive

### Comment Settings

- `createComment`: Boolean value indicating whether to create a comment on the ticket.
- `commentIsPublic`: Boolean value determining whether the comment should be public.
- `commentContent`: Content of the comment, supporting HTML and including a placeholder for the new ticket status.

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

### Preparations for the Search

- Validate and Prepare Search Template.
- Check for illegal characters in the search template.
- Ensure the placeholder character (~) exists exactly once.

### Execution of the Search

- Retrieve the latest thread (email) associated with the provided ticket ID.
- Search for the search template in the email content.
- Extract relevant content based on the defined search template.
- Validate and ensure the search result is not empty.

### Processing of the Search Result

- Retrieve information about the ticket.
- Validate if the found status in the email content is a valid ticket status according to the ticket layout.

### Update Ticket Status

- Check if a blueprint is applied to the ticket and if a valid transition exists to reach the desired status.
- If a valid transition exists in the blueprint, perform the transition.
- Otherwise, directly update the ticket status using the Zoho Desk API.

### Commenting (Optional)

- If configured, create a comment on the ticket indicating the status change.

### Error Logging (Optional)

In case of an error, the contents of all maps (general information about the function, parameter values, settings, and the specific error messages) are send via mail or recorded in the table.

- If specified, an email notification will be sent to the designated recipient list.
- If enabled, logging data will be recorded in a dedicated Zoho Analytics table.