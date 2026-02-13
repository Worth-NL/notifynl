---
title: error_codes_message_data
---

| Error message                         | How to fix                                                                                                                     |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **ValidationError (status code 400)** |                                                                                                                                |
| `id is not a valid UUID`              | Check the notification ID.                                                                                                     |
| **NoResultFound (status code 404)**   |                                                                                                                                |
| `No result found`                     | If it’s outside the retention period, you may no longer get the status of the message. The default retention period is 7 days. |
