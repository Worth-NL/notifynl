# Message status

Use the Dashboard to check the status of a message.

This page describes the statuses you’ll see when you’re signed-in to NotifyNL.

If you’re using the Notify API, read our [documentation](api-documentation.md) for a list of API statuses.

{% tabs %}
{% tab title="Emails" %}
| Status                                 | Description                                                                                                                                                                                                                                                                                                                                              |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Delivering                             | Notify has sent the message to the provider. The provider will try to deliver the message to the recipient for up to 72 hours. Notify is waiting for delivery information.                                                                                                                                                                               |
| Delivered                              | The message was successfully delivered. Notify will not tell you if a user has opened or read a message.                                                                                                                                                                                                                                                 |
| Email address does not exist           | The provider could not deliver the message because the email address was wrong. You should remove these email addresses from your database.                                                                                                                                                                                                              |
| Inbox not accepting messages right now | The provider could not deliver the message. This can happen when the recipient’s inbox is full or their anti-spam filter rejects your email. [Check your content does not look like spam](https://www.gov.uk/service-manual/design/sending-emails-and-text-messages#protect-your-users-from-spam-and-phishing) before you try to send the message again. |
| Technical failure                      | Your message was not sent because there was a problem between Notify and the provider. You’ll have to try sending your messages again.                                                                                                                                                                                                                   |
{% endtab %}

{% tab title="Text messages" %}
| Status                                 | Description                                                                                                                                                                                                                                                                                                                                                                       |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Delivering                             | Notify has sent the message to the provider. The provider will try to deliver the message to the recipient for up to 72 hours. Notify is waiting for delivery information.                                                                                                                                                                                                        |
| Sent to an international number        | The mobile networks in some countries do not provide any more delivery information.                                                                                                                                                                                                                                                                                               |
| Delivered                              | The message was successfully delivered. Notify will not tell you if a user has opened or read a message.                                                                                                                                                                                                                                                                          |
| Not delivered                          | The provider could not deliver the message. This can happen if the phone number was wrong or if the network operator rejects the message. If you’re sure that these phone numbers are correct, you should [contact us](https://admin.notifynl.nl/support). If not, you should remove them from your database. You’ll still be charged for text messages that cannot be delivered. |
| Phone not accepting messages right now | The provider could not deliver the message. This can happen when the recipient’s phone is off, has no signal, or their text message inbox is full. You can try to send the message again. You’ll still be charged for text messages to phones that are not accepting messages.                                                                                                    |
| Technical failure                      | Your message was not sent because there was a problem between Notify and the provider. You’ll have to try sending your messages again. You will not be charged for text messages that are affected by a technical failure.                                                                                                                                                        |
{% endtab %}

{% tab title="Letters" %}
| Status            | Description                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------------- |
| Sent              | Notify has sent the letter to the provider to be printed.                                               |
| Printed           | The provider has printed the letter. Letters are printed at 5:30pm and dispatched the next working day. |
| Cancelled         | Sending cancelled. Your letter will not be printed or dispatched.                                       |
| Technical failure | Notify had an unexpected error while sending the letter to our printing provider.                       |
| Permanent failure | The provider cannot print the letter. Your letter will not be dispatched.                               |
{% endtab %}
{% endtabs %}
