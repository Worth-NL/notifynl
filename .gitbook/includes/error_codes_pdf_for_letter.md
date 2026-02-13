---
title: error_codes_pdf_for_letter
---

| Error message                                        | How to fix                                                                  |
| ---------------------------------------------------- | --------------------------------------------------------------------------- |
| **ValidationError (status code 400)**                |                                                                             |
| `id is not a valid UUID`                             | Check the notification ID.                                                  |
| `Notification is not a letter`                       | Check that you are looking up the correct notification.                     |
| **PDFNotReadyError (status code 400)**               |                                                                             |
| `PDF not available yet, try again later`             | Wait for the letter to finish processing. This usually takes a few seconds. |
| **BadRequestError (status code 400)**                |                                                                             |
| `File did not pass the virus scan`                   | You cannot retrieve the contents of a letter that contains a virus.         |
| `PDF not available for letters in technical-failure` | You cannot retrieve the contents of a letter in technical-failure.          |
| `Notification is not a letter`                       | Check that you are looking up the correct notification.                     |
| **NoResultFound (status code 404)**                  |                                                                             |
| `No result found`                                    | Check the notification ID.                                                  |
