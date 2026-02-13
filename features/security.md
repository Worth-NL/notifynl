---
description: NotifyNL is built for the security needs of government services.
icon: user-police-tie
---

# Security

This page describes our approach to:

* [running a secure service](https://www.notifications.service.gov.uk/features/security#running-a-secure-service)
* [storing and processing your data](https://www.notifications.service.gov.uk/features/security#storing-and-processing-your-data)
* [protecting data in transit](https://www.notifications.service.gov.uk/features/security#protecting-data-in-transit)
* [protecting data at rest](https://www.notifications.service.gov.uk/features/security#protecting-data-at-rest)
* [building and managing Notify](https://www.notifications.service.gov.uk/features/security#building-and-managing-notify)
* [finding and fixing security issues](https://www.notifications.service.gov.uk/features/security#finding-and-fixing-security-issues)
* [security incidents](https://www.notifications.service.gov.uk/features/security#security-incidents)
* [sign in and API access](https://www.notifications.service.gov.uk/features/security#sign-in-and-api-access)
* [protecting our website and API](https://www.notifications.service.gov.uk/features/security#protecting-our-website-and-api)
* [email security](https://www.notifications.service.gov.uk/features/security#email-security)
* [security classifications](https://www.notifications.service.gov.uk/features/security#security-classifications)
* [GOV.UK Notify staff](https://www.notifications.service.gov.uk/features/security#staff)
* [suppliers](https://www.notifications.service.gov.uk/features/security#suppliers)

### Running a secure service <a href="#running-a-secure-service" id="running-a-secure-service"></a>

NotifyNL

* follows the principles of the [Open Standaard](https://www.digitaleoverheid.nl/overzicht-van-alle-onderwerpen/open-standaarden/) for the Dutch government
* is currently under review by Logius to become the de-facto Notification platform for the Dutch government

We regularly assess and review our security in line with:

* the [OWASP guidelines](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/) en
* de [NIS2-richtlijn](https://www.digitaleoverheid.nl/overzicht-van-alle-onderwerpen/cybersecurity/wetten-en-regels/) for cybersecurity standards

We monitor the threat landscape and conduct regular penetration testing so we can:

* continue to improve our security
* deal with common threats like Distributed Denial of Service (DDoS) attacks

### Storing and processing your data <a href="#storing-and-processing-your-data" id="storing-and-processing-your-data"></a>

NotiyNL currently uses Amazon Web Services (AWS) as our cloud service provider.

Data on Notify is stored and processed in:

* AWS data centres in the EU
* locations where our sub-processors store and process data (EU only)

#### How long we keep your data <a href="#how-long-we-keep-your-data" id="how-long-we-keep-your-data"></a>

NotifyNL keeps a temporary record of:

* the content of the emails, text messages and letters you send
* recipient email addresses, mobile numbers and addresses

By default, we keep this data for 7 days.

Once your service is live, you can choose the number of days you want Notify to keep details of the messages you send.

For more information, see [data retention period](../using-notifynl/using-notifynl/data-retention-period.md).

#### Who can access your data <a href="#who-can-access-your-data" id="who-can-access-your-data"></a>

Your data could be accessed by:

* the Notify team
* law enforcement agencies (where legally required)

Teams using NotifyNL can only access their own data.

You can set [different permissions for each member of your team](../using-notifynl/using-notifynl/team-members-and-permissions.md).

AWS provides logical separation between different AWS customers.

#### Data centre security <a href="#data-centre-security" id="data-centre-security"></a>

AWS provides a description of their:

* [physical security measures, data sanitisation and equipment disposal arrangements](https://aws.amazon.com/compliance/data-center/controls/)
* [security assurance materials](https://aws.amazon.com/compliance/programs/)

#### How text messages are stored and processed <a href="#how-text-messages-are-stored-and-processed" id="how-text-messages-are-stored-and-processed"></a>

Text messages are stored and processed in:

* the UK and Ireland
* the country where the recipient’s phone is
* the phone’s country of origin (for international numbers)

### Protecting data in transit <a href="#protecting-data-in-transit" id="protecting-data-in-transit"></a>

GOV.UK Notify uses Transport Layer Security (TLS) version 1.2 to encrypt data when:

* users access the Notify website or API
* data passes through Notify
* we exchange data with our sub-processors

#### Emails

We always try to encrypt emails using TLS 1.2, 1.1 or 1.0. If the recipient’s mail server does not support TLS, we will send the email without protection.

Email cannot provide end-to-end encryption.

#### Text messages

Text messages cannot provide end-to-end encryption.

### Protecting data at rest <a href="#protecting-data-at-rest" id="protecting-data-at-rest"></a>

GOV.UK Notify encrypts the data stored in our databases and backups using AES-256 encryption.

This includes any files that you upload to Notify when you:

* [send a batch of messages](https://www.notifications.service.gov.uk/using-notify/bulk-sending)
* [attach pages to a letter template](https://www.notifications.service.gov.uk/using-notify/attach-pages)
* [upload a letter](https://www.notifications.service.gov.uk/using-notify/upload-a-letter)

#### Sending files by email <a href="#sending-files-by-email" id="sending-files-by-email"></a>

When you upload a file we encrypt it with [AWS SSE-C](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html), which uses AES-256 encryption.

We will only share the unique link with the intended recipient. We cannot access or decrypt your file.

For more information about this feature, see [send files by email](https://www.notifications.service.gov.uk/using-notify/send-files-by-email).

### Building and managing GOV.UK Notify <a href="#building-and-managing-notify" id="building-and-managing-notify"></a>

We follow an Agile software development lifecycle.

To protect our code, we:

* run separate development, testing and production environments
* deploy code through a continuous integration/continuous delivery (CI/CD) pipeline
* track vulnerabilities for any third-party libraries we use
* store production secrets in a secure environment with audited access

#### How we manage code changes <a href="#how-we-manage-code-changes" id="how-we-manage-code-changes"></a>

To manage GOV.UK Notify, we use:

* GDS-managed devices
* multi-factor authentication (MFA)

We manage Notify through the www.notifications.service.gov.uk website.

AWS manages the hardware we use.

We use infrastructure as code (IaC) to manage the systems and services that host Notify.

All code changes must be reviewed by the team before we can deploy them.

We monitor our production environment for unauthorised changes.

We give our users appropriate notice before:

* any planned outages or downtime
* making significant functional changes

We announce planned downtime on the [Notify status page](https://status.notifications.service.gov.uk).

### Finding and fixing security issues <a href="#finding-and-fixing-security-issues" id="finding-and-fixing-security-issues"></a>

GOV.UK Notify:

* follows [secure development principles](https://www.ncsc.gov.uk/collection/developers-collection/principles)
* tracks third-party dependencies in our code base
* monitors our logs for attacks, misuse and malfunctions
* provides [24-hour online support](https://www.notifications.service.gov.uk/support)

We use [Web Check](https://www.ncsc.gov.uk/information/web-check) and other services to:

* scan for vulnerabilities
* prioritise which software patches to test and deploy first

AWS is responsible for patching our infrastructure:

* firmware
* hardware
* operating system (OS) kernel

### Security incidents <a href="#security-incidents" id="security-incidents"></a>

We provide a 24-hour response in case of an incident.

If there is a data loss event, we will contact you directly.

If there is another type of incident, we’ll publish details and updates on the [Notify status page](https://status.notifications.service.gov.uk).

### Sign in and API access <a href="#sign-in-and-api-access" id="sign-in-and-api-access"></a>

#### Signing in to Notify

GOV.UK Notify uses two-factor authentication for sign-in.

Team members can sign in with a text message code or a link that’s sent in an email.

For security, you’ll need to confirm that you still have access to your email address every 3 months.

Find out more about our [sign-in methods](https://www.notifications.service.gov.uk/using-notify/sign-in-method).

You must keep to our [terms of use for signing in to Notify](https://www.notifications.service.gov.uk/terms-of-use).

#### Accessing the GOV.UK Notify API

Services access the GOV.UK Notify API with an API key, encoded using [JSON Web Tokens](https://jwt.io/).

For more information, see our [API documentation](https://www.notifications.service.gov.uk/using-notify/api-documentation).

### Protecting our website and API <a href="#protecting-our-website-and-api" id="protecting-our-website-and-api"></a>

The GOV.UK Notify website, API and any files sent by email are protected by:

* AWS [Web Application Firewall](https://aws.amazon.com/waf/) (WAF)
* AWS [Shield Advanced](https://aws.amazon.com/shield/)
* rate limiting

We use publicly-verifiable digital certificates, so you’ll always know you’ve connected to the real GOV.UK Notify.

### Email security <a href="#email-security" id="email-security"></a>

To help recipient’s email services tell the difference between our emails and spam, we use:

* [Domain-based Message Authentication, Reporting and Conformance (DMARC)](https://www.gov.uk/government/publications/email-security-standards/domain-based-message-authentication-reporting-and-conformance-dmarc)
* [DomainKeys Identified Mail (DKIM)](https://www.gov.uk/government/publications/email-security-standards/domainkeys-identified-mail-dkim)
* [Sender Policy Framework (SPF)](https://www.gov.uk/government/publications/email-security-standards/sender-policy-framework-spf)

### Security classifications <a href="#security-classifications" id="security-classifications"></a>

We have designed GOV.UK Notify for sending messages classified as ‘OFFICIAL’, including ‘OFFICIAL-SENSITIVE’, under the [Government Security Classifications policy](https://www.gov.uk/government/publications/government-security-classifications).

Before you send any messages classified as ‘OFFICIAL’, you must make sure that GOV.UK Notify meets your organisation’s standards for:

* using, processing, storing and sending information
* cyber security
* data protection

Notify must not be used to process data classified as ‘SECRET’ or ‘TOP SECRET’.

### GOV.UK Notify staff <a href="#staff" id="staff"></a>

We restrict the number of people that can access your data on GOV.UK Notify.

We follow the principle of least privilege. This means we give our team members the lowest level of permissions needed to do their job.

All our staff:

* receive security training
* complete [personnel screening equivalent to BPSS](https://www.gov.uk/government/publications/government-baseline-personnel-security-standard)

Team members who need greater access to the data stored on Notify must complete [National Security Vetting to Security Check (SC) level](https://www.gov.uk/government/publications/united-kingdom-security-vetting-clearance-levels/national-security-vetting-clearance-levels#security-check-sc).

We only give additional access to GOV.UK Notify’s production environment to privileged users:

* by exception
* on a temporary basis
* in relation to a specific change request or support ticket

The GDS security operations team logs and tracks privileged users’ access to our production environment.

### Suppliers <a href="#suppliers" id="suppliers"></a>

GOV.UK Notify uses third-party providers to send emails, text messages and letters.

Suppliers sign a contract or memorandum of understanding that includes our security requirements.

All our suppliers:

* receive security training
* complete [personnel screening equivalent to BPSS](https://www.gov.uk/government/publications/government-baseline-personnel-security-standard)

GDS assesses suppliers:

* before we decide whether to use them
* at regular intervals to make sure they still meet our requirements
