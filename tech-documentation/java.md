---
description: >-
  This documentation is for developers interested in using the NotifyNL Java
  client to send emails, text messages or letters.
icon: java
---

# Java client documentation

## Set up the client

### Install the client

The `notifications-java-client` deploys to Maven Central.

Go to the [Notify Java client page on Maven Central](https://search.maven.org/artifact/uk.gov.service.notify/notifications-java-client):

1. Select the most recent version.
2. Copy the dependency configuration snippet for your build tool.

Refer to the [client changelog](https://github.com/alphagov/notifications-java-client/blob/main/CHANGELOG.md) for the version number and the latest updates.

### Create a new instance of the client

Add this code to your application:

```java
import uk.gov.service.notify.NotificationClient;
NotificationClient client = new NotificationClient(apiKey, "https://api.notifynl.nl");
```

To get an API key, [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page. You can find more information in the API keys section of this documentation.

**Connect through a proxy (optional)**

If you use a proxy you can pass it into the NotificationClient constructor.

```java
import uk.gov.service.notify.NotificationClient;
NotificationClient client = new NotificationClient(apiKey, "https://api.notifynl.nl", proxy);
```

## Send a message

You can use NotifyNL to send emails, text messages and letters.

### Bulk sending

You cannot use a single API call to send messages in bulk.

To send a batch of messages, use the API to loop over your recipient list, sending one message at a time to each recipient.

Make sure you do not exceed our rate limits.

### Send a text message

**Method**

```java
SendSmsResponse response = client.sendSms(
    templateId,
    phoneNumber,
    personalisation,
    reference,
    smsSenderId
);
```

#### **Arguments**

**templateId (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```java
String templateId="f33517ff-2a88-4f6e-b855-c550268ce08a";
```

**phoneNumber (required)**

The phone number of the recipient of the text message. This number can be a NL or international number.

```java
String phoneNumber="+3112345678";
```

**personalisation (required)**

If a template has placeholder fields for personalised information such as name or reference number, you must provide their values in a map. For example:

```java
Map<String, Object> personalisation = new HashMap<>();
personalisation.put("first_name", "Amala");
personalisation.put("application_date", "2018-01-01");
personalisation.put("list", listOfItems); // Will appear as a comma separated list in the message
```

If a template does not have any placeholder fields for personalised information, you must pass in an empty map or `null`.

**reference (required)**

A unique identifier you create. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. If you do not have a reference, you must pass in an empty string or `null`.

```java
String reference='STRING';
```

**smsSenderId (optional)**

A unique identifier of the sender of the text message notification.

To find the text message sender:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Text Messages** section, select **Manage** on the **Text Message sender** row.

You can then either:

* copy the sender ID that you want to use and paste it into the method
* select **Change** to change the default sender that the service will use, and select **Save**

```java
String smsSenderId='8e222534-7f05-4972-86e3-17c5d9f894e2'
```

You can leave out this argument if your service only has one text message sender, or if you want to use the default sender.

#### **Response**

If the request to the client is successful, the client returns a `SendSmsResponse`:

```java
UUID notificationId;
Optional<String> reference;
UUID templateId;
int templateVersion;
String templateUri;
String body;
Optional<String> fromNumber;
```

If you are using the test API key, all your messages come back with a `delivered` status.

All messages sent using the team and guest list or live keys appear on your dashboard.

#### **Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_sms.md" %}

### Send an email

**Method**

```java
SendEmailResponse response = client.sendEmail(
    templateId,
    emailAddress,
    personalisation,
    reference,
    emailReplyToId
);
```

**Arguments**

**templateId (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```java
String templateId="f33517ff-2a88-4f6e-b855-c550268ce08a";
```

**emailAddress (required)**

The email address of the recipient.

```java
String emailAddress='sender@something.com';
```

**personalisation (required)**

If a template has placeholder fields for personalised information such as name or application date, you must provide their values in a map. For example:

```java
Map<String, Object> personalisation = new HashMap<>();
personalisation.put("first_name", "Amala");
personalisation.put("application_date", "2018-01-01");
// pass in a list and it will appear as bullet points in the message:
personalisation.put("list", listOfItems);

```

If a template does not have any placeholder fields for personalised information, you must pass in an empty map or `null`.

Find out how to reduce the risk of malicious content injection in placeholders.

**reference (required)**

A unique identifier you create. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. If you do not have a reference, you must pass in an empty string or `null`.

```java
String reference='STRING';
```

**oneClickUnsubscribeURL (recommended)**

If you send subscription emails you must let recipients opt out of receiving them. Read our Using Notify page for more information about [unsubscribe links](https://admin.notifynl.nl/using-notify/unsubscribe-links).

The one-click unsubscribe URL will be added to the headers of your email. Email clients will use it to add an unsubscribe button.

```java
URI oneClickUnsubscribeURL = 'https://example.com/unsubscribe.html?opaque=123456789'
```

The one-click unsubscribe URL must respond to an empty `POST` request by unsubscribing the user from your emails. You can include query parameters to help you identify the user.

Your unsubscribe URL and response must comply with the guidance specified in [Section 3.1 of IETF RFC 8058](https://www.rfcreader.com/#rfc8058_line139).

You can leave out this argument if the email being sent is not a subscription email.

You must also add an unsubscribe link to the bottom of your email. The unsubscribe link at the bottom of your email should take the email recipient to a webpage where they can confirm that they want to unsubscribe.

Find out how to add a link when you create a **New template** or **Edit** an email template.

**emailReplyToId (optional)**

This is an email address specified by you to receive replies from your users. You must add at least one reply-to email address before your service can go live.

To add a reply-to email address:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Email** section, select **Manage** on the **Reply-to email addresses** row.
4. Select **Add reply-to address**.
5. Enter the email address you want to use, and select **Add**.

```java
String emailReplyToId='8e222534-7f05-4972-86e3-17c5d9f894e2'
```

You can leave out this argument if your service only has one reply-to email address, or you want to use the default email address.

**Response**

If the request to the client is successful, the client returns a `SendEmailResponse`:

```java
UUID notificationId;
Optional<String> reference;
Optional<URI> oneClickUnsubscribeURL;
UUID templateId;
int templateVersion;
String templateUri;
String body;
String subject;
Optional<String> fromEmail;
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

```json
{name: "Anne Example, now [click this evil link](https://malicious.link)"}
```

**Email will appear as**:

```
 Dear Anne Example, now click this evil link
```

We recommend you sanitise all input from untrusted sources to prevent the injection of malicious content. You can use a backslash to escape [individual characters](https://www.markdownguide.org/basic-syntax/#characters-you-can-escape). The characters of most concern are those that could be used to add a URL link such as `[`, `]`, `(` or `)`.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_email.md" %}

### Send a file by email

To send a file by email, add a placeholder to the template then upload a file. The placeholder will contain a secure link to download the file.

The links are unique and unguessable. NotifyNL cannot access or decrypt your file.

Your file will be available to download for a default period of 26 weeks (6 months).

To help protect your files you can also:

* ask recipients to confirm their email address before downloading
* choose the length of time that a file is available to download

#### **Add contact details to the file download page**

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Email** section, select **Manage** on the **Send files by email** row.
4. Enter the contact details you want to use, and select **Save**.

#### **Add a placeholder to the template**

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant email template.
3. Select **Edit**.
4. Add a placeholder to the email template using double brackets. For example: "Download your file at: ((link\_to\_file))"

Your email should also tell recipients how long the file will be available to download.

#### **Upload your file**

You can upload the following files types:

* CSV (.csv)
* image (.jpeg, .jpg, .png)
* Microsoft Excel Spreadsheet (.xlsx)
* Microsoft Word Document (.doc, .docx)
* PDF (.pdf)
* text (.json, .odt, .rtf, .txt)

Your file must be smaller than 2MB. [Contact the NotifyNL team](https://admin.notifynl.nl/support) if you need to send other file types.

1. Convert the PDF to a `byte[]`.
2. Pass the `byte[]` to the personalisation argument.
3. Call the sendEmail method.

For example:

```java
ClassLoader classLoader = getClass().getClassLoader();
File file = new File(classLoader.getResource("document_to_upload.pdf").getFile());
byte [] fileContents = FileUtils.readFileToByteArray(file);

HashMap<String, Object> personalisation = new HashMap();
personalisation.put("link_to_file", client.prepareUpload(fileContents));
client.sendEmail(templateId,
                 emailAddress,
                 personalisation,
                 reference,
                 emailReplyToId);
```

#### **Set the filename**

To do this you will need version 5.0.0-RELEASE of the Java client library, or a more recent version.

You should provide a filename when you upload your file.

The filename should tell the recipient what the file contains. A memorable filename can help the recipient to find the file again later.

The filename must end with a file extension. For example, `.csv` for a CSV file. If you include the wrong file extension, recipients may not be able to open your file.

If you do not provide a filename for your file, Notify will:

* generate a random filename
* try to add the correct file extension

If Notify cannot add the correct file extension, recipients may not be able to open your file.

```java
ClassLoader classLoader = getClass().getClassLoader();
File file = new File(classLoader.getResource("document_to_upload.pdf").getFile());
byte [] fileContents = FileUtils.readFileToByteArray(file);

HashMap<String, Object> personalisation = new HashMap();
personalisation.put("link_to_file", client.prepareUpload(fileContents, "report.csv"));
client.sendEmail(templateId,
                 emailAddress,
                 personalisation,
                 reference,
                 emailReplyToId);
```

#### **Ask recipients to confirm their email address before they can download the file**

When a recipient clicks the link in the email you’ve sent them, they have to enter their email address. Only someone who knows the recipient’s email address can download the file.

This security feature is turned on by default.

#### **Turn off email address check (not recommended)**

If you do not want to use this feature, you can turn it off on a file-by-file basis.

To do this you will need version 3.19.0-RELEASE of the Java client library, or a more recent version.

You should not turn this feature off if you send files that contain:

* personally identifiable information
* commercially sensitive information
* information classified as ‘OFFICIAL’ or ‘OFFICIAL-SENSITIVE’ under the [Government Security Classifications](https://www.gov.uk/government/publications/government-security-classifications) policy

To let the recipient download the file without confirming their email address, set the `confirmEmailBeforeDownload` flag to `false`.

```java
ClassLoader classLoader = getClass().getClassLoader();
File file = new File(classLoader.getResource("document_to_upload.pdf").getFile());
byte [] fileContents = FileUtils.readFileToByteArray(file);

HashMap<String, Object> personalisation = new HashMap();
personalisation.put("link_to_file", client.prepareUpload(fileContents, false, false, "52 weeks"));
client.sendEmail(templateId,
                 emailAddress,
                 personalisation,
                 reference,
                 emailReplyToId);
```

**Choose the length of time that a file is available to download**

Set the number of weeks you want the file to be available using the `retention_period` parameter.

To use this feature you will need 3.19.0-RELEASE of the Java client library, or a more recent version.

You can choose any value between 1 week and 78 weeks. When deciding this, you should consider:

* the need to protect the recipient’s personal information
* whether the recipient will need to download the file again later

If you do not choose a value, the file will be available for the default period of 26 weeks (6 months).

Files sent before 12 April 2023 had a longer default period of 78 weeks (18 months).

```java
ClassLoader classLoader = getClass().getClassLoader();
File file = new File(classLoader.getResource("document_to_upload.pdf").getFile());
byte [] fileContents = FileUtils.readFileToByteArray(file);

HashMap<String, Object> personalisation = new HashMap();
personalisation.put("link_to_file",
                    client.prepareUpload(
                            fileContents,
                            false,
                            false,
                            new RetentionPeriodDuration(52, ChronoUnit.WEEKS)
                    ));
client.sendEmail(templateId,
                 emailAddress,
                 personalisation,
                 reference,
                 emailReplyToId);
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_send_file_email.md" %}

### Send a letter

When you add a new service it will start in [trial mode](../using-notifynl/using-notifynl/trial-mode.md). You can only send letters when your service is live.

To send Notify a request to go live:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Settings** page.
3. In the **Your service is in trial mode** section, select **request to go live**.

**Method**

```java
SendLetterResponse response = client.sendLetter(
    templateId,
    personalisation,
    reference
);
```

**Arguments**

**templateId (required)**

To find the template ID:

1. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in).
2. Go to the **Templates** page and select the relevant template.
3. Select **Copy template ID to clipboard**.

For example:

```java
String templateId = "f33517ff-2a88-4f6e-b855-c550268ce08a";
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

Any other placeholder fields included in the letter template also count as required parameters. You must provide their values in a map. For example:

```java
Map<String, Object> personalisation = new HashMap<>();
personalisation.put("address_line_1", "The Occupier"); // mandatory address field
personalisation.put("address_line_2", "Flat 2"); // mandatory address field
personalisation.put("address_line_3", "SW14 6BH"); // mandatory address field, must be a real UK postcode
personalisation.put("first_name", "Amala"); // field from template
personalisation.put("application_date", "2018-01-01"); // field from template
// pass in a list and it will appear as bullet points in the letter:
personalisation.put("list", listOfItems);
```

If a template does not have any placeholder fields for personalised information, you must pass in an empty map or `null`.

**reference (required)**

A unique identifier you create. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address. If you do not have a reference, you must pass in an empty string or `null`.

```java
String reference='STRING';
```

**Response**

If the request to the client is successful, the client returns a `SendLetterResponse`:

```java
UUID notificationId;
Optional<String> reference;
UUID templateId;
int templateVersion;
String templateUri;
String body;
String subject;
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_letter.md" %}

### Send a precompiled letter

**Method**

```java
LetterResponse response = client.sendPrecompiledLetter(
    reference,
    precompiledPDFAsFile
    );
```

```java
LetterResponse response = client.sendPrecompiledLetterWithInputStream(
    reference,
    precompiledPDFAsInputStream
    );
```

```java
LetterResponse response = client.sendPrecompiledLetter(
    reference,
    precompiledPDFAsFile,
    postage
    );
```

```java
LetterResponse response = client.sendPrecompiledLetterWithInputStream(
    reference,
    precompiledPDFAsInputStream,
    postage
    );
```

**Arguments**

**reference (required)**

A unique identifier you create. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address.

```java
String reference="STRING";
```

**precompiledPDFAsFile (required for the sendPrecompiledLetter method)**

The precompiled letter must be a PDF file which meets [the NotifyNL letter specification](https://admin.notifynl.nl/using-notify/guidance/letter-specification).

This argument adds the precompiled letter PDF file to a Java file object. The method sends this Java file object to NotifyNL.

```java
File precompiledPDF = new File("<PDF file path>");
```

**precompiledPDFAsInputStream (required for the sendPrecompiledLetterWithInputStream method)**

The precompiled letter must be an InputStream. This argument adds the precompiled letter PDF content to a Java InputStream object. The method sends this InputStream to NotifyNL.

```java
InputStream precompiledPDFAsInputStream = new FileInputStream(pdfContent);
```

**postage (optional)**

You can choose first class, second class or economy mail postage for your precompiled letter. Set the value to `first` for first class, `second` for second class or `economy` for economy mail. If you do not pass in this argument, the postage will default to second class.

**Response**

If the request to the client is successful, the client returns a `LetterResponse`:

```java
UUID notificationId;
String reference;
String postage
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_precompiled.md" %}

## Get message data

### Get the data for one message

You can only get the data for messages sent within the retention period. The default retention period is 7 days.

**Method**

```java
Notification notification = client.getNotificationById(notificationId);
```

**Arguments**

**notificationId (required)**

The ID of the notification. To find the notification ID, you can either:

* check the response to the original notification method call
* [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page

**Response**

If the request to the client is successful, the client returns a `Notification`:

```java
UUID id;
Optional<String> reference;
Optional<String> emailAddress;
Optional<String> phoneNumber;
Optional<String> line1;
Optional<String> line2;
Optional<String> line3;
Optional<String> line4;
Optional<String> line5;
Optional<String> line6;
Optional<String> line7;
Optional<String> postage;
String notificationType;
String status;
UUID templateId;
int templateVersion;
String templateUri;
String body;
Optional<String> subject;
ZonedDateTime createdAt;
Optional<ZonedDateTime> sentAt;
Optional<ZonedDateTime> completedAt;
Optional<ZonedDateTime> estimatedDelivery;
Optional<String> createdByName;
boolean isCostDataReady;
double costInPounds;
Optional<Integer> billableSmsFragments;
Optional<Double> internationalRateMultiplier;
Optional<Double> smsRate;
Optional<Integer> billableSheetsOfPaper;
Optional<String> postageType;
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_message_data.md" %}

### Get the data for multiple messages

This API call returns one page with data for up to 250 messages. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the `olderThanId` argument.

You can only get messages that are within your data retention period. The default data retention period is 7 days. It can be changed in your Service Settings.

**Method**

```java
NotificationList notification = client.getNotifications(
    status,
    notificationType,
    reference,
    olderThanId
);
```

To get the most recent messages, you must pass in an empty `olderThanId` argument or `null`.

To get older messages, pass the ID of an older notification into the `olderThanId` argument. This returns the next oldest messages from the specified notification ID.

**Arguments**

You can pass in empty arguments or `null` to ignore these filters.

**status (optional)**

You can filter by each:

* email status
* text message status
* letter status
* precompiled letter status

You can leave out this argument to ignore this filter.

**notificationType (optional)**

You can filter by:

* `email`
* `sms`
* `letter`

**reference (optional)**

A unique identifier you create if necessary. This reference identifies a single unique notification or a batch of notifications. It must not contain any personal information such as name or postal address.

```java
String reference='STRING';
```

**olderThanId (optional)**

Input the ID of a notification into this argument. If you use this argument, the client returns the next 250 received notifications older than the given ID.

```java
String olderThanId='8e222534-7f05-4972-86e3-17c5d9f894e2'
```

If you pass in an empty argument or `null`, the client returns the most recent 250 notifications.

**Response**

If the request to the client is successful, the client returns a `NotificationList`:

```java
List<Notification> notifications;
String currentPageLink;
Optional<String> nextPageLink;
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

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

This returns the PDF contents of a letter notification.

```java
byte[] pdfFile = client.getPdfForLetter(notificationId)
```

**Arguments**

**notificationId (required)**

The ID of the notification. To find the notification ID, you can either:

* check the response to the original notification method call
* [sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **API integration** page

**Response**

If the request to the client is successful, the client will return a `byte[]` object containing the raw PDF data.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_pdf_for_letter.md" %}

## Get a template

### Get a template by ID

**Method**

This returns the latest version of the template.

```java
Template template = client.getTemplateById(templateId);
```

**Arguments**

**templateId (required)**

The ID of the template. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **Templates** page to find it.

```
String templateId='f33517ff-2a88-4f6e-b855-c550268ce08a';
```

**Response**

If the request to the client is successful, the client returns a `Template`:

```java
UUID id;
String name;
String templateType;
ZonedDateTime createdAt;
Optional<ZonedDateTime> updatedAt;
String createdBy;
int version;
String body;
Optional<String> subject;
Optional<Map<String, Object>> personalisation;
Optional<String> letterContactBlock;
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_get_template_by_id.md" %}

### Get a template by ID and version

**Method**

```java
Template template = client.getTemplateVersion(templateId, version);
```

**Arguments**

**templateId (required)**

The ID of the template. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **Templates** page to find it.

```java
String templateId='f33517ff-2a88-4f6e-b855-c550268ce08a';
```

**version (required)**

The version number of the template.

**Response**

If the request to the client is successful, the client returns a `Template`:

```java
UUID id;
String name;
String templateType;
ZonedDateTime createdAt;
Optional<ZonedDateTime> updatedAt;
String createdBy;
int version;
String body;
Optional<String> subject;
Optional<Map<String, Object>> personalisation;
Optional<String> letterContactBlock;
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_get_template_by_id_and_version.md" %}

### Get all templates

**Method**

This returns the latest version of all templates.

```java
TemplateList templates = client.getAllTemplates(templateType);
```

**Arguments**

**templateType (optional)**

If you do not use `templateType`, the client returns all templates. Otherwise you can filter by:

* `email`
* `sms`
* `letter`

**Response**

If the request to the client is successful, the client returns a `TemplateList`:

```java
List<Template> templates;
```

If no templates exist for a template type or there no templates for a service, the templates list is empty.

### Generate a preview template

**Method**

This generates a preview version of a template.

```java
TemplatePreview templatePreview = client.generateTemplatePreview(
    templateId,
    personalisation
);
```

The parameters in the personalisation argument must match the placeholder fields in the actual template. The API notification client ignores any extra fields in the method.

**Arguments**

**templateId (required)**

The ID of the template. [Sign in to NotifyNL](https://admin.notifynl.nl/sign-in) and go to the **Templates** page to find it.

```java
String templateId='f33517ff-2a88-4f6e-b855-c550268ce08a';
```

**personalisation (required)**

If a template has placeholder fields for personalised information such as name or application date, you must provide their values in a map. For example:

```java
Map<String, Object> personalisation = new HashMap<>();
personalisation.put("first_name", "Amala");
personalisation.put("application_date", "2018-01-01");
```

If a template does not have any placeholder fields for personalised information, you must pass in an empty map or `null`.

**Response**

If the request to the client is successful, the client returns a `TemplatePreview`:

```java
UUID id;
String templateType;
int version;
String body;
Optional<String> subject;
Optional<String> html;
```

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

{% include "../.gitbook/includes/error_codes_preview_template.md" %}

## Get received text messages

This API call returns one page of up to 250 received text messages. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the `olderThanId` argument.

You can only get messages that are 7 days old or newer.

You can also set up callbacks for received text messages.

### Enable received text messages

To receive text messages:

1. Go to the **Text message settings** section of the **Settings** page.
2. Select **Change** on the **Receive text messages** row.

### Get a page of received text messages

**Method**

```java
ReceivedTextMessageList response = client.getReceivedTextMessages(olderThanId);
```

To get the most recent messages, you must pass in an empty argument or `null`.

To get older messages, pass the ID of an older notification into the `olderThanId` argument. This returns the next oldest messages from the specified notification ID.

**Arguments**

**olderThanId (optional)**

Input the ID of a received text message into this argument. If you use this argument, the client returns the next 250 received text messages older than the given ID.

```java
String olderThanId='8e222534-7f05-4972-86e3-17c5d9f894e2'
```

If you pass in an empty argument or `null`, the client returns the most recent 250 text messages.

**Response**

If the request to the client is successful, the client returns a `ReceivedTextMessageList` that returns all received texts.

```java
private List<ReceivedTextMessage> receivedTextMessages;
private String currentPageLink;
private String nextPageLink;
```

The `ReceivedTextMessageList` has the following properties:

```java
private UUID id;
private String notifyNumber;
private String userNumber;
private UUID serviceId;
private String content;
private ZonedDateTime createdAt;
```

If the notification specified in the `olderThanId` argument is older than 7 days, the client returns an empty response.

**Error codes**

If the request is not successful, the client returns an error. To learn more about error structure, go to Errors section.

## Errors

### Error handling

If the request is not successful, the client raises an `NotificationClientException`.

This error consists of:

* a status code, for example `400`
* an error type, for example `BadRequestError`
* a message, for example `Mobile numbers can only include: 0 1 2 3 4 5 6 7 8 9 ( ) + -`

To access these details about the exception, read its `getHttpResult()` and `getMessage()` methods, for example:

```java
    SendSmsResponse response;
    try {
        response = client.sendSms(
            templateId,
            phoneNumber,
            personalisation,
            reference,
            smsSenderId
        );
        System.out.println(response);
    } catch (NotificationClientException e) {
        if (e.getHttpResult() >= 500) {
            //retry logic
        } else {
            System.out.printf(e.getMessage());
        }
    }
```

Exception's `getMessage()` method returns a `String`, for example:

```java
Status code: 403 {"errors":[{"error":"AuthError","message":"Invalid token: API key not found"}],"status_code":403}
```

### General errors

{% include "../.gitbook/includes/general_errors.md" %}

### Schema validation errors

{% include "../.gitbook/includes/schema_validation_errors.md" %}

### Endpoint-specific errors

In addition to the above, you may also encounter endpoint-specific errors, which are listed under each relevant API endpoint section.

Find references for endpoint-specific errors in:

* [send a message](java.md#send-a-message)
* [get message data](java.md#get-message-data)
* [get a template](java.md#get-a-template)
* [get received text messages](java.md#get-received-text-messages)

{% include "../.gitbook/includes/footer_links.md" %}
