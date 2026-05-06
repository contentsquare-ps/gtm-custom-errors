# Contentsquare - Custom Errors (Google Tag Manager Template)

This repository contains a Google Tag Manager (GTM) custom tag template that sends custom error events to Contentsquare.

The template pushes data to the global `_uxa` queue using:

`trackError(errorMessage, attributes)`

## What this template does

- Sends a custom error message to Contentsquare.
- Optionally sends up to 5 additional attributes to help categorize the error.
- Uses the Contentsquare queue (`_uxa`) through GTM template sandbox APIs.

## Repository contents

- `template.tpl`: GTM custom template definition (UI fields, sandboxed JS, permissions).
- `metadata.yaml`: Community Template Gallery metadata and version notes.
- `README.md`: Documentation for implementation and usage.

## Requirements

- A web container in Google Tag Manager.
- Contentsquare already installed on the site so `_uxa` is available.
- Permission to publish GTM container changes.

## Template configuration

### 1) Error Message (required)

- Field: `error_message`
- Type: text
- Validation: required, 1 to 300 characters

This should describe the error you want to send to Contentsquare.

### 2) Additional Error Attributes (optional)

- Field: `attributes_array`
- Type: simple table
- Max rows: 5
- Per row:
	- `attribute_name`: 1 to 30 characters
	- `attribute_value`: 1 to 30 characters

Use attributes for dimensions such as feature, environment, severity, page type, module, or source.

## Runtime behavior

When the tag fires, the template:

1. Reads `error_message` and optional `attributes_array`.
2. Builds an attributes object from table rows.
3. Executes:

```javascript
_uxaPush(["trackError", error_message, attributes]);
```

4. Calls `data.gtmOnSuccess()`.

## Permissions

The template requires access to the global `_uxa` object with read, write, and execute permissions.

This is required so the tag can enqueue Contentsquare commands.

## How to use in GTM

1. Open your GTM web container.
2. Go to Templates.
3. In Tag Templates, import this template (or create from `template.tpl`).
4. Create a new tag using Contentsquare - Custom Errors.
5. Set Error Message (required).
6. Add optional attributes (up to 5).
7. Add a trigger (for example: custom event, JS error event mapping, or specific app error signal).
8. Preview, validate in GTM debug mode, then publish.

## Example setup

Example values:

- Error Message: `Checkout API timeout`
- Attributes:
	- `module`: `checkout`
	- `severity`: `high`
	- `env`: `prod`

Resulting command:

```javascript
_uxa.push(["trackError", "Checkout API timeout", {
	module: "checkout",
	severity: "high",
	env: "prod"
}]);
```

## Testing recommendations

- Use GTM Preview mode to confirm the tag fires on expected triggers.
- Verify `error_message` is populated and within limits.
- Verify attributes are capped at 5 and each name/value is within 1 to 30 characters.
- Validate in browser devtools that `_uxa` receives a `trackError` command.

## Troubleshooting

- Tag fires but no data in Contentsquare:
	- Confirm Contentsquare is loaded before this tag runs.
	- Confirm `_uxa` exists on the page.
	- Check trigger timing and firing conditions.
- Validation errors in GTM UI:
	- Ensure required message is not empty.
	- Ensure message and attribute lengths are within limits.
	- Ensure no more than 5 attribute rows are configured.

## Versioning

Template versioning metadata is stored in `metadata.yaml`.

Current recorded version:

- Version 1
- Change note: First Version