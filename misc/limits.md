---
icon: chart-simple-horizontal
---

# Limits

### Rate limits <a href="#rate-limits" id="rate-limits"></a>

You’re limited to sending 3,000 messages per minute. You should avoid sending messages in large spikes.

This limit is calculated on a rolling basis, per API key type. If you exceed the limit, you will get a `429` error `RateLimitError`.

### Daily limits

There’s a limit to the number of messages you can send each day:

| Service status | Type of API key | Daily limit                                                                                                                                |
| -------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Live           | Team or live    | <ul><li>250,000 emails</li><li>250,000 text messages, including a default 100 international text messages</li><li>20,000 letters</li></ul> |
| Trial          | Team            | 50 emails or text messages                                                                                                                 |
| Live or trial  | Test            | <ul><li>250,000 emails</li><li>250,000 text messages, including a default 100 international text messages</li><li>20,000 letters</li></ul> |

[GP surgeries](https://www.notifications.service.gov.uk/features/who-can-use-notify#gp) cannot send any text messages in trial mode.

While your service is in trial mode, you can only send messages to yourself and your team members.

These limits reset at midnight UTC.
