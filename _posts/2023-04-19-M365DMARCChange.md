---
layout: post
title: M365 starts sending DMARC reports
tags: [M365,DMARC]
date: 2023-04-17 17:09:57 +0200
comments: true
---

## Intro

Recently, during late March of 2023. M365 tenants started to sending DMARC reports according to Microsoft 365 Roadmap ID 109535. More details can be found at [MC516348 — DMARC aggregate reports for enterprise](https://techcommunity.microsoft.com/t5/public-sector-blog/march-2023-microsoft-365-us-public-sector-roadmap-newsletter/ba-p/3770486#:~:text=MC516348%20%E2%80%94%20DMARC%20aggregate%20reports%20for%20enterprise).

Kudos to Microsoft for doing so and making an internet safer place.
## Devil is in details
If you read details of article there is a catch:
"*DMARC reports are only sent to domains whose MX is pointed to O365. In order to obtain DMARC aggregate reports for your domain, it must have a valid DMARC record that includes a valid RUA email address.*"

## But what does this mean in real life?

First you need to know a little about of:
- [What is DMARC?](https://dmarc.org/#:~:text=Search%E2%80%A6-,What%20is%20DMARC%3F,-DMARC%2C%20which%20stands)
- [How is it used?](https://dmarc.org/wiki/FAQ#Why_is_DMARC_important.3F:~:text=they%20are%20received.-,How%20does%20DMARC%20work%2C%20briefly%2C%20and%20in%20non%2Dtechnical%20terms%3F,-A%20DMARC%20policy)
- [What problem is it solving?](https://dmarc.org/wiki/FAQ#Why_is_DMARC_important.3F:~:text=fail%20DMARC%20evaluation.-,Why%20is%20DMARC%20needed%3F,-End%20users%20and)

In very simplified manner using logical operators, DMARC is doing following check:

"(Header-from domain == Envelope-From domain) && (Envelope from domain == DKIM domain) "||" (Envelope-From domain == SPF domain) = DMARC Pass "

![DMARC flow ](https://blog.returnpath.com/wp-content/uploads/2015/07/Capture1-1.jpg)

For each received email message DMARC policy is evaluated and aggregate report is being sent periodically to domain owner according specified RUA address in DMARC DNS record. <br>
For example:
"_dmarc.microsoft.com    
text =
        "v=DMARC1; p=reject; pct=100; rua=mailto:itex-rua@microsoft.com; ruf=mailto:itex-ruf@microsoft.com; fo=1""

With parsing these aggregate reports, which are XML files as this  [sample](../assets/img/2023-04-19-M365DMARCChange/dm_emPz0toVbG.xml)  from M365 (Enterprise Outlook) organization can get very accurate picture on how "Internet" sees their SMTP domain and distinguish what is legitimate traffic, legitimate unaligned and malicious traffic as well.


There are many of vendors offering platform that ingest DMARC reports and translate them into valuable sources of information.

For example I'll highlight my top 3:<br>
[DMARC Advisor](https://dmarcadvisor.com/)<br>
[ProofPoint EFD](https://www.proofpoint.com/us/products/email-protection/email-fraud-defense)<br>
[Agari](https://www.agari.com/solutions/email-security/dmarc)


## Reporting
Let's get back to afore mentioned [detail](#devil-is-in-details).

To make it more visually understandable I drew diagram showing 2 cases. For sake of simplicity I created two groups. 3rd party senders and Recipients. 

3rd party senders: Commonly used 3rd party sending email services for such Amazon SES and Mailchimp plus malicious actor.

Receivers (are also senders) such as Yahoo, Gmail, Outlook.com and M365 tenants.

### M365 Sending DMARC report
![M365 Sending DMARC report](../assets/img/2023-04-19-M365DMARCChange/DMARC-M365Sending.png)

### M365 not sending DMARC report to M365 tenant with 3rd party mail GW
![M365 Not sending DMARC report](../assets/img/2023-04-19-M365DMARCChange/DMARC-M365NotSending.png)


## Conclusion

Even though it's a great improvement that M365 as world biggest mail provider is finally sending DMARC reports it does not show a complete picture. Is it Microsoft punishing their M365 tenants for using 3rd party mail gateways (Proofpoint, Cisco ESA...)? Is there something else behind it? Please comment if you know why is this so.
Thanks for reading!