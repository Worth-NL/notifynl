# QR codes

A QR code lets the recipient of a letter visit your website without having to type in the URL.

When you add a QR code to your letter, you must provide an alternative for people who cannot scan QR codes. For example, a short URL that’s easy to read or written instructions on how to find your website.

### How to add QR codes

You can either:

* add the same QR code to every letter you send
* personalise the QR code each time you send a letter

Do not add more than one QR code per page.

#### Add the same QR code to every letter you send

Copy this example to add a QR code to your letter template:

```md
QR: https://www.gov.uk/example
```

Leave one empty line space before and after the QR code.

#### Personalise the QR code each time you send a letter

Use [placeholders](personalisation.md) to personalise all or part of the link contained in a QR code.

Each time you send the letter template, you can either:

* fill in the placeholder with a link yourself
* upload a list of personal details, including links, and let Notify do it for you

Copy this example if the whole link changes each time you send a letter:

```md
QR: ((link))
```

Copy this example if only part of the link changes each time you send a letter:

```md
QR: https://www.example.com?reference=((link reference))
```

Leave one empty line space before and after the QR code.

Choose a unique name for the placeholder inside the double brackets. When you send the letter, Notify will replace this with the QR code link.

If you upload a list of personal details, the column names in your spreadsheet need to match the placeholders in the template.
