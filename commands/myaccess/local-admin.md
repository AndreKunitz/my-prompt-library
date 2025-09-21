description: Request local admin access

## Context

You are a developer in PayPal, and you need to request access to role `PP_CORP_LOCAL_WORKSTATION_ADMIN`. This role is needed so you can gain admin access so you can update software on your computer. 

## Task
Use Playwright and follow these directions in order.

* `NAVIGATE` to https://myaccess.paypalinc.com/identityiq/home.jsf
* `CLICK` on the Manage My Access button
* `SEARCH` for access `PP_CORP_LOCAL_WORKSTATION_ADMIN`
* `CLICK` on the check so the role is added to the cart
* `CLICK` the Next button
* `CLICK` the comment button and add comment `I need access so I can update software on my computer`
* `CLICK` the "Sunrise Sunset" button (calendar/time icon) to open the date/time widget
* `ENTER` a start date and time that is 3 mins into the future in format `YYYY-MM-DDTHH:MM` (e.g., 2025-06-05T10:52)
* `ENTER` an end date and time that is 1 week in the future in format `YYYY-MM-DDTHH:MM` (e.g., 2025-06-12T10:49)
* `CLICK` on the start date field to trigger validation if the Save button remains disabled
* `CLICK` the Save button
* `CLICK` the Review button
* `CLICK` the Submit button

### IMPORTANT
- If you see a website for SSO login, wait for 20 seconds and check the page again. If the login screen is still showing pause and wait for me to enter my credentials. Once I enter my credentials, you can continue.
- The date/time fields are HTML `datetime-local` inputs that require precise formatting and field interaction to enable the Save button.
- Use system commands to calculate future dates in the correct format before entering them.

## MCP Tools
> If there is no access to this tools, tell me and ask me follow the [docs](https://paypal.atlassian.net/wiki/spaces/SMBFS/pages/2215246080/MCP+-+Open-Source+Servers). 

## Definitions

* MyAccess is an Identity and Access Governance system used to manage identity and access data. It provides core functionality for access governance, ensuring that the right users have the appropriate authorizations within IT assets. MyAccess is used to establish a strong security posture and centralize access management in line with enterprise governance policies.
