# Table of contents

## Features

* [Roadmap](README.md)
* [Who can use NotifyNL](features/who-can-use-notifynl.md)
* [Security](features/security.md)

## Using NotifyNL

* [Using NotifyNL](using-notifynl/using-notifynl/README.md)
  * [API Documentation](using-notifynl/using-notifynl/api-documentation.md)
  * [Attach pages](using-notifynl/using-notifynl/attach-pages.md)
  * [Bulk sending](using-notifynl/using-notifynl/bulk-sending.md)
  * [Daily limits](using-notifynl/using-notifynl/daily-limits.md)
  * [Data retention period](using-notifynl/using-notifynl/data-retention-period.md)
  * [Delivery times](using-notifynl/using-notifynl/delivery-times.md)
  * [Email branding](using-notifynl/using-notifynl/email-branding.md)
  * [Formatting emails and letters](using-notifynl/using-notifynl/formatting-emails-and-letters.md)
  * [Letter branding](using-notifynl/using-notifynl/letter-branding.md)
  * [Links and URLs](using-notifynl/using-notifynl/links-and-urls.md)
  * [Message status](using-notifynl/using-notifynl/message-status.md)
  * [Optional content](using-notifynl/using-notifynl/optional-content.md)
  * [Personalisation](using-notifynl/using-notifynl/personalisation.md)
  * [QR codes](using-notifynl/using-notifynl/qr-codes.md)
  * [Receive text messages](using-notifynl/using-notifynl/receive-text-messages.md)
  * [Reply-to email address](using-notifynl/using-notifynl/reply-to-email-address.md)
  * [Returned letters](using-notifynl/using-notifynl/returned-letters.md)
  * [Schedule messages](using-notifynl/using-notifynl/schedule-messages.md)
  * [Send files by email](using-notifynl/using-notifynl/send-files-by-email.md)
  * [Sign-in method](using-notifynl/using-notifynl/sign-in-method.md)
  * [Team members and permissions](using-notifynl/using-notifynl/team-members-and-permissions.md)
  * [Templates](using-notifynl/using-notifynl/templates.md)
  * [Text message sender ID](using-notifynl/using-notifynl/text-message-sender-id.md)
  * [Trial mode](using-notifynl/using-notifynl/trial-mode.md)
  * [Unsubscribe links](using-notifynl/using-notifynl/unsubscribe-links.md)
  * [Upload a letter](using-notifynl/using-notifynl/upload-a-letter.md)

## Tech documentation

* [Java client documentation](tech-documentation/java.md)
* [.NET client documentation](tech-documentation/dotnet.md)
* [Node JS client documentation](tech-documentation/node-js.md)
* [PHP client documentation](tech-documentation/php.md)
* [Python client documentation](tech-documentation/python.md)
* [Ruby client documentation](tech-documentation/ruby.md)
* [REST API documentation](tech-documentation/rest-api-documentation.md)

## misc

* [Testing](misc/testing.md)
* [API keys](misc/api-keys.md)
* [Limits](misc/limits.md)
* [Callbacks](misc/callbacks.md)
* [API architecture](misc/api-architecture.md)

## OpenAPI specification

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: true
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: notifynl-api
  ```
* ```yaml
  props:
    models: true
    downloadLink: true
  type: builtin:openapi
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: notifynl-api
  ```
