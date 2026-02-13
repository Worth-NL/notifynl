---
title: schema_validation_errors
---

The following are a few examples of schema validation errors you may encounter when making a request to a Notify endpoint.

| Error message                                                                            | How to fix                                                                |
| ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **ValidationError (status code 400)**                                                    |                                                                           |
| `template_id is a required property`                                                     | Provide the missing argument.                                             |
| `sms_sender_id is not a valid UUID`                                                      | Check the argument to make sure that it is valid for the given data type. |
| `personalisation <data type of argument you sent> is not of type object`                 | Provide argument in the correct type.                                     |
| `reference <reference string you provided> is too long`                                  | Provide a shorter string.                                                 |
| `type <invalid type> is not one of [sms, email, letter]`                                 | Make sure that the argument matches one of the items in the list.         |
| `Additional properties are not allowed (<list of unexpected properties> was unexpected)` | Only provide allowed arguments for the endpoint.                          |
