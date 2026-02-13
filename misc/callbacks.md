---
icon: webhook
---

# Callbacks

Callbacks are when NotifyNL sends `POST` requests to your service. You can get callbacks when:

* a text message or email you’ve sent is delivered or fails
* your service receives a text message
* a letter you sent is returned

### Set up callbacks <a href="#set-up-callbacks" id="set-up-callbacks"></a>

You must provide:

* a URL where NotifyNL will post the callback to
* a bearer token which NotifyNL will put in the authorisation header of the requests

To do this:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **API integration** page.
3. Select **Callbacks**.

### Retry callbacks

If Notify sends a `POST` request to your service, but the request fails then we will retry.

We will retry every 5 minutes, up to a maximum of 5 times.

### Delivery receipts

When you send an email or text message, NotifyNL will send a receipt to your callback URL with the status of the message. This is an automated method to get the status of messages.

This functionality works with test API keys, but does not work with smoke testing phone numbers or email addresses.

The callback message is formatted in JSON. All of the values are strings, apart from the template version, which is a number. The key, description and format of the callback message arguments will be:

| Key                 | Description                                        | Format                                                                       |
| ------------------- | -------------------------------------------------- | ---------------------------------------------------------------------------- |
| `id`                | Notify’s id for the status receipts                | UUID                                                                         |
| `reference`         | The reference sent by the service                  | `12345678` or null                                                           |
| `to`                | The email address or phone number of the recipient | `hello@notifynl.nl`or `07700912345`                                          |
| `status`            | The status of the notification                     | `delivered`, `permanent-failure`, `temporary-failure` or `technical-failure` |
| `created_at`        | The time the service sent the request              | `2017-05-14T12:15:30.000000Z`                                                |
| `completed_at`      | The last time the status was updated               | `2017-05-14T12:15:30.000000Z` or null                                        |
| `sent_at`           | The time the notification was sent                 | `2017-05-14T12:15:30.000000Z` or null                                        |
| `notification_type` | The notification type                              | `email` or `sms`                                                             |
| `template_id`       | The id of the template that was used               | UUID                                                                         |
| `template_version`  | The version number of the template that was used   | `1`                                                                          |

### Received text messages

If your service receives text messages in NotifyNL, NotifyNL can forward them to your callback URL as soon as they arrive.

Find out [how to let people send text messages to your service](../using-notifynl/using-notifynl/receive-text-messages.md).

The callback message is formatted in JSON. All of the values are strings. The key, description and format of the callback message arguments will be:

| Key                  | Description                                              | Format                      |
| -------------------- | -------------------------------------------------------- | --------------------------- |
| `id`                 | Notify’s id for the received message                     | UUID                        |
| `source_number`      | The phone number the message was sent from               | 447700912345                |
| `destination_number` | The number the message was sent to (your number)         | 07700987654                 |
| `message`            | The received message                                     | Hello Notify!               |
| `date_received`      | The UTC datetime that the message was received by Notify | 2017-05-14T12:15:30.000000Z |

### Returned letters

When a letter you sent is returned, Notify will send details of the returned letter to your callback URL.

Find more [information about returned letters](../using-notifynl/using-notifynl/returned-letters.md). It can take a few weeks to receive information about a returned letter.

The callback message is formatted in JSON. The key, description and format of the callback message arguments will be:

| Key                       | Description                                                 | Format                        |
| ------------------------- | ----------------------------------------------------------- | ----------------------------- |
| `notification_id`         | Notify’s ID for the returned letter                         | UUID                          |
| `reference`               | The reference sent by the service                           | `12345678` or null            |
| `date_sent`               | The time the letter was sent                                | `2017-05-14T12:15:30.000000Z` |
| `sent_by`                 | The email address of the service member who sent the letter | `hello@notifynl.nl` or null   |
| `template_name`           | The name of the template that was used                      | `Template name`               |
| `template_id`             | The id of the template that was used                        | UUID                          |
| `template_version`        | The version number of the template that was used            | `1`                           |
| `spreadsheet_file_name`   | The name of the uploaded spreadsheet                        | `contact_list.csv` or null    |
| `spreadsheet_row_number`  | The row in the spreadsheet                                  | `2` or null                   |
| `upload_letter_file_name` | The name of the uploaded letter                             | `uploaded_letter.pdf` or null |
