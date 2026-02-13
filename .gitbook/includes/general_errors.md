---
title: general_errors
---

You may encounter following errors when making requests to a number of NotifyNL’s API endpoints.

| Error message                                                                                            | How to fix                                                                                                       |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                                                                    |                                                                                                                  |
| `Cannot send to this recipient using a team-only API key.`                                               | Use a live API key, or add recipient to `Guest list` (located in API Integration section)                        |
| `Cannot send to this recipient when service is in trial mode – see https://admin.notifynl.nl/trial-mode` | You need to request for your service to go live before you can send messages to people outside your team.        |
| **BadRequestError (status code 403)**                                                                    |                                                                                                                  |
| `Error: Your system clock must be accurate to within 30 seconds`                                         | Check your system clock                                                                                          |
| `Invalid token: API key not found`                                                                       | Use the correct API key. Refer to [API keys](../../misc/api-keys.md) for more information                        |
| **RateLimitError (status code 429)**                                                                     |                                                                                                                  |
| `Exceeded rate limit for key type <team/test/live> of 3000/<custom limit> requests per 60 seconds`       | Refer to [API rate limits](https://docs.notifications.service.gov.uk/java.html#rate-limits) for more information |
| **TooManyRequestsError (status code 429)**                                                               |                                                                                                                  |
| `Exceeded send limits (<sms/email/letter/international_sms>: <LIMIT SIZE>) for today`                    | Refer to [service limits](../../misc/limits.md#daily-limits) for the limit size                                  |
| **Exception (status code 500)**                                                                          |                                                                                                                  |
| `Internal server error`                                                                                  | Notify was unable to process the request, resend your notification.                                              |
