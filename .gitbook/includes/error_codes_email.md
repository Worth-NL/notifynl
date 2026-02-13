---
title: error_codes_email
---

| Error message                                                                                            | How to fix                                                                                                                                                            |
| -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ValidationError (status code 400)**                                                                    |                                                                                                                                                                       |
| `email_address Not a valid email address`                                                                | Provide a valid recipient email address.                                                                                                                              |
| `one_click_unsubscribe_url is not a valid https url`                                                     | Provide a valid https url for your unsubscribe link.                                                                                                                  |
| **BadRequestError (status code 400)**                                                                    |                                                                                                                                                                       |
| `email_reply_to_id <reply to id> does not exist in database for service id <service id>`                 | Go to your service Settings and copy a valid `email_reply_to_id`. Double check that the API key you are using and the `email_reply_to_id` belong to the same service. |
| `Emails cannot be longer than 2000000 bytes. Your message is <rendered template size in bytes> bytes.`   | Shorten your email message.                                                                                                                                           |
| `Cannot send to this recipient when service is in trial mode - see https://admin.notifynl.nl/trial-mode` | Your service cannot send this email in [trial mode](../../using-notifynl/using-notifynl/trial-mode.md). To fix, you need to request for your service to go live.      |
