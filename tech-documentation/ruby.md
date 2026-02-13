---
description: >-
  This documentation is for developers interested in using the GOV.UK Notify
  Ruby client to send emails, text messages or letters.
icon: gem
---

# Ruby client documentation

## Set up the client

### Install the client

**Using Bundler (recommended)**

Add `notifications-ruby-client` to your application’s `Gemfile`, for example:

```shellscript
gem "notifications-ruby-client"
```

Then run `bundle install` from your project’s directory.

Refer to the [client changelog](https://github.com/alphagov/notifications-ruby-client/blob/main/CHANGELOG.md) for the version number and the latest updates.

**Installing globally**

Run the following from the command line:

```shellscript
gem install notifications-ruby-client
```

### Create a new instance of the client

Add this code to your application:

```ruby
require "notifications/client"
client = Notifications::Client.new(api_key)
```

To get an API key, [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page. You can find more information in the [API keys](../misc/api-keys.md) section of this documentation.

## Send a message

You can use NotifyNL to send emails, text messages and letters.

### Bulk sending

You cannot use a single API call to send messages in bulk.

To send a batch of messages, use the API to loop over your recipient list, sending one message at a time to each recipient.

Make sure you do not exceed our [rate limits](../misc/limits.md).

### Send a text message

**Method**

```ruby
smsresponse = client.send_sms(
  phone_number: "+447900900123",
  template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a",
)
```

**Arguments**

**phone\_number (required)**

The phone number of the text message recipient. This can be a UK or international number. For example:

```ruby
phone_number: "+447900900123"
```

**template\_id (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```ruby
template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a"
```

**personalisation (optional)**

If a template has placeholder fields for personalised information such as name or reference number, you must provide their values in a hash. For example:

```ruby
personalisation: {
  name: "John Smith",
  ID: "300241",
}
```

You can leave out this argument if a template does not have any placeholder fields for personalised information.

**reference (optional)**

A unique identifier you can create if necessary. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. For example:

```ruby
reference: "your_reference_string"
```

You can leave out this argument if you do not have a reference.

**sms\_sender\_id (optional)**

A unique identifier of the sender of the text message notification.

To find the text message sender:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Text Messages** section, select **Manage** on the **Text Message sender** row.

You can then either:

* copy the sender ID that you want to use and paste it into the method
* select **Change** to change the default sender that the service uses, and select **Save**

For example:

```ruby
sms_sender_id: "8e222534-7f05-4972-86e3-17c5d9f894e2"
```

You can leave out this argument if your service only has one text message sender, or if you want to use the default sender.

**Response**

If the request to the client is successful, the client returns a `Notifications::Client:ResponseNotification` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#method), the object is named `smsresponse`.

You can then call different methods on this object:

| Method                  | Information                                                                                                                     | Type   |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ------ |
| `smsresponse.id`        | Notification UUID                                                                                                               | String |
| `smsresponse.reference` | `reference` argument                                                                                                            | String |
| `smsresponse.content`   | <p>- <code>body</code>: Message body sent to the recipient<br>- <code>from_number</code>: SMS sender number of your service</p> | Hash   |
| `smsresponse.template`  | Contains the `id`, `version` and `uri` of the template                                                                          | Hash   |
| `smsresponse.uri`       | Notification URL                                                                                                                | String |

If you are using the [test API key](../misc/api-keys.md#test), all your messages come back with a `delivered` status.

All messages sent using the [team and guest list](../misc/api-keys.md#team-and-guest-list) or [live](../misc/api-keys.md#live) keys appear on your GOV.UK Notify dashboard.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](ruby.md#errors).

{% include "../.gitbook/includes/error_codes_sms.md" %}

### Send an email

**Method**

```ruby
emailresponse = client.send_email(
  email_address: "sender@something.com",
  template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a",
)
```

**Arguments**

**email\_address (required)**

The email address of the recipient. For example:

```ruby
email_address: "sender@something.com"
```

**template\_id (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```ruby
template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a"
```

**personalisation (optional)**

If a template has placeholder fields for personalised information such as name or reference number, you must provide their values in a hash. For example:

```ruby
personalisation: {
  name: "John Smith",
  year: "2016",
  # pass in an array and it will appear as bullet points in the message:
  required_documents: ["passport", "utility bill", "other id"],
}
```

You can leave out this argument if a template does not have any placeholder fields for personalised information.

Find out how to [reduce the risk of malicious content injection in placeholders](https://docs.notifications.service.gov.uk/ruby.html#reducing-the-risk-of-malicious-content-injection-in-placeholders).

**reference (optional)**

A unique identifier you can create if necessary. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. For example:

```ruby
reference: "your_reference_string"
```

You can leave out this argument if you do not have a reference.

**one\_click\_unsubscribe\_url (recommended)**

If you send subscription emails you must let recipients opt out of receiving them. Read our Using Notify page for more information about [unsubscribe links](../using-notifynl/using-notifynl/unsubscribe-links.md).

The one-click unsubscribe URL will be added to the headers of your email. Email clients will use it to add an unsubscribe button.

```ruby
one_click_unsubscribe_url: "https://example.com/unsubscribe.html?opaque=123456789"
```

The one-click unsubscribe URL must respond to an empty `POST` request by unsubscribing the user from your emails. You can include query parameters to help you identify the user.

Your unsubscribe URL and response must comply with the guidance specified in [Section 3.1 of IETF RFC 8058](https://www.rfcreader.com/#rfc8058_line139).

You can leave out this argument if the email being sent is not a subscription email.

You must also add an unsubscribe link to the bottom of your email. The unsubscribe link at the bottom of your email should take the email recipient to a webpage where they can confirm that they want to unsubscribe.

Find out how to add a link when you create a **New template** or **Edit** an email template.

**email\_reply\_to\_id (optional)**

This is an email address specified by you to receive replies from your users. You must add at least one reply-to email address before your service can go live.

To add a reply-to email address:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Email** section, select **Manage** on the **Reply-to email addresses** row.
4. Select **Add reply-to address**.
5. Enter the email address you want to use, and select **Add**.

For example:

```ruby
email_reply_to_id: "8e222534-7f05-4972-86e3-17c5d9f894e2"
```

**Reducing the risk of malicious content injection in placeholders**

Notify lets you [personalise messages](../using-notifynl/using-notifynl/personalisation.md) using placeholders.

You can [format](../using-notifynl/using-notifynl/formatting-emails-and-letters.md) content or add links and urls into placeholders using Markdown.

If you pass in information from untrusted sources (such as online forms) into your Notify template using personalisation, this may be used to add malicious content and links to notifications you send via Notify.

The malicious content could be:

* Markdown syntax intended to be rendered into HTML
* a plain text URL which would be rendered into a clickable phishing link

An example of how malicious content can be injected into Notify personalisation:

**Template in Notify**:

```markdown
Hello ((name))
```

**Personalisation**:

```ruby
{name: "Anne Example, now [click this evil link](https://malicious.link)"}
```

**Email will appear as**:

```
 Dear Anne Example, now click this evil link
```

We recommend you sanitise all input from untrusted sources to prevent the injection of malicious content. You can use a backslash to escape [individual characters](https://www.markdownguide.org/basic-syntax/#characters-you-can-escape). The characters of most concern are those that could be used to add a URL link such as `[`, `]`, `(` or `)`.

You can leave out this argument if your service only has one email reply-to address, or you want to use the default email address.

**Response**

If the request to the client is successful, the client returns a `Notifications::Client:ResponseNotification` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#send-an-email-method), the object is named `emailresponse`.

You can then call different methods on this object to return the requested information.

| Method                    | Information                                                                                                                                                                                      | Type   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------ |
| `emailresponse.id`        | Notification UUID                                                                                                                                                                                | String |
| `emailresponse.reference` | `reference` argument                                                                                                                                                                             | String |
| `emailresponse.content`   | <p>- <code>body</code>: Message body<br>- <code>subject</code>: Message subject<br>- <code>from_email</code>: From email address of your service found on the <strong>Settings</strong> page</p> | Hash   |
| `emailresponse.template`  | Contains the `id`, `version` and `uri` of the template                                                                                                                                           | Hash   |
| `emailresponse.uri`       | Notification URL                                                                                                                                                                                 | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](ruby.md#errors).

{% include "../.gitbook/includes/error_codes_email.md" %}

### Send a file by email

To send a file by email, add a placeholder to the template then upload a file. The placeholder will contain a secure link to download the file.

The links are unique and unguessable. NotifyNL cannot access or decrypt your file.

Your file will be available to download for a default period of 26 weeks (6 months).

To help protect your files you can also:

* ask recipients to confirm their email address before downloading
* choose the length of time that a file is available to download

**Add contact details to the file download page**

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Email** section, select **Manage** on the **Send files by email** row.
4. Enter the contact details you want to use, and select **Save**.

**Add a placeholder to the template**

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant email template.
3. Select **Edit**.
4. Add a placeholder to the email template using double brackets. For example: “Download your file at: ((link\_to\_file))”

Your email should also tell recipients how long the file will be available to download.

**Upload your file**

You can upload the following file types:

* CSV (.csv)
* image (.jpeg, .jpg, .png)
* Microsoft Excel Spreadsheet (.xlsx)
* Microsoft Word Document (.doc, .docx)
* PDF (.pdf)
* text (.json, .odt, .rtf, .txt)

Your file must be smaller than 2MB. [Contact the NotifyNL team](https://admin.notify.nl/support) if you need to send other file types.

1. Pass the file object as an argument to the `Notifications.prepare_upload` helper method.
2. Pass the result into the personalisation argument.

For example:

```ruby
File.open("file.pdf", "rb") do |f|
    ...
    personalisation: {
      first_name: "Amala",
      application_date: "2018-01-01",
      link_to_file: Notifications.prepare_upload(f),
    }
end
```

**Set the filename**

To do this you will need version 6.0.0 of the Ruby client library, or a more recent version.

You should provide a filename when you upload your file.

The filename should tell the recipient what the file contains. A memorable filename can help the recipient to find the file again later.

The filename must end with a file extension. For example, `.csv` for a CSV file. If you include the wrong file extension, recipients may not be able to open your file.

If you do not provide a filename for your file, Notify will:

* generate a random filename
* try to add the correct file extension

If Notify cannot add the correct file extension, recipients may not be able to open your file.

```ruby
File.open("file.csv", "rb") do |f|
    ...
    personalisation: {
      first_name: "Amala",
      application_date: "2018-01-01",
      link_to_file: Notifications.prepare_upload(f, filename: "2023-12-25-daily-report.csv"),
    }
end
```

**Ask recipients to confirm their email address before they can download the file**

When a recipient clicks the link in the email you’ve sent them, they have to enter their email address. Only someone who knows the recipient’s email address can download the file.

This security feature is turned on by default.

**Turn off email address check (not recommended)**

If you do not want to use this feature, you can turn it off on a file-by-file basis.

To do this you will need version 5.4.0 of the Ruby client library, or a more recent version.

You should not turn this feature off if you send files that contain:

* personally identifiable information
* commercially sensitive information
* information classified as ‘OFFICIAL’ or ‘OFFICIAL-SENSITIVE’ under the [Government Security Classifications](https://www.gov.uk/government/publications/government-security-classifications) policy

To let the recipient download the file without confirming their email address, set the `confirm_email_before_download` flag to `false`.

```ruby
File.open("file.pdf", "rb") do |f|
    ...
    personalisation: {
      first_name: "Amala",
      application_date: "2018-01-01",
      link_to_file: Notifications.prepare_upload(f, confirm_email_before_download: false),
    }
end
```

**Choose the length of time that a file is available to download**

Set the number of weeks you want the file to be available using the `retention_period` key.

To use this feature will need version 5.4.0 of the Ruby client library, or a more recent version.

You can choose any value between 1 week and 78 weeks. When deciding this, you should consider:

* the need to protect the recipient’s personal information
* whether the recipient will need to download the file again later

If you do not choose a value, the file will be available for the default period of 26 weeks (6 months).

Files sent before 12 April 2023 had a longer default period of 78 weeks (18 months).

```ruby
File.open("file.pdf", "rb") do |f|
    ...
    personalisation: {
      first_name: "Amala",
      application_date: "2018-01-01",
      link_to_file: Notifications.prepare_upload(f, retention_period: "52 weeks"),
    }
end
```

**Response**

If the request to the client is successful, the client returns a `Notifications::Client:ResponseNotification` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#send-an-email-method), the object is named `emailresponse`.

You can then call different methods on this object to return the requested information.

| Method                    | Information                                                                                                                                                                                      | Type   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------ |
| `emailresponse.id`        | Notification UUID                                                                                                                                                                                | String |
| `emailresponse.reference` | `reference` argument                                                                                                                                                                             | String |
| `emailresponse.content`   | <p>- <code>body</code>: Message body<br>- <code>subject</code>: Message subject<br>- <code>from_email</code>: From email address of your service found on the <strong>Settings</strong> page</p> | Hash   |
| `emailresponse.template`  | Contains the `id`, `version` and `uri` of the template                                                                                                                                           | Hash   |
| `emailresponse.uri`       | Notification URL                                                                                                                                                                                 | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](ruby.md#errors).

{% include "../.gitbook/includes/error_codes_send_file_email.md" %}

### Send a letter

When you add a new service it will start in [trial mode](../using-notifynl/using-notifynl/trial-mode.md). You can only send letters when your service is live.

To send Notify a request to go live:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Your service is in trial mode** section, select **request to go live**.

**Method**

```ruby
letterresponse = client.send_letter(
  template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a",
  personalisation: {
    address_line_1: "The Occupier",
    address_line_2: "123 High Street",
    address_line_3: "SW14 6BH",
  },
)
```

**Arguments**

**template\_id (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```ruby
template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a"
```

**personalisation (required)**

The personalisation argument always contains the following parameters for the letter recipient’s address:

* `address_line_1`
* `address_line_2`
* `address_line_3`
* `address_line_4`
* `address_line_5`
* `address_line_6`
* `address_line_7`

The address must have at least 3 lines.

The last line needs to be a real UK postcode or the name of a country outside the UK.

Notify checks for international addresses and will automatically charge you the correct postage.

The `postcode` personalisation argument has been replaced. If your template still uses `postcode`, Notify will treat it as the last line of the address.

Any other placeholder fields included in the letter template also count as required parameters. You must provide their values in a hash. For example:

```ruby
personalisation: {
  address_line_1: "The Occupier",  # mandatory address field
  address_line_2: "123 High Street", # mandatory address field
  address_line_3: "SW14 6BH",  # mandatory address field
  name: "John Smith", # field from template
  application_date: "2018-01-01" # field from template,
  # pass in an array and it will appear as bullet points in the letter:
  required_documents: ["passport", "utility bill", "other id"],
},
```

**reference (optional)**

A unique identifier you can create if necessary. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. For example:

```ruby
reference: "your_reference_string"
```

**Response**

If the request to the client is successful, the client returns a `Notifications::Client:ResponseNotification` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#send-a-letter-method), the object is named `letterresponse`.

You can then call different methods on this object to return the requested information.

| Method                     | Information                                                                                       | Type   |
| -------------------------- | ------------------------------------------------------------------------------------------------- | ------ |
| `letterresponse.id`        | Notification UUID                                                                                 | String |
| `letterresponse.reference` | `reference` argument                                                                              | String |
| `letterresponse.content`   | <p>- <code>body</code>: Letter body<br>- <code>subject</code>: Letter subject or main heading</p> | Hash   |
| `letterresponse.template`  | Contains the `id`, `version` and `uri` of the template                                            | Hash   |
| `letterresponse.uri`       | Notification URL                                                                                  | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](ruby.md#errors).

{% include "../.gitbook/includes/error_codes_letter.md" %}

### Send a precompiled letter

**Method**

```ruby
precompiled_letter = client.send_precompiled_letter(reference, pdf_file)
```

**Arguments**

**reference (required)**

A unique identifier you create. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address.

**pdf\_file (required)**

The precompiled letter must be a PDF file which meets [the GOV.UK Notify letter specification](https://www.notifications.service.gov.uk/using-notify/guidance/letter-specification).

```ruby
File.open("path/to/pdf_file", "rb") do |pdf_file|
    client.send_precompiled_letter("your reference", pdf_file)
end
```

**postage (optional)**

You can choose first class, second class or economy mail postage for your precompiled letter. Set the value to `first` for first class, `second` for second class or `economy` for economy mail. If you do not pass in this argument, the postage will default to second class.

```ruby
precompiled_letter = client.send_precompiled_letter(
  reference, pdf_file, "first"
)
```

**Response**

If the request to the client is successful, the client returns a `Notifications::Client:ResponsePrecompiledLetter` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#send-a-precompiled-letter-method), the object is named `precompiled_letter`.

You can then call different methods on this object to return the requested information.

| Method                         | Information          | Type   |
| ------------------------------ | -------------------- | ------ |
| `precompiled_letter.id`        | Notification UUID    | String |
| `precompiled_letter.reference` | `reference` argument | String |
| `precompiled_letter.postage`   | `postage` argument   | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](ruby.md#errors).

{% include "../.gitbook/includes/error_codes_precompiled.md" %}

## Get message data

### Get the data for one message

You can only get the data for messages sent within the retention period. The default retention period is 7 days.

**Method**

```ruby
response = client.get_notification(id)
```

**Arguments**

**id (required)**

The ID of the notification. To find the notification ID, you can either:

* check the response to the [original notification method call](https://docs.notifications.service.gov.uk/ruby.html#response)
* [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::Notification` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#get-the-status-of-one-message-method), the object is named `response`.

You can then call different methods on this object to return the requested information.

| Method                                                | Information                                                                                           | Type    |
| ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------- |
| `response.id`                                         | Notification UUID                                                                                     | String  |
| `response.reference`                                  | String supplied in `reference` argument                                                               | String  |
| `response.email_address`                              | Recipient email address (email only)                                                                  | String  |
| `response.phone_number`                               | Recipient phone number (SMS only)                                                                     | String  |
| `response.line_1`                                     | Recipient address line 1 of the address (letter only)                                                 | String  |
| `response.line_2`                                     | Recipient address line 2 of the address (letter only)                                                 | String  |
| `response.line_3`                                     | Recipient address line 3 of the address (letter only)                                                 | String  |
| `response.line_4`                                     | Recipient address line 4 of the address (letter only)                                                 | String  |
| `response.line_5`                                     | Recipient address line 5 of the address (letter only)                                                 | String  |
| `response.line_6`                                     | Recipient address line 6 of the address (letter only)                                                 | String  |
| `response.postage`                                    | Postage class of the notification sent (letter only)                                                  | String  |
| `response.type`                                       | Type of notification sent (sms, email or letter)                                                      | String  |
| `response.status`                                     | Notification status (sending / delivered / permanent-failure / temporary-failure / technical-failure) | String  |
| `response.template`                                   | Template UUID                                                                                         | String  |
| `response.body`                                       | Notification body                                                                                     | String  |
| `response.subject`                                    | Notification subject (email and letter)                                                               | String  |
| `response.sent_at`                                    | Date and time notification sent to provider                                                           | String  |
| `response.created_at`                                 | Date and time notification created                                                                    | String  |
| `response.completed_at`                               | Date and time notification delivered or failed                                                        | String  |
| `response.created_by_name`                            | Name of sender if notification sent manually                                                          | String  |
| `response.one_click_unsubscribe`                      | URL that you provided so your recipients can unsubscribe (email only)                                 | String  |
| `response.is_cost_data_ready`                         | This field is true if cost data is ready, and false if it isn’t                                       | Boolean |
| `response.cost_in_pounds`                             | Cost of the notification in pounds. The cost does not take free allowance into account                | Float   |
| `response.cost_details.billable_sms_fragments`        | Number of billable SMS fragments in your text message (SMS only)                                      | Integer |
| `response.cost_details.international_rate_multiplier` | For international SMS rate is multiplied by this value (SMS only)                                     | Integer |
| `response.cost_details.sms_rate`                      | Cost of 1 SMS fragment (SMS only)                                                                     | Float   |
| `response.cost_details.billable_sheets_of_paper`      | Number of sheets of paper in the letter you sent, that you will be charged for (letter only)          | Integer |
| `response.cost_details.postage`                       | Postage class of the notification sent (letter only)                                                  | String  |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](ruby.md#errors).

{% include "../.gitbook/includes/error_codes_message_data.md" %}

### Get the data for multiple messages

This API call returns one page with data for up to 250 messages. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the `older_than` argument.

You can only get messages that are within your data retention period. The default data retention period is 7 days. It can be changed in your Service Settings.

**Method**

```ruby
response = client.get_notifications(
  template_type: "sms",
  status: "failed",
  reference: "your_reference_string",
  older_than: "e194efd1-c34d-49c9-9915-e4267e01e92e",
)
```

You can leave out the `older_than` argument to get the 250 most recent messages.

To get older messages, pass the ID of an older notification into the `older_than` argument. This returns the next 250 oldest messages from the specified notification ID.

**Arguments**

You can leave out these arguments to ignore these filters.

**status (optional)**

The possible statuses that a notification can be in depends on the type of notification:

* [email status](ruby.md#email-status-descriptions)
* [text message status](ruby.md#text-message-status-descriptions)
* [letter status](ruby.md#letter-status-descriptions)
* [precompiled letter status](ruby.md#precompiled-letter-status-descriptions)

You can specify a single status:

```ruby
status: "failed"
```

Or multiple, by specifying an array:

```ruby
status: ["permanent-failure", "temporary-failure"]
```

You can leave out this argument to ignore this filter.

**templateType (optional)**

You can filter by:

* `email`
* `sms`
* `letter`

**reference (optional)**

A unique identifier you can create if necessary. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. For example:

```ruby
reference: "your_reference_string"
```

**older\_than (optional)**

Input the ID of a notification into this argument. If you use this argument, the client returns the next 250 received notifications older than the given ID. For example:

```ruby
older_than: "e194efd1-c34d-49c9-9915-e4267e01e92e"
```

If you leave out this argument, the client returns the most recent 250 notifications.

The client only returns notifications that are 7 days old or newer. If the notification specified in this argument is older than 7 days, the client returns an empty response.

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::NotificationsCollection` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#get-the-status-of-multiple-messages-method), the object is named `response`.

You must then call either the `.links` method or the `.collection` method on this object.

| Method                | Information                                                            |
| --------------------- | ---------------------------------------------------------------------- |
| `response.links`      | Returns a hash linking to the requested notifications (limited to 250) |
| `response.collection` | Returns an array of the required notifications                         |

If you call the `collection` method on this object to return a notification array, you must then call the following methods on the notifications in this array to return information on those notifications:

| Method                                                | Information                                                                                           | Type    |
| ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------- |
| `response.id`                                         | Notification UUID                                                                                     | String  |
| `response.reference`                                  | String supplied in `reference` argument                                                               | String  |
| `response.email_address`                              | Recipient email address (email only)                                                                  | String  |
| `response.phone_number`                               | Recipient phone number (SMS only)                                                                     | String  |
| `response.line_1`                                     | Recipient address line 1 of the address (letter only)                                                 | String  |
| `response.line_2`                                     | Recipient address line 2 of the address (letter only)                                                 | String  |
| `response.line_3`                                     | Recipient address line 3 of the address (letter only)                                                 | String  |
| `response.line_4`                                     | Recipient address line 4 of the address (letter only)                                                 | String  |
| `response.line_5`                                     | Recipient address line 5 of the address (letter only)                                                 | String  |
| `response.line_6`                                     | Recipient address line 6 of the address (letter only)                                                 | String  |
| `response.postage`                                    | Postage class of the notification sent (letter only)                                                  | String  |
| `response.postcode`                                   | Recipient postcode (letter only)                                                                      | String  |
| `response.type`                                       | Type of notification sent (sms, email or letter)                                                      | String  |
| `response.status`                                     | Notification status (sending / delivered / permanent-failure / temporary-failure / technical-failure) | String  |
| `response.template`                                   | Template UUID                                                                                         | String  |
| `response.body`                                       | Notification body                                                                                     | String  |
| `response.subject`                                    | Notification subject (email and letter)                                                               | String  |
| `response.sent_at`                                    | Date and time notification sent to provider                                                           | String  |
| `response.created_at`                                 | Date and time notification created                                                                    | String  |
| `response.completed_at`                               | Date and time notification delivered or failed                                                        | String  |
| `response.created_by_name`                            | Name of sender if notification sent manually                                                          | String  |
| `response.one_click_unsubscribe`                      | URL that you provided so your recipients can unsubscribe (email only)                                 | String  |
| `response.is_cost_data_ready`                         | This field is true if cost data is ready, and false if it isn’t                                       | Boolean |
| `response.cost_in_pounds`                             | Cost of the notification in pounds. The cost does not take free allowance into account                | Float   |
| `response.cost_details.billable_sms_fragments`        | Number of billable SMS fragments in your text message (SMS only)                                      | Integer |
| `response.cost_details.international_rate_multiplier` | For international SMS rate is multiplied by this value (SMS only)                                     | Integer |
| `response.cost_details.sms_rate`                      | Cost of 1 SMS fragment (SMS only)                                                                     | Float   |
| `response.cost_details.billable_sheets_of_paper`      | Number of sheets of paper in the letter you sent, that you will be charged for (letter only)          | Integer |
| `response.cost_details.postage`                       | Postage class of the notification sent (letter only)                                                  | String  |

If the notification specified in the `older_than` argument is older than 7 days, the client returns an empty `collection` response.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

| Error message                                                                                                                                                                                                                                                               | How to fix                                                                                                        |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                                                                                                                                                                                                                                       |                                                                                                                   |
| `ValidationError: status ‘elephant’ is not one of [cancelled, created, sending, sent, delivered, pending, failed, technical-failure, temporary-failure, permanent-failure, pending-virus-check, validation-failed, virus-scan-failed, returned-letter, accepted, received]` | Change the [status argument](https://docs.notifications.service.gov.uk/ruby.html#status-optional).                |
| `ValidationError: ‘Apple’ is not one of [sms, email, letter]`                                                                                                                                                                                                               | Change the [template\_type argument](https://docs.notifications.service.gov.uk/ruby.html#template-type-optional). |

In addition to the above, you may also encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to getting the status of messages, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

### Email status descriptions

| Status              | Description                                                                                                                                                                                                                                                                                                                                              |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `created`           | GOV.UK Notify has placed the message in a queue, ready to be sent to the provider. It should only remain in this state for a few seconds.                                                                                                                                                                                                                |
| `sending`           | GOV.UK Notify has sent the message to the provider. The provider will try to deliver the message to the recipient for up to 72 hours. GOV.UK Notify is waiting for delivery information.                                                                                                                                                                 |
| `delivered`         | The message was successfully delivered.                                                                                                                                                                                                                                                                                                                  |
| `permanent-failure` | The provider could not deliver the message because the email address was wrong. You should remove these email addresses from your database.                                                                                                                                                                                                              |
| `temporary-failure` | The provider could not deliver the message. This can happen when the recipient’s inbox is full or their anti-spam filter rejects your email. [Check your content does not look like spam](https://www.gov.uk/service-manual/design/sending-emails-and-text-messages#protect-your-users-from-spam-and-phishing) before you try to send the message again. |
| `technical-failure` | <p>Your message was not sent because there was a problem between Notify and the provider.<br>You’ll have to try sending your messages again.</p>                                                                                                                                                                                                         |

### Text message status descriptions

| Status              | Description                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `created`           | GOV.UK Notify has placed the message in a queue, ready to be sent to the provider. It should only remain in this state for a few seconds.                                                                                                                                                                                                                                                                           |
| `sending`           | GOV.UK Notify has sent the message to the provider. The provider will try to deliver the message to the recipient for up to 72 hours. GOV.UK Notify is waiting for delivery information.                                                                                                                                                                                                                            |
| `pending`           | <p>GOV.UK Notify is waiting for more delivery information.<br>GOV.UK Notify received a callback from the provider but the recipient’s device has not yet responded. Another callback from the provider determines the final status of the text message.</p>                                                                                                                                                         |
| `sent`              | The message was sent to an international number. The mobile networks in some countries do not provide any more delivery information. The GOV.UK Notify website displays this status as ‘Sent to an international number’.                                                                                                                                                                                           |
| `delivered`         | The message was successfully delivered. If a recipient blocks your sender ID or mobile number, your message will still show as delivered.                                                                                                                                                                                                                                                                           |
| `permanent-failure` | The provider could not deliver the message. This can happen if the phone number was wrong or if the network operator rejects the message. If you’re sure that these phone numbers are correct, you should [contact GOV.UK Notify support](https://www.notifications.service.gov.uk/support). If not, you should remove them from your database. You’ll still be charged for text messages that cannot be delivered. |
| `temporary-failure` | The provider could not deliver the message. This can happen when the recipient’s phone is off, has no signal, or their text message inbox is full. You can try to send the message again. You’ll still be charged for text messages to phones that are not accepting messages.                                                                                                                                      |
| `technical-failure` | <p>Your message was not sent because there was a problem between Notify and the provider.<br>You’ll have to try sending your messages again. You will not be charged for text messages that are affected by a technical failure.</p>                                                                                                                                                                                |

### Letter status descriptions

| Status              | Description                                                                              |
| ------------------- | ---------------------------------------------------------------------------------------- |
| `accepted`          | GOV.UK Notify has sent the letter to the provider to be printed.                         |
| `received`          | The provider has printed and dispatched the letter.                                      |
| `cancelled`         | Sending cancelled. The letter will not be printed or dispatched.                         |
| `technical-failure` | GOV.UK Notify had an unexpected error while sending the letter to our printing provider. |
| `permanent-failure` | The provider cannot print the letter. Your letter will not be dispatched.                |

### Precompiled letter status descriptions

| Status                | Description                                                                                                                                                                                                                   |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `accepted`            | GOV.UK Notify has sent the letter to the provider to be printed.                                                                                                                                                              |
| `received`            | The provider has printed and dispatched the letter.                                                                                                                                                                           |
| `cancelled`           | Sending cancelled. The letter will not be printed or dispatched.                                                                                                                                                              |
| `pending-virus-check` | GOV.UK Notify has not completed a virus scan of the precompiled letter file.                                                                                                                                                  |
| `virus-scan-failed`   | GOV.UK Notify found a potential virus in the precompiled letter file.                                                                                                                                                         |
| `validation-failed`   | Content in the precompiled letter file is outside the printable area. See the [GOV.UK Notify letter specification](https://www.notifications.service.gov.uk/using-notify/guidance/letter-specification) for more information. |
| `technical-failure`   | GOV.UK Notify had an unexpected error while sending the letter to our printing provider.                                                                                                                                      |
| `permanent-failure`   | The provider cannot print the letter. Your letter will not be dispatched.                                                                                                                                                     |

### Get a PDF for a letter notification

**Method**

This returns the pdf contents of a letter notification.

```ruby
pdf_file = client.get_pdf_for_letter(
  "f33517ff-2a88-4f6e-b855-c550268ce08a" # notification id (required)
)
```

**Arguments**

**id (required)**

The ID of the notification. To find the notification ID, you can either:

* check the response to the [original notification method call](https://docs.notifications.service.gov.uk/ruby.html#get-the-status-of-one-message-response)
* [sign in to GOV.UK Notify](https://www.notifications.service.gov.uk/sign-in) and go to the **API integration** page

**Response**

If the request to the client is successful, the client will return a `string` containing the raw PDF data.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

| Error message                                                         | How to fix                                                                  |
| --------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                                 |                                                                             |
| `ValidationError: id is not a valid UUID`                             | Check the notification ID.                                                  |
| `ValidationError: Notification is not a letter`                       | Check that you are looking up the correct notification.                     |
| `PDFNotReadyError: PDF not available yet, try again later`            | Wait for the letter to finish processing. This usually takes a few seconds. |
| `BadRequestError: File did not pass the virus scan`                   | You cannot retrieve the contents of a letter that contains a virus.         |
| `BadRequestError: PDF not available for letters in technical-failure` | You cannot retrieve the contents of a letter in technical-failure.          |
| `BadRequestError: Notification is not a letter`                       | Check that you are looking up the correct notification.                     |
| **NotFoundError (status code 404)**                                   |                                                                             |
| `NoResultFound: No result found`                                      | Check the notification ID.                                                  |

In addition to the above, you may also encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to getting a PDF for a letter, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

## Get a template

### Get a template by ID

**Method**

This returns the latest version of the template.

```ruby
response = client.get_template_by_id(id)
```

**Arguments**

**id (required)**

The ID of the template. [Sign in to GOV.UK Notify](https://www.notifications.service.gov.uk/sign-in) and go to the **Templates** page to find it. For example:

```ruby
"f33517ff-2a88-4f6e-b855-c550268ce08a"
```

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::Template` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#get-a-template-by-id-method), the object is named `response`.

You can then call different methods on this object to return the requested information.

| Method                          | Information                                                   | Type   |
| ------------------------------- | ------------------------------------------------------------- | ------ |
| `response.id`                   | Template UUID                                                 | String |
| `response.name`                 | Template name                                                 | String |
| `response.type`                 | Template type (email/sms/letter)                              | String |
| `response.created_at`           | Date and time template created                                | String |
| `response.updated_at`           | Date and time template last updated (may be nil if version 1) | String |
| `response.created_by`           | Email address of person that created the template             | String |
| `response.version`              | Template version                                              | String |
| `response.body`                 | Template content                                              | String |
| `response.subject`              | Template subject (email and letter)                           | String |
| `response.letter_contact_block` | Template letter contact block (letter)                        | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

| Error message                       | How to fix                                                                                                                         |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **NotFoundError (status code 404)** |                                                                                                                                    |
| `NoResultFound: No Result Found`    | Check your [template ID](https://docs.notifications.service.gov.uk/ruby.html#get-a-template-by-id-arguments-template-id-required). |

In addition to the above, you may also encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to getting a template, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

### Get a template by ID and version

**Method**

```ruby
response = client.get_template_version(id, version)
```

**Arguments**

**id (required)**

The ID of the template. [Sign in to GOV.UK Notify](https://www.notifications.service.gov.uk/sign-in) and go to the **Templates** page to find it. For example:

```ruby
"f33517ff-2a88-4f6e-b855-c550268ce08a"
```

**version (required)**

The version number of the template.

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::Template` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#get-a-template-by-id-and-version-method), the object is named `response`.

You can then call different methods on this object to return the requested information.

| Method                          | Information                                                                 | Type   |
| ------------------------------- | --------------------------------------------------------------------------- | ------ |
| `response.id`                   | Template UUID                                                               | String |
| `response.name`                 | Template name                                                               | String |
| `response.type`                 | Template type (email/sms/letter)                                            | String |
| `response.created_at`           | Date and time template created                                              | String |
| `response.updated_at`           | Date and time template last updated (may be nil if it is the first version) | String |
| `response.created_by`           | Email address of person that created the template                           | String |
| `response.version`              | Template version                                                            | String |
| `response.body`                 | Template content                                                            | String |
| `response.subject`              | Template subject (email and letter)                                         | String |
| `response.letter_contact_block` | Template letter contact block (letter)                                      | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

| Error message                       | How to fix                                                                                                                         |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **NotFoundError (status code 404)** |                                                                                                                                    |
| `NoResultFound: No Result Found`    | Check your [template ID](https://docs.notifications.service.gov.uk/ruby.html#get-a-template-by-id-arguments-template-id-required). |

In addition to the above, you may also encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to getting a template, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

### Get all templates

**Method**

This returns the latest version of all templates inside a collection object.

```ruby
response = client.get_all_templates(type: "sms")
```

**Arguments**

**type (optional)**

If you do not use `type`, the client returns all templates. Otherwise you can filter by:

* `email`
* `sms`
* `letter`

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::TemplateCollection` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#get-all-templates-method), the object is named `response`.

You must then call the `.collection` method on this object to return an array of the required templates.

Once the client has returned a template array, you must then call the following methods on the templates in this array to return information on those templates.

| Method                          | Information                                                                 | Type   |
| ------------------------------- | --------------------------------------------------------------------------- | ------ |
| `response.id`                   | Template UUID                                                               | String |
| `response.name`                 | Template name                                                               | String |
| `response.type`                 | Template type (email/sms/letter)                                            | String |
| `response.created_at`           | Date and time template created                                              | String |
| `response.updated_at`           | Date and time template last updated (may be nil if it is the first version) | String |
| `response.created_by`           | Email address of person that created the template                           | String |
| `response.version`              | Template version                                                            | String |
| `response.body`                 | Template content                                                            | String |
| `response.subject`              | Template subject (email and letter)                                         | String |
| `response.letter_contact_block` | Template letter contact block (letter)                                      | String |

If no templates exist for a template type or there no templates for a service, the templates array will be empty.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

| Error message                                                       | How to fix                                                                                     |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                               |                                                                                                |
| `ValidationError: Template type is not one of [sms, email, letter]` | Check your [template type](https://docs.notifications.service.gov.uk/ruby.html#type-optional). |

In addition to the above, you may also encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to getting a template, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

### Generate a preview template

**Method**

This generates a preview version of a template.

```ruby
response = client.generate_template_preview(id)
```

The parameters in the personalisation argument must match the placeholder fields in the actual template. The API notification client ignores any extra fields in the method.

**Arguments**

**id (required)**

The ID of the template. [Sign in to GOV.UK Notify](https://www.notifications.service.gov.uk/sign-in) and go to the **Templates** page to find it. For example:

```ruby
"f33517ff-2a88-4f6e-b855-c550268ce08a"
```

**personalisation (optional)**

If a template has placeholder fields for personalised information such as name or application date, you must provide their values in a hash. For example:

```ruby
personalisation: {
  name: "John Smith",
  ID: "300241",
}
```

You can leave out this argument if a template does not have any placeholder fields for personalised information.

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::TemplatePreview` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#generate-a-preview-template-method), the object is named `response`.

You can then call different methods on this object to return the requested information.

| Method             | Information                         | Type   |
| ------------------ | ----------------------------------- | ------ |
| `response.id`      | Template UUID                       | String |
| `response.version` | Template version                    | String |
| `response.body`    | Template content                    | String |
| `response.subject` | Template subject (email and letter) | String |
| `response.type`    | Template type (sms/email/letter)    | String |
| `response.html`    | Body as rendered HTML (email only)  | String |

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

| Error message                                                       | How to fix                                                                                                                         |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                               |                                                                                                                                    |
| `BadRequestError: Missing personalisation: [PERSONALISATION FIELD]` | Check that the personalisation arguments in the method match the placeholder fields in the template.                               |
| **NotFoundError (status code 404)**                                 |                                                                                                                                    |
| `NoResultFound: No Result Found`                                    | Check your [template ID](https://docs.notifications.service.gov.uk/ruby.html#get-a-template-by-id-arguments-template-id-required). |

In addition to the above, you may also encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to generating a preview template, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

## Get received text messages

This API call returns one page of up to 250 received text messages. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the `older_than` argument.

You can only get the data for messages that are 7 days old or newer.

You can also set up [callbacks](https://docs.notifications.service.gov.uk/ruby.html#callbacks) for received text messages.

### Enable received text messages

To receive text messages:

1. Go to the **Text message settings** section of the **Settings** page.
2. Select **Change** on the **Receive text messages** row.

### Get a page of received text messages

**Method**

```ruby
response = client.get_received_texts(
  older_than: "e194efd1-c34d-49c9-9915-e4267e01e92e",
)
```

To get older messages, pass the ID of an older notification into the `older_than` argument. This returns the next oldest messages from the specified notification ID.

If you leave out the `older_than` argument, the client returns the most recent 250 notifications.

**Arguments**

**older\_than (optional)**

Input the ID of a received text message into this argument. If you use this argument, the client returns the next 250 received text messages older than the given ID. For example:

```ruby
older_than: "8e222534-7f05-4972-86e3-17c5d9f894e2"
```

If you leave out the `older_than` argument, the client returns the most recent 250 notifications.

The client only returns notifications that are 7 days old or newer. If the notification specified in this argument is older than 7 days, the client returns an empty `collection` response.

**Response**

If the request to the client is successful, the client returns a `Notifications::Client::ReceivedTextCollection` object. In the example shown in the [Method section](https://docs.notifications.service.gov.uk/ruby.html#get-a-page-of-received-text-messages-method), the object is named `response`.

You must then call either the `.links` method or the `.collection` method on this object.

| Method                | Information                                                    |
| --------------------- | -------------------------------------------------------------- |
| `response.links`      | Returns a hash linking to the requested texts (limited to 250) |
| `response.collection` | Returns an array of the required texts                         |

If you call the `collection` method on this object to return an array, you must then call the following methods on the received texts in this array to return information on those texts:

| Method                   | Information                             | Type   |
| ------------------------ | --------------------------------------- | ------ |
| `response.id`            | Received text UUID                      | String |
| `response.created_at`    | Date and time of received text          | String |
| `response.content`       | Received text content                   | String |
| `response.notify_number` | Number that received text was sent to   | String |
| `response.service_id`    | Received text service ID                | String |
| `response.user_number`   | Number that received text was sent from | String |

If the notification specified in the `older_than` argument is older than 7 days, the client returns an empty `collection` response.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](https://docs.notifications.service.gov.uk/ruby.html#errors).

There are no errors specific to this endpoint, but you may encounter:

* [various schema validation errors](https://docs.notifications.service.gov.uk/ruby.html#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to generating a preview template, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](https://docs.notifications.service.gov.uk/ruby.html#general-errors)

## Errors

### Error handling

If the request is not successful, the client raises a `Notifications::Client::RequestError` exception (or a subclass).

This error consists of:

* a status code, for example `400`
* an error class, for example `BadRequestError`
* a message, for example `Mobile numbers can only include: 0 1 2 3 4 5 6 7 8 9 ( ) + -`

To access these details about the error, read its `error.code`, `error.class`, and `error.message` fields, for example:

```ruby
require_relative "lib/notifications/client/request_error"

begin
  response = client.send_sms(
    phone_number: "+447700900123",
    template_id: "f33517ff-2a88-4f6e-b855-c550268ce08a",
  )
rescue Notifications::Client::RequestError => e
  if (500..599).include? e.code  # using status code for error handling
    # retry logic goes here
  else
    puts e.message  # accessing error details to debug
  end
else
    # unexpected error, raise!
end

```

Error’s `message` field is a string, for example:

```ruby
"BadRequestError: Template not found"
```

Do not use the content of the messages in your code. These can sometimes change, which may affect your API integration.

Use the status code or the error type instead, as these will not change.

### General errors

You may encounter following errors when making requests to a number of Notify’s API endpoints.

| Error message                                                                                                                            | How to fix                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                                                                                                    |                                                                                                                                 |
| `BadRequestError: Cannot send to this recipient using a team-only API key.`                                                              | Use a live API key, or add recipient to `Guest list` (located in API Integration section)                                       |
| `BadRequestError: Cannot send to this recipient when service is in trial mode – see https://www.notifications.service.gov.uk/trial-mode` | You need to request for your service to go live before you can send messages to people outside your team.                       |
| **AuthError (status code 403)**                                                                                                          |                                                                                                                                 |
| `BadRequestError: Error: Your system clock must be accurate to within 30 seconds`                                                        | Check your system clock                                                                                                         |
| `BadRequestError: Invalid token: API key not found`                                                                                      | Use the correct API key. Refer to [API keys](https://docs.notifications.service.gov.uk/ruby.html#api-keys) for more information |
| **RateLimitError (status code 429)**                                                                                                     |                                                                                                                                 |
| `RateLimitError: Exceeded rate limit for key type <team/test/live> of 3000/<custom limit> requests per 60 seconds`                       | Refer to [API rate limits](https://docs.notifications.service.gov.uk/ruby.html#rate-limits) for more information                |
| `TooManyRequestsError: Exceeded send limits (<sms/email/letter/international_sms>: <LIMIT SIZE>) for today`                              | Refer to [service limits](https://docs.notifications.service.gov.uk/ruby.html#daily-limits) for the limit size                  |
| **ServerError (status code 500)**                                                                                                        |                                                                                                                                 |
| `Exception: Internal server error`                                                                                                       | Notify was unable to process the request, resend your notification.                                                             |

### Schema validation errors

The following are a few examples of schema validation errors you may encounter when making a request to a Notify endpoint.

| Error message                                                                                             | How to fix                                                                |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **BadRequestError (status code 400)**                                                                     |                                                                           |
| `ValidationError: template_id is a required property`                                                     | Provide the missing argument.                                             |
| `ValidationError: sms_sender_id is not a valid UUID`                                                      | Check the argument to make sure that it is valid for the given data type. |
| `ValidationError: personalisation <data type of argument you sent> is not of type object`                 | Provide argument in the correct type.                                     |
| `ValidationError: reference <reference string you provided> is too long`                                  | Provide a shorter string.                                                 |
| `ValidationError: template_type <invalid type> is not one of [sms, email, letter]`                        | Make sure that the argument matches one of the items in the list.         |
| `ValidationError: Additional properties are not allowed (<list of unexpected properties> was unexpected)` | Only provide allowed arguments for the endpoint.                          |

### Endpoint-specific errors

In addition to the above, you may also encounter endpoint-specific errors, which are listed under each relevant API endpoint section.

Find references for endpoint-specific errors in:

* [send a message](https://docs.notifications.service.gov.uk/ruby.html#send-a-message)
* [get message data](https://docs.notifications.service.gov.uk/ruby.html#get-message-data)
* [get a template](https://docs.notifications.service.gov.uk/ruby.html#get-a-template)
* [get received text messages](https://docs.notifications.service.gov.uk/ruby.html#get-received-text-messages)

{% include "../.gitbook/includes/footer_links.md" %}
