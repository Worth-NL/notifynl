---
title: status_desc_precompiled
---

| Status                | Description                                                                                                                                                                                               |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `accepted`            | NotifyNL has sent the letter to the provider to be printed.                                                                                                                                               |
| `received`            | The provider has printed and dispatched the letter.                                                                                                                                                       |
| `cancelled`           | Sending cancelled. The letter will not be printed or dispatched.                                                                                                                                          |
| `pending-virus-check` | NotifyNL has not completed a virus scan of the precompiled letter file.                                                                                                                                   |
| `virus-scan-failed`   | NotifyNL found a potential virus in the precompiled letter file.                                                                                                                                          |
| `validation-failed`   | Content in the precompiled letter file is outside the printable area. See the [NotifyNL letter specification](https://admin.notifynl.nl/using-notify/guidance/letter-specification) for more information. |
| `technical-failure`   | NotifyNL had an unexpected error while sending the letter to our printing provider.                                                                                                                       |
| `permanent-failure`   | The provider cannot print the letter. Your letter will not be dispatched.                                                                                                                                 |
