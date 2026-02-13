# Personalisation

You can send personalised messages using a single template.

To personalise a message, use double brackets to add a placeholder to your content. For example:

```md
Hello ((first name)), your reference is ((reference number)).
```

Each time you send the message, you can either:

* fill in the placeholders yourself
* upload a list of personal details and let NotifyNL do it for you

If you upload a list of personal details, the column names in your spreadsheet need to match the placeholders in the template.

You can use personalisation to add:

* [optional content](optional-content.md)
* [a QR code to a letter template](qr-codes.md)
* [custom bullet points to email and letter templates](formatting-emails-and-letters.md)

### Hide personalised content after sending <a href="#hide-personalised-content" id="hide-personalised-content"></a>

Some placeholders include sensitive information like security codes or password reset links. To protect your users, you can choose to hide the content of these placeholders after sending.

Only the recipient will be able to see the sensitive information you’ve sent them. You and your team will see a redacted version of the message.

To hide personalised content after sending:

1. Go to the Templates page.
2. Add a new template or choose an existing template.
3. Select Hide personalisation after sending.

You cannot undo this change.

Read more about [NotifyNL’s security features](../../features/security.md).
