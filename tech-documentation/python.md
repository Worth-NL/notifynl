---
description: >-
  This documentation is for developers interested in using the GOV.UK Notify
  Python client to send emails, text messages or letters. Notify supports Python
  3.8 and higher.
icon: python
---

# Python client documentation

## Set up the client

### Install the client

Run the following code in the command line:

```shellscript
pip install notifications-python-client
```

Refer to the [client changelog](https://github.com/alphagov/notifications-python-client/blob/main/CHANGELOG.md) for the client version number and the latest updates.

### Create a new instance of the client

Add this code to your application:

```python
from notifications_python_client.notifications import NotificationsAPIClient

notifications_client = NotificationsAPIClient(api_key, base_url="https://api.notifynl.nl")
```

#### **Arguments**

**api\_key (required)**

To get an API key, [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page. You can find more information in the [API keys](../misc/api-keys.md) section of this documentation.

**timeout (optional)**

The default timeout is 30 seconds. For more information about timeouts see https://docs.python-requests.org/en/latest/user/advanced/#timeouts.

## Send a message

You can use NotifyNL to send emails, text messages and letters.

### Bulk sending

You cannot use a single API call to send messages in bulk.

To send a batch of messages, use the API to loop over your recipient list, sending one message at a time to each recipient.

Make sure you do not exceed our [rate limits](../misc/limits.md).

### Send a text message

**Method**

```python
response = notifications_client.send_sms_notification(
    phone_number="+447700900123",
    template_id="f33517ff-2a88-4f6e-b855-c550268ce08a",
)
```

**Arguments**

**phone\_number (required)**

The phone number of the recipient of the text message. This can be a UK or international number.

For example:

```python
phone_number="+447700900123", # required string
```

**template\_id (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```python
template_id="f33517ff-2a88-4f6e-b855-c550268ce08a", # required UUID string
```

**personalisation (optional)**

If a template has placeholder fields for personalised information such as name or reference number, you must provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
    "first_name": "Amala",
    "appointment_date": "1 January 2018 at 01:00PM",
},
```

You can leave out this argument if a template does not have any placeholder fields for personalised information.

**reference (optional)**

An identifier you can create if necessary. This reference identifies a single unique message or a batch of messages. It must not contain any personal information such as name or postal address. For example:

```python
reference="your reference", # optional string - identifies notification(s)
```

You can leave out this argument if you do not have a reference.

**sms\_sender\_id (optional)**

A unique identifier of the sender of the text message.

To find the text message sender:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Text Messages** section, select **Manage** on the **Text Message sender** row.

You can then either:

* copy the sender ID that you want to use and paste it into the method
* select **Change** to change the default sender that the service will use, and select **Save**

```python
sms_sender_id="8e222534-7f05-4972-86e3-17c5d9f894e2", # optional UUID string
```

You can leave out this argument if your service only has one text message sender, or if you want to use the default sender.

**Response**

If the request to the client is successful, the client returns a `dict`:

```python
{
  "id": "740e5834-3a29-46b4-9a6f-16142fde533a",  # required string - notification ID
  "reference": "your reference", # optional string - reference you provided when sending the message
  "content": {
    "body": "Hi Amala, your appointment is on 1 January 2018 at 1:00pm",  # required string - message content
    "from_number": "NOTIFYNL"  # required string - sender ID / phone number
  },
  "uri": "https://api.notifynl.nl/v2/notifications/740e5834-3a29-46b4-9a6f-16142fde533a",  # required string
  "template": {
    "id": "f33517ff-2a88-4f6e-b855-c550268ce08a", # required string - template ID
    "version": 3,  # required integer
    "uri": "https://api.notifynl.nl/v2/template/f33517ff-2a88-4f6e-b855-c550268ce08a"  # required string
  }
}
```

If you are using the [test API key](../misc/api-keys.md#test), all your messages will come back with a `delivered` status.

All messages sent using the [team and guest list](../misc/api-keys.md#team-and-guest-list) or [live](../misc/api-keys.md#live) keys will appear on your dashboard.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_sms.md" %}

### Send an email

**Method**

```python
response = notifications_client.send_email_notification(
    email_address="amala@example.com",
    template_id="9d751e0e-f929-4891-82a1-a3e1c3c18ee3",
)
```

**Arguments**

**email\_address (required)**

The email address of the recipient.

For example:

```python
email_address="amala@example.com", # required string
```

**template\_id (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```python
template_id="9d751e0e-f929-4891-82a1-a3e1c3c18ee3", # required UUID string
```

**personalisation (optional)**

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
    "first_name": "Amala",
    "appointment_date": "1 January 2018 at 1:00pm",
    # pass in a list and it will appear as bullet points in the message:
    "required_documents": ["passport", "utility bill", "other id"],
},
```

You can leave out this argument if a template does not have any placeholder fields for personalised information.

**reference (optional)**

An identifier you can create if necessary. This reference identifies a single unique email or a batch of emails. It must not contain any personal information such as name or postal address. For example:

```python
reference="your reference", # optional string - identifies notification(s)
```

You can leave out this argument if you do not have a reference.

**one\_click\_unsubscribe\_url (recommended)**

If you send subscription emails you must let recipients opt out of receiving them. Read our Using Notify page for more information about [unsubscribe links](../using-notifynl/using-notifynl/unsubscribe-links.md).

The one-click unsubscribe URL will be added to the headers of your email. Email clients will use it to add an unsubscribe button.

```python
one_click_unsubscribe_url = "https://example.com/unsubscribe.html?opaque=123456789", # optional string, a URL
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

```python
email_reply_to_id="ca4fdde7-2a67-4a6c-8393-62aa7245751f", # optional UUID string
```

You can leave out this argument if your service only has one reply-to email address, or you want to use the default email address.

**Response**

If the request to the client is successful, the client returns a `dict`:

```python
{
  "id": "201b576e-c09b-467b-9dfa-9c3b689ee730",  # required string - notification ID
  "reference": "your reference",  # optional string - reference you provided when sending the message
  "content": {
    "subject": "Your upcoming pigeon registration appointment",  # required string - message subject
    "body": "Dear Amala\r\n\r\nYour pigeon registration appointment is scheduled for 1 January 2018 at 1:00pm.\r\n\r\nPlease bring:\r\n\n\n* passport\n* utility bill\n* other id\r\n\r\nYours,\r\nPigeon Affairs Bureau",  # required string - message content
    "from_email": "pigeon.affairs.bureau@notifynl.nl",  # required string - "FROM" email address, not a real inbox
    "one_click_unsubscribe_url": "https://example.com/unsubscribe.html?opaque=123456789",  # optional string
  },
  "uri": "https://api.notifynl.nl/v2/notifications/201b576e-c09b-467b-9dfa-9c3b689ee730",  # required string
  "template": {
    "id": "9d751e0e-f929-4891-82a1-a3e1c3c18ee3",  # required string - template ID
    "version": 1,  # required integer
    "uri": "https://api.notifynl.nl/v2/template/9d751e0e-f929-4891-82a1-a3e1c3c18ee3"  # required string
  }
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_email.md" %}

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

```json
{name: "Anne Example, now [click this evil link](https://malicious.link)"}
```

**Email will appear as**:

```
 Dear Anne Example, now click this evil link
```

We recommend you sanitise all input from untrusted sources to prevent the injection of malicious content. You can use a backslash to escape [individual characters](https://www.markdownguide.org/basic-syntax/#characters-you-can-escape). The characters of most concern are those that could be used to add a URL link such as `[`, `]`, `(` or `)`.

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

Your file must be smaller than 2MB. [Contact the NotifyNL team](https://admin.notifynl.nl/support) if you need to send other file types.

Pass the file object as a value into the personalisation argument. For example:

```python
from notifications_python_client import prepare_upload

with open("file.pdf", "rb") as f:
    ...
    personalisation={
      "first_name": "Amala",
      "appointment_date": "1 January 2018 at 1:00pm",
      "link_to_file": prepare_upload(f),
    }
```

**Set the filename**

To do this you will need version 9.0.0 of the Python client library, or a more recent version.

You should provide a filename when you upload your file.

The filename should tell the recipient what the file contains. A memorable filename can help the recipient to find the file again later.

The filename must end with a file extension. For example, `.csv` for a CSV file. If you include the wrong file extension, recipients may not be able to open your file.

If you do not provide a filename for your file, Notify will:

* generate a random filename
* try to add the correct file extension

If Notify cannot add the correct file extension, recipients may not be able to open your file.

```python
from notifications_python_client import prepare_upload

with open("file.csv", "rb") as f:
    ...
    personalisation={
      "first_name": "Amala",
      "appointment_date": "1 January 2018 at 1:00pm",
      "link_to_file": prepare_upload(f, filename="amala_pigeon_affairs_bureau_invite.csv"),
    }
```

**Ask recipients to confirm their email address before they can download the file**

When a recipient clicks the link in the email you’ve sent them, they have to enter their email address. Only someone who knows the recipient’s email address can download the file.

This security feature is turned on by default.

**Turn off email address check (not recommended)**

If you do not want to use this feature, you can turn it off on a file-by-file basis.

To do this you will need version 6.4.0 of the Python client library, or a more recent version.

You should not turn this feature off if you send files that contain:

* personally identifiable information
* commercially sensitive information
* information classified as ‘OFFICIAL’ or ‘OFFICIAL-SENSITIVE’ under the [Government Security Classifications](https://www.gov.uk/government/publications/government-security-classifications) policy

To let the recipient download the file without confirming their email address, set the `confirm_email_before_download` flag to `False`.

```python
from notifications_python_client import prepare_upload

with open("file.pdf", "rb") as f:
    ...
    personalisation={
      "first_name": "Amala",
      "appointment_date": "1 January 2018 at 1:00pm",
      "link_to_file": prepare_upload(f, confirm_email_before_download=False),
    }
```

**Choose the length of time that a file is available to download**

Set the number of weeks you want the file to be available using the `retention_period` key.

To use this feature will need version 6.4.0 of the Python client library, or a more recent version.

You can choose any value between 1 week and 78 weeks. When deciding this, you should consider:

* the need to protect the recipient’s personal information
* whether the recipient will need to download the file again later

If you do not choose a value, the file will be available for the default period of 26 weeks (6 months).

Files sent before 12 April 2023 had a longer default period of 78 weeks (18 months).

```python
from notifications_python_client import prepare_upload

with open("file.pdf", "rb") as f:
    ...
    personalisation={
      "first_name": "Amala",
      "appointment_date": "1 January 2018 at 1:00pm",
      "link_to_file": prepare_upload(f, retention_period="4 weeks"),
    }
```

**Response**

If the request to the client is successful, the client returns a `dict`:

```python
{
  "id": "201b576e-c09b-467b-9dfa-9c3b689ee730",  # required string - notification ID
  "reference": "your reference",  # optional string - reference you provided when sending the message
  "content": {
    "subject": "Your upcoming pigeon registration appointment",  # required string - message subject
    "body": "Dear Amala\r\n\r\nYour pigeon registration appointment is scheduled for 1 January 2018 at 1:00pm.\r\n\r\n Here is a link to your invitation document:\r\nhttps://documents.service.gov.uk/d/YlxDzgNUQYi1Qg6QxIpptA/th46VnrvRxyVO9div6f7hA?key=R0VDmwJ1YzNYFJysAIjQd9yHn5qKUFg-nXHVe3Ioa3A\r\n\r\nPlease bring the invite with you to the appointment.\r\n\r\nYours,\r\nPigeon Affairs Bureau",  # required string - message content - see that the link to document is embedded in the message content
    "from_email": "pigeon.affairs.bureau@notifynl.nl",  # required string - "FROM" email address, not a real inbox
    "one_click_unsubscribe_url": "https://example.com/unsubscribe.html?opaque=123456789",  # optional string
  },
  "uri": "https://api.notifynl.nl/v2/notifications/201b576e-c09b-467b-9dfa-9c3b689ee730",  # required string
  "template": {
    "id": "9d751e0e-f929-4891-82a1-a3e1c3c18ee3",  # required string - template ID
    "version": 1,  # required integer
    "uri": "https://api.notifynl.nl/v2/template/9d751e0e-f929-4891-82a1-a3e1c3c18ee3"  # required string
  }
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_send_file_email.md" %}

### Send a letter

When you add a new service it will start in [trial mode](../using-notifynl/using-notifynl/trial-mode.md). You can only send letters when your service is live.

To send Notify a request to go live:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Your service is in trial mode** section, select **request to go live**.

**Method**

```python
    response = notifications_client.send_letter_notification(
        template_id="64415853-cb86-4cc4-b597-2aaa94ef8c39",
        personalisation={
          "address_line_1": "Amala Bird",
          "address_line_2": "123 High Street",
          "address_line_3": "SW14 6BH",
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

```python
template_id="64415853-cb86-4cc4-b597-2aaa94ef8c39", # required UUID string
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

Any other placeholder fields included in the letter template also count as required parameters. You need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
  "address_line_1": "Amala Bird",  # required string
  "address_line_2": "123 High Street",  # required string
  "address_line_3": "Richmond upon Thames",  # required string
  "address_line_4": "Middlesex",
  "address_line_5": "SW14 6BF",  # last line of address you include must be a postcode or a country name  outside the UK
  "name": "Amala",
  "appointment_date": "1 January 2018 at 1:00pm",
  # pass in a list and it will appear as bullet points in the letter:
  "required_documents": ["passport", "utility bill", "other id"],
}
```

**reference (optional)**

An identifier you can create if necessary. This reference identifies a single unique letter or a batch of letters. It must not contain any personal information such as name or postal address. For example:

```python
reference="your reference" # optional string - identifies notification(s)
```

**Response**

If the request to the client is successful, the client returns a `dict`:

```python
{
  "id": "3d1ce039-5476-414c-99b2-fac1e6add62c",  # required string - notification ID
  "reference": "your reference",  # optional string - reference you provided when sending the message
  "content": {
    "subject": "Your upcoming pigeon registration appointment",  # required string - letter heading
    "body": "Dear Amala\r\n\r\nYour pigeon registration appointment is scheduled for 1 January 2018 at 1:00pm.\r\n\r\nPlease bring:\r\n\n\n* passport\n* utility bill\n* other id\r\n\r\nYours,\r\nPigeon Affairs Bureau",  # required string - letter content
  },
  "uri": "https://api.notifynl.nl/v2/notifications/3d1ce039-5476-414c-99b2-fac1e6add62c",  # required string
  "template": {
    "id": "64415853-cb86-4cc4-b597-2aaa94ef8c39",  # required string - template ID
    "version": 3,  # required integer
    "uri": "https://api.notifynl.nl/v2/template/64415853-cb86-4cc4-b597-2aaa94ef8c39"  # required string
  },
  "scheduled_for": None
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_letter.md" %}

### Send a precompiled letter

**Method**

```python
with open("path/to/pdf_file.pdf", "rb") as pdf_file:
  response = notifications_client.send_precompiled_letter_notification(
      reference="your reference",
      pdf_file=pdf_file,
  )
```

**Arguments**

**reference (required)**

An identifier you create. This reference identifies a single unique precompiled letter or a batch of precompiled letters. It must not contain any personal information such as name or postal address.

```python
reference="your reference" # required string - identifies notification(s)
```

**pdf\_file (required)**

The precompiled letter must be a PDF file which meets [the GOV.UK Notify letter specification](https://www.notifications.service.gov.uk/using-notify/guidance/letter-specification).

```python
with open("path/to/pdf_file.pdf", "rb") as pdf_file:
    notification = notifications_client.send_precompiled_letter_notification(
        reference="your reference", pdf_file=pdf_file
    )
```

**postage (optional)**

You can choose first class, second class or economy mail postage for your precompiled letter. Set the value to `first` for first class, `second` for second class or `economy` for economy mail. If you do not pass in this argument, the postage will default to second class.

```python
postage="first" # optional string
```

**Response**

If the request to the client is successful, the client returns a `dict`:

```python
{
  "id": "1d986ba7-fba6-49fb-84e5-75038a1dd968",  # required string - notification ID
  "reference": "your reference",  # required string - reference your provided
  "postage": "first"  # required string - postage you provided, or else default postage for the letter
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_precompiled.md" %}

## Get message data

### Get the data for one message

You can only get the data for messages sent within the retention period. The default retention period is 7 days.

**Method**

```python
response = notifications_client.get_notification_by_id(notification_id)
```

**Arguments**

**notification\_id (required)**

The ID of the notification, for example:

```python
notification_id = "3d1ce039-5476-414c-99b2-fac1e6add62c"
```

To find the notification ID, you can either:

* check the response to the [original notification method call](https://docs.notifications.service.gov.uk/python.html#response)
* [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page

**Response**

If the request to the client is successful, the client will return a `dict`:

```python
{
    "id": "740e5834-3a29-46b4-9a6f-16142fde533a",  # required string - notification ID
    "reference": "your reference",  # optional string - reference you provided when sending the message
    "email_address": "amala@example.com",  # required string for emails
    "phone_number": "+447700900123",  # required string for text messages
    "line_1": "Amala Bird",  # required string for letter
    "line_2": "123 High Street",  # required string for letter
    "line_3": "Richmond upon Thames",  # required string for letter
    "line_4": "Middlesex",  # optional string for letter
    "line_5": "SW14 6BF",  # optional string for letter
    "line_6": None,  # optional string for letter
    "line_7": None, # optional string for letter
    "postage": "first / second / economy / europe / rest-of-world", # required string for letter
    "type": "sms / letter / email",  # required string
    "status": "sending / delivered / permanent-failure / temporary-failure / technical-failure",  # required string
    "template": {
        "version": 1, # required integer
        "id": "f33517ff-2a88-4f6e-b855-c550268ce08a",  # required string - template ID
        "uri": "/v2/template/{id}/{version}"  # required string
    },
    "body": "Hi Amala, your appointment is on 1 January 2018 at 1:00pm",  # required string - body of notification
    "subject": "Your upcoming pigeon registration appointment",  # required string for email - subject of email
    "created_at": "2024-05-17 15:58:38.342838",  # required string - date and time notification created
    "created_by_name": "Charlie Smith",  # optional string - name of the person who sent the notification if sent manually
    "sent_at": "2024-05-17 15:58:30.143000",  # optional string - date and time notification sent to provider
    "completed_at": "2024-05-17 15:59:10.321000",  # optional string - date and time notification delivered or failed
    "scheduled_for": "2024-05-17 9:00:00.000000", # optional string - date and time notification has been scheduled to be sent at
    "one_click_unsubscribe": "https://example.com/unsubscribe.html?opaque=123456789", # optional string, email only - URL that you provided so your recipients can unsubscribe
    "is_cost_data_ready": True,  # required boolean, this field is true if cost data is ready, and false if it isn't
    "cost_in_pounds": 0.0027,  # optional number - cost of the notification in pounds. The cost does not take free allowance into account
    "cost_details": {
        # for text messages:
        "billable_sms_fragments": 1,  # optional integer - number of billable sms fragments in your text message
        "international_rate_multiplier": 1,  # optional integer - for international sms rate is multiplied by this value
        "sms_rate": 0.0027,  # optional number - cost of 1 sms fragment
        # for letters:
        "billable_sheets_of_paper": 2,  # optional integer - number of sheets of paper in the letter you sent, that you will be charged for
        "postage": "first / second / economy / europe / rest-of-world"  # optional string
    }
}
```

For more information, see the:

* [email status descriptions](python.md#email-status-descriptions)
* [text message status descriptions](python.md#text-message-status-descriptions)
* [letter status descriptions](python.md#letter-status-descriptions)
* [precompiled letter status descriptions](python.md#precompiled-letter-status-descriptions)

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_message_data.md" %}

### Get the data for multiple messages

You can only get the data for messages sent within the retention period. The default retention period is 7 days. It can be changed in your Service Settings.

**Method**

**One page of up to 250 messages**

This API call returns one page with data for up to 250 messages. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the `older_than` argument.

```python
response = notifications_client.get_all_notifications(
  template_type, status, reference, older_than, include_jobs
)
```

You can filter the returned messages by including the following optional arguments in the method:

* [`template_type`](https://docs.notifications.service.gov.uk/python.html#template-type-optional)
* [`status`](https://docs.notifications.service.gov.uk/python.html#status-optional)
* [`reference`](https://docs.notifications.service.gov.uk/python.html#get-the-status-of-multiple-messages-arguments-reference-optional)
* [`older_than`](https://docs.notifications.service.gov.uk/python.html#older-than-optional)
* [`include_jobs`](https://docs.notifications.service.gov.uk/python.html#include-jobs-optional)

For example:

```python
response = notifications_client.get_all_notifications(
  template_type="sms",
  status="failed",
  include_jobs=True,
)
```

**All messages - as an iterator**

This will return a Python iterator object which yields one notification at a time until it has yielded all your notifications.

```python
response = get_all_notifications_iterator()

# to iterate and process the notifications using the iterator:
for notification in response:
    # process notification
```

You can filter the returned messages by including the following optional arguments in the method:

* [`template_type`](https://docs.notifications.service.gov.uk/python.html#template-type-optional)
* [`status`](https://docs.notifications.service.gov.uk/python.html#status-optional)
* [`reference`](https://docs.notifications.service.gov.uk/python.html#get-the-status-of-multiple-messages-arguments-reference-optional)
* [`older_than`](https://docs.notifications.service.gov.uk/python.html#older-than-optional)
* [`include_jobs`](https://docs.notifications.service.gov.uk/python.html#include-jobs-optional)

**Arguments**

**template\_type (optional)**

You can filter by:

* `email`
* `sms`
* `letter`

You can leave out this argument to ignore this filter.

**status (optional)**

You can filter by each:

* [email status](python.md#email-status-descriptions)
* [text message status](python.md#text-message-status-descriptions)
* [letter status](python.md#letter-status-descriptions)
* [precompiled letter status](python.md#precompiled-letter-status-descriptions)

If you filter by `failed` it will return all 3 failure statuses: `permanent-failure`, `temporary-failure` and `technical-failure`.

You can leave out this argument to ignore this filter.

**reference (optional)**

An identifier you can create if necessary. This reference identifies a single unique message or a batch of messages. It must not contain any personal information such as name or postal address. For example:

```python
reference="your reference" # optional string - reference you provided when sending the message
```

You can leave out this argument to ignore this filter.

**older\_than (optional)**

Input a notification ID into this argument. If you use this argument, the method returns the next 250 messages older than the given ID.

```python
older_than="740e5834-3a29-46b4-9a6f-16142fde533a" # optional string - notification ID
```

If you leave out this argument, the method returns the most recent 250 messages.

The client only returns messages sent within the retention period. The default retention period is 7 days. If the message specified in this argument was sent before the retention period, the client returns an empty response.

**include\_jobs (optional)**

Includes notifications sent as part of a batch upload.

If you leave out this argument, the method only returns notifications sent using the API.

**Response**

**One page of up to 250 messages**

If the request to the client is successful, the client returns a `dict`.

```python
{
    "notifications": [
        {
            "id": "740e5834-3a29-46b4-9a6f-16142fde533a",  # required string - notification ID
            "reference": "your reference",  # optional string - reference you provided when sending the message
            "email_address": "amala@example.com",  # required string for emails
            "phone_number": "+447700900123",  # required string for text messages
            "line_1": "Amala Bird",  # required string for letter
            "line_2": "123 High Street",  # required string for letter
            "line_3": "Richmond upon Thames",  # required string for letter
            "line_4": "Middlesex",  # optional string for letter
            "line_5": "SW14 6BF",  # optional string for letter
            "line_6": None,  # optional string for letter
            "line_7": None, # optional string for letter
            "postage": "first / second / economy / europe / rest-of-world", # required string for letter
            "type": "sms / letter / email",  # required string
            "status": "sending / delivered / permanent-failure / temporary-failure / technical-failure",  # required string
            "template": {
                "version": 1, # required integer
                "id": "f33517ff-2a88-4f6e-b855-c550268ce08a",  # required string - template ID
                "uri": "/v2/template/{id}/{version}"  # required string
            },
            "body": "Hi Amala, your appointment is on 1 January 2018 at 1:00pm",  # required string - body of notification
            "subject": "Your upcoming pigeon registration appointment",  # required string for email - subject of email
            "created_at": "2024-05-17 15:58:38.342838",  # required string - date and time notification created
            "created_by_name": "Charlie Smith",  # optional string - name of the person who sent the notification if sent manually
            "sent_at": "2024-05-17 15:58:30.143000",  # optional string - date and time notification sent to provider
            "completed_at": "2024-05-17 15:59:10.321000",  # optional string - date and time notification delivered or failed
            "scheduled_for": "2024-05-17 9:00:00.000000", # optional string - date and time notification has been scheduled to be sent at
            "one_click_unsubscribe": "https://example.com/unsubscribe.html?opaque=123456789", # optional string, email only - URL that you provided so your recipients can unsubscribe
            "is_cost_data_ready": True,  # required boolean, this field is true if cost data is ready, and false if it isn't
            "cost_in_pounds": 0.0027,  # optional number - cost of the notification in pounds. The cost does not take free allowance into account
            "cost_details": {
                # for text messages:
                "billable_sms_fragments": 1,  # optional integer - number of billable sms fragments in your text message
                "international_rate_multiplier": 1,  # optional integer - for international sms rate is multiplied by this value
                "sms_rate": 0.0027,  # optional number - cost of 1 sms fragment
                # for letters:
                "billable_sheets_of_paper": 2,  # optional integer - number of sheets of paper in the letter you sent, that you will be charged for
                "postage": "first / second / economy / europe / rest-of-world"  # optional string
            }
        },
        {
            ...another notification
        }
    ],
    "links": {
        "current": "/notifications?template_type=sms&status=delivered",
        "next": "/notifications?other_than=last_id_in_list&template_type=sms&status=delivered"
    }
}
```

**All messages**

If the request to the client is successful, the client returns an iterator object which yields one notification at a time until it has yielded all your notifications. Each notification is a dict.

```python
<generator object NotificationsAPIClient.get_all_notifications_iterator at 0x1026c7410>
```

For more information, see the:

* [email status](python.md#email-status-descriptions)
* [text message status](python.md#text-message-status-descriptions)
* [letter status](python.md#letter-status-descriptions)
* [precompiled letter status](python.md#precompiled-letter-status-descriptions)

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_multi_message_data.md" %}

### Email status descriptions

{% include "../.gitbook/includes/status_desc_email.md" %}

### Text message status descriptions

{% include "../.gitbook/includes/status_desc_sms.md" %}

### Letter status descriptions

{% include "../.gitbook/includes/status_desc_letter.md" %}

### Precompiled letter status descriptions

{% include "../.gitbook/includes/status_desc_precompiled.md" %}

### Get a PDF for a letter notification

**Method**

This returns the PDF contents of a letter.

```python
pdf_file = notifications_client.get_pdf_for_letter(
  "3d1ce039-5476-414c-99b2-fac1e6add62c" # required string - notification ID
)
```

**Arguments**

**notification\_id (required)**

The ID of the notification. To find the notification ID, you can either:

* check the response to the [original notification method call](python.md#get-the-data-for-one-message)
* [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page

**Response**

If the request to the client is successful, the client will return a `io.BytesIO` object containing the raw PDF data.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_pdf_for_letter.md" %}

## Get a template

### Get a template by ID

**Method**

This returns the latest version of the template.

```python
response = notifications_client.get_template(
  template_id="f33517ff-2a88-4f6e-b855-c550268ce08a"
)
```

**Arguments**

**template\_id (required)**

The ID of the template. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **Templates** page to find it.

For example:

```python
template_id="f33517ff-2a88-4f6e-b855-c550268ce08a", # required UUID string
```

**Response**

If the request to the client is successful, the client returns a `dict`.

```python
{
    "id": "f33517ff-2a88-4f6e-b855-c550268ce08a", # required string - template ID
    "name": "Pigeon registration - appointment email", # required string - template name
    "type": "sms / email / letter" , # required string
    "created_at": "2024-05-10 10:30:31.142535", # required string - date and time template created
    "updated_at": "2024-08-25 13:00:09.123234", # required string - date and time template last updated
    "version": 2, # required integer - template version
    "created_by": "charlie.smith@pigeons.gov.uk", # required string
    "subject": "Your upcoming pigeon registration appointment",  # required string for email and letter - subject of email / heading of letter
    "body": "Dear ((first_name))\r\n\r\nYour pigeon registration appointment is scheduled for ((appointment_date)).\r\n\r\nPlease bring:\r\n\n\n((required_documents))\r\n\r\nYours,\r\nPigeon Affairs Bureau",  # required string - body of notification
    "letter_contact_block": "Pigeons Affairs Bureau\n10 Whitechapel High Street\nLondon\nE1 8EF" # optional string - present for letter templates where contact block is set, otherwise None
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_get_template_by_id.md" %}

### Get a template by ID and version

**Method**

```python
response = notifications_client.get_template_version(
    template_id="f33517ff-2a88-4f6e-b855-c550268ce08a",
    version=1,
)
```

**Arguments**

**template\_id (required)**

The ID of the template. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **Templates** page to find it.

For example:

```python
template_id="f33517ff-2a88-4f6e-b855-c550268ce08a", # required UUID string
```

**version (required)**

The version number of the template.

**Response**

If the request to the client is successful, the client returns a `dict`.

```python
{
    "id": "f33517ff-2a88-4f6e-b855-c550268ce08a", # required string - template ID
    "name": "Pigeon registration - appointment email", # required string - template name
    "type": "sms / email / letter" , # required string
    "created_at": "2024-05-10 10:30:31.142535", # required string - date and time template created
    "updated_at": "2024-08-25 13:00:09.123234", # required string - date and time template last updated
    "version": 1, # required integer - template version
    "created_by": "charlie.smith@pigeons.gov.uk", # required string
    "subject": "Your upcoming pigeon registration appointment",  # required string for email and letter - subject of email / heading of letter
    "body": "Dear ((first_name))\r\n\r\nYour pigeon registration appointment is scheduled for ((appointment_date)).\r\n\r\nPlease bring:\r\n\n\n((required_documents))\r\n\r\nYours,\r\nPigeon Affairs Bureau",  # required string - body of notification
    "letter_contact_block": "Pigeons Affairs Bureau\n10 Whitechapel High Street\nLondon\nE1 8EF" # optional string - present for letter templates where contact block is set, otherwise None
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_get_template_by_id_and_version.md" %}

### Get all templates

**Method**

This returns the latest version of all templates.

```python
response = notifications_client.get_all_templates(
    template_type="sms / letter / email" # optional string
)
```

**Arguments**

**template\_type (optional)**

If you leave out this argument, the method returns all templates. Otherwise you can filter by:

* `email`
* `sms`
* `letter`

**Response**

If the request to the client is successful, the client returns a `dict`.

```python
{
    "templates": [
        {
            "id": "f33517ff-2a88-4f6e-b855-c550268ce08a", # required string - template ID
            "name": "Pigeon registration - appointment email", # required string - template name
            "type": "sms / email / letter" , # required string
            "created_at": "2024-05-10 10:30:31.142535", # required string - date and time template created
            "updated_at": "2024-08-25 13:00:09.123234", # required string - date and time template last updated
            "version": 2, # required integer - template version
            "created_by": "charlie.smith@pigeons.gov.uk", # required string
            "subject": "Your upcoming pigeon registration appointment",  # required string for email and letter - subject of email / heading of letter
            "body": "Dear ((first_name))\r\n\r\nYour pigeon registration appointment is scheduled for ((appointment_date)).\r\n\r\nPlease bring:\r\n\n\n((required_documents))\r\n\r\nYours,\r\nPigeon Affairs Bureau",  # required string - body of notification
            "letter_contact_block": "Pigeons Affairs Bureau\n10 Whitechapel High Street\nLondon\nE1 8EF" # optional string - present for letter templates where contact block is set, otherwise None
        },
        {
            ...another template
        }
    ]
}
```

If no templates exist for a template type or there no templates for a service, the client returns a `dict` with an empty `templates` list element:

```python
{
    "templates": []
}
```

### Generate a preview template

**Method**

This generates a preview version of a template.

```python
response = notifications_client.post_template_preview(
    template_id="f33517ff-2a88-4f6e-b855-c550268ce08a",
    personalisation={
        "first_name": "Amala",
        "appointment_date": "1 January 2018 at 1:00pm",
    }
)
```

The parameters in the personalisation argument must match the placeholder fields in the actual template. The API notification client will ignore any extra fields in the method.

**Arguments**

**template\_id (required)**

The ID of the template. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **Templates** page to find it.

For example:

```python
template_id="f33517ff-2a88-4f6e-b855-c550268ce08a", # required UUID string
```

**personalisation (required)**

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
    "first_name": "Amala",
    "appointment_date": "1 January 2018 at 1:00pm",
    "required_documents": ["passport", "utility bill", "other id"],
},
```

**Response**

If the request to the client is successful, you receive a `dict` response.

```python
{
    "id": "740e5834-3a29-46b4-9a6f-16142fde533a", # required string - notification ID
    "type": "sms / email / letter" , # required string
    "version": 3,
    # required string - body of notification
    "body": "Dear Amala\r\n\r\nYour pigeon registration appointment is scheduled for 1 January 2018 at 1:00pm.\r\n\r\n Here is a link to your invitation document:\r\n\n\n* passport\n* utility bill\n* other id\r\n\r\nPlease bring the invite with you to the appointment.\r\n\r\nYours,\r\nPigeon Affairs Bureau",
    # required string for emails, empty for sms and letters - html version of the email body
    "html": '<p style="Margin: 0 0 20px 0; font-size: 19px; line-height: 25px; color: #0B0C0C;">Dear Amala</p> ... [snippet truncated for readability]',
    # required string for email and letter - subject of email / heading of letter
    "subject": 'Your upcoming pigeon registration appointment',
    'postage': None, # required string for letters, empty for sms and emails - letter postage
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

{% include "../.gitbook/includes/error_codes_preview_template.md" %}

## Get received text messages

This API call returns one page of up to 250 received text messages. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the older\_than argument.

You can only get the data for messages that are 7 days old or newer.

### Enable received text messages

To receive text messages:

1. Go to the **Text message settings** section of the **Settings** page.
2. Select **Change** on the **Receive text messages** row.

### Get all received text messages

This will return a Python iterator object which yields one received text message at a time until it has yielded all your received text messages.

**Method**

```python
response = notifications_client.get_received_texts_iterator()

# to iterate and process the received text messages using the iterator:
for text_message in response:
    # process received text message
```

**Response**

If the request to the client is successful, the client will return a `<generator object>` that will return all received text messages.

```python
<generator object NotificationsAPIClient.get_received_texts_iterator at 0x1026c7410>
```

### Get a page of received text messages

This will return one page of up to 250 text messages.

**Method**

```python
response = notifications_client.get_received_texts(older_than="740e5834-3a29-46b4-9a6f-16142fde533a")
```

You can specify which text messages to receive by inputting the ID of a received text message into the [`older_than`](https://docs.notifications.service.gov.uk/python.html#get-a-page-of-received-text-messages-arguments-older-than-optional) argument.

**Arguments**

**older\_than (optional)**

Input the ID of a received text message into this argument. If you use this argument, the method returns the next 250 received text messages older than the given ID.

```python
older_than="740e5834-3a29-46b4-9a6f-16142fde533a" # optional string - notification ID
```

If you leave out this argument, the method returns the most recent 250 text messages.

**Response**

If the request to the client is successful, the client returns a `dict`.

```python
{
  "received_text_messages":
  [
    {
      "id": "'b51f638b-4295-46e0-a06e-cd41eee7c33b", # required string - ID of received text message
      "user_number": "447700900123", # required string - number of the end user who sent the message
      "notify_number": "07700900456", # required string - your receiving number
      "created_at": "2024-12-12 18:39:16.123346", # required string - date and time template created
      "service_id": "26785a09-ab16-4eb0-8407-a37497a57506", # required string - service ID
      "content": "STRING" # required string - text content
    },
    {
      ...another received text message
    }
  ],
  "links": {
    "current": "/received-text-messages",
    "next": "/received-text-messages?other_than=last_id_in_list"
  }
}
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to [Errors section](python.md#errors).

There are no errors specific to this endpoint, but you may encounter:

* [various schema validation errors](python.md#schema-validation-errors), for example when you forget to pass in an argument, or pass in an argument of a wrong type.
* errors that are not related to generating a preview template, but instead are related to things like authentication and rate limits. You can find a list of these errors [in General errors](python.md#general-errors)

## Errors

### Error handling

If the request is not successful, the client raises an `HTTPError`.

This error consists of:

* a status code, for example `400`
* an error type, for example `BadRequestError`
* a message, for example `Mobile numbers can only include: 0 1 2 3 4 5 6 7 8 9 ( ) + -`

To access these details about the error, read its `status_code` and `message` fields, for example:

```python
from notifications_python_client.errors import HTTPError

try:
  response = notifications_client.send_sms_notification(
    phone_number="+447700900123",
    template_id="f33517ff-2a88-4f6e-b855-c550268ce08a",
  )
except HTTPError as e:
  if e.status_code in range(500, 599):  # using status code for error handling
    self.retry()
  else:
    print(e.message)  # accessing error details to debug

```

Error’s `message` field is a `dict`, for example:

```python
[{
  "error": "AuthError",
  "message": "Invalid token: API key not found",
}]
```

Do not use the content of the messages in your code. These can sometimes change, which may affect your API integration.

Use the status code or the error type instead, as these will not change.

### General errors

{% include "../.gitbook/includes/general_errors.md" %}

### Schema validation errors

{% include "../.gitbook/includes/schema_validation_errors.md" %}

### Endpoint-specific errors

In addition to the above, you may also encounter endpoint-specific errors, which are listed under each relevant API endpoint section.

Find references for endpoint-specific errors in:

* [send a message](python.md#send-a-message)
* [get message data](python.md#get-message-data)
* [get a template](python.md#get-a-template)
* [get received text messages](python.md#get-received-text-messages)

{% include "../.gitbook/includes/footer_links.md" %}
