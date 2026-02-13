# Formatting emails and letters

NotifyNL uses Markdown to format content.

You can see formatting instructions while you’re editing a template:

1. Go to the Templates page.
2. Add a new template or choose an existing template and select Edit.
3. Scroll down to see a guide to the available Markdown.

## Formatting options

Email templates can include:

* [bullet points](formatting-emails-and-letters.md#bullets)
* [headings](formatting-emails-and-letters.md#headings-and-subheadings)
* [horizontal lines](formatting-emails-and-letters.md#horizontal-lines)
* [inset text](formatting-emails-and-letters.md#inset-text)
* [numbered steps](formatting-emails-and-letters.md#numbered-steps)

Letters can include:

* [bullet points](formatting-emails-and-letters.md#bullets)
* [headings](formatting-emails-and-letters.md#headings-and-subheadings)
* [numbered steps](formatting-emails-and-letters.md#numbered-steps)
* [page breaks](formatting-emails-and-letters.md#page-breaks)

You cannot use bold, italics, underlined text, different typefaces or fonts. This is because they can make it harder for people to read what you’ve written.

## Guidance

### Bullet points <a href="#bullets" id="bullets"></a>

Use bullet points to help words or phrases stand out in your emails and letters.

You can either:

* add the same bullet points to every message you send
* add personalised bullet points each time you send a message

Copy this example to add bullet points:

```md
Introduce bullet points with a lead-in line ending in a colon:

* leave one empty line space after the lead-in line
* use an asterisk or a dash followed by a space to add an item
* start each item with a lowercase letter, do not end with a full stop
* leave one empty line space after the last item
```

To create sub-items, add an indent of 2 spaces before the asterisk or dash.

<details>

<summary>How to add personalised bullet points</summary>

Copy this example to add a [placeholder](https://www.notifications.service.gov.uk/using-notify/personalisation) to your message template:

```md
Introduce bullet points with a lead-in line ending in a colon:

((bullet points))

Leave one empty line space before the next paragraph.
```

To send the message, [upload a list of recipient details](https://www.notifications.service.gov.uk/using-notify/bulk-sending).

Your spreadsheet should include one column for each bullet point. The column names must match the placeholder in the template.

Notify will fill in the placeholder with your bullet points.

</details>

There’s more [guidance about bullet points on GOV.UK](https://www.gov.uk/guidance/style-guide/a-to-z-of-gov-uk-style#bullet-points-steps).

### Headings <a href="#headings-and-subheadings" id="headings-and-subheadings"></a>

Use a heading to tell recipients what your email or letter is about.

For emails, use subheadings to break up the rest of your content. Your first subheading must come after a heading.

For letters, use headings to break up the rest of your content.

Write all headings and subheadings in sentence case.

Use one hash symbol followed by a space for a heading in emails and letters, for example:

```md
# This is a heading
```

Use 2 hash symbols followed by a space for a subheading in emails, for example:

```md
## This is a subheading
```

There’s more [guidance about headings on GOV.UK](https://www.gov.uk/guidance/content-design/writing-for-gov-uk#headings).

### Horizontal lines <a href="#horizontal-lines" id="horizontal-lines"></a>

Use a horizontal line to create separate sections in an email template.

To add a horizontal line between 2 paragraphs, use 3 dashes. Leave one empty line space after the first paragraph. For example:

```md
First paragraph

---
Second paragraph
```

### Inset text <a href="#inset-text" id="inset-text"></a>

Use inset text to differentiate a block of text from the content that surrounds it, for example:

* quotes
* examples
* additional information

To add inset text, use a caret. For example:

```md
^ You must tell us if your circumstances change.
```

Use inset text very sparingly – it’s less effective if it’s overused.

There’s more [guidance about inset text in the GOV.UK Design System](https://design-system.service.gov.uk/components/inset-text/).

### Numbered steps <a href="#numbered-steps" id="numbered-steps"></a>

Use numbered steps instead of bullet points to guide a user through a process, or when the order of the items in a list is relevant.

You do not need a lead-in line for a numbered list.

Copy this example to add numbered steps:

```md
1. Leave one empty line space before starting your list.
2. Enter a number followed by a full stop and a space to add an item.
3. Start each item with a capital letter and end it with a full stop.
4. Leave one empty line space after the last item.
```

To create sub-items, add an indent of 2 spaces before the number.

There’s more [guidance about numbered lists in the GOV.UK Design System](https://design-system.service.gov.uk/styles/lists/#numbered-lists).

### Page breaks <a href="#page-breaks" id="page-breaks"></a>

To insert a page break in a letter template, use 3 asterisks. For example:

```md
Content on page 1

***

Content on page 2
```
