---
title: error_codes_precompiled
---

| Error message                                                                                  | How to fix                                                                                                           |
| ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **ValidationError (status code 400)**                                                          |                                                                                                                      |
| `reference is a required property`                                                             | Add a `reference` argument to the method call                                                                        |
| `postage invalid. It must be either first, second or economy.`                                 | Change the value of `postage` argument in the method call to either `"first"`, `"second"` or `"economy"`             |
| **BadRequestError (status code 400)**                                                          |                                                                                                                      |
| `Letter content is not a valid PDF`                                                            | PDF file format is required.                                                                                         |
| `Cannot send letters when service is in trial mode - see https://admin.notifynl.nl/trial-mode` | Your service cannot send this precompiled letter in [trial mode](../../using-notifynl/using-notifynl/trial-mode.md). |
| **BadRequestError (status code 403)**                                                          |                                                                                                                      |
| `Cannot send letters with a team api key`                                                      | Use the correct type of [API key](../../misc/api-keys.md).                                                           |
