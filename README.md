# SOC-Phishing-Simulator
In this lab, I reviewed phishing alerts, investigating firewall and proxy logs using Splunk, and wrote short case reports for each alert. 

The goal of this lab was to determine whether each alert was a **true positive** or a **false positive**, provide evidence, and to clearly explain the reasoning behind each decision to reach a conclusion. 

## Alert #1: Access to Blacklisted External URL Blocked by Firewall
Severity: High 

To begin triaging alerts, I started with the alert labeled high severity (the other 4 alerts were medium). High severity alerts can indicate active threats, attacks, or attempts to reach malicious domains, and they rightfully a top priority. 

![alert 1](Screenshots/alert-1.png)

*“The alert was triggered when a user attempted to access an external URL that is listed in the organization’s blacklist or threat intelligence feeds. The firewall or proxy successfully blacked the outbound request, preventing the connection. Note: The blacklist only covers known threats. It does not guarantee protection against new or unknown malicious domains.”*

From this firewall alert, I could see in the details that the action was blocked, so the firewall already has a rule in place to prevent outbound connections to this malicious domain. 

I opened Splunk and filtered for the source host or the specific URL, to analyze the source hosts outbound connections and if anyone else tried to connect to the URL.

Index=* 10.20.2.17 OR "http<!-- -->://bit.ly/3sHkX3da12340"

![alert 1 logs](Screenshots/alert-1-logs.png)

From the Splunk logs, I confirmed the result. The firewall blocked the action, and the attempt was isolated (contained) to this single source host. I found no other suspicious activity. 

To finish, I wrote my incident report. I classified the result as a true positive, as a internal host did in fact try to reach a malicious domain on the company blacklist. 

Even though there was no compromise, I documented the alert for compliance and security awareness. The employee needs to be informed that they attempted to access a blacklisted URL and remind him of the company’s security practices. 

![alert 1 report](Screenshots/alert-1-report.png)

## Alert #2: Inbound Email Containing Suspicious External Link
Severity: Medium

The next alert happened due to an email potentially containing a phishing attempt. The email was flagged because it contained an external link. 

![alert 2](Screenshots/alert-2.png)

To investigate, I searched the Splunk logs for links from the external domain and got three results.

Index=* "http<!-- -->://hrconnex.thm"

![alert 2 logs](Screenshots/alert-2-logs.png)

I checked whether the recipient clicked on the link (he didn’t) and if anyone else received that link and clicked on it. 

I found this was an isolated incident and a **false positive**. An email sent to the IT team from HR explained the link came from a new third -party onboarding tool the company is using. I documented all this in the incident report and closed the alert. 

![alert 2 report](Screenshots/alert-2-report.png)

## Alert #3: Inbound Email Containing Suspicious External Link

From the details of this alert, this seems very suspicious and very likely a true positive. First, the sender’s email “urgents<!-- -->@amazon.biz” is a suspicious domain, and the message oviously contains a personal information phishing attempt; “We were unable to deliver your package due to an incomplete address.\n\nPlease confirm your shipping information by clicking the link below:\n\nhttp<!-- -->://bit.ly/3sHkX3da12340”

![alert 3](Screenshots/alert-3.png)

Searching the Splunk logs for the sender’s email or the suspicious link with the query:

index=* ("urgents<!-- -->@amazon.biz" OR "http<!-- -->://bit.ly/3sHkX3da12340")

![alert 3 logs](Screenshots/alert-3-logs.png)

I was able to determine a correlation between this alert and alert #1. This phishing email was sent to h.harris and they clicked on the malicious link in the email, which the firewall then blocked. I documented and reported this in the incident report, and also escalated the alert as the recipient did click on the link.

![alert 3 report](Screenshots/alert-3-report.png)

## Alert #4: Inbound Email Containing Suspicious External Link

Similar to the last alert, this is suspicious and likely a true positive. The sender’s email domain (m1crosoftsupport.co) is impersonating Microsoft, and the email contains an external link with this same domain, asking for login credentials. 

![alert 4](Screenshots/alert-4.png)

I checked the Splunk logs and filtered for the sender’s email or the external link to verify this incident was contained to a single user. 

Index=* ("no-reply<!-- -->@m1crosoftsupport.co” OR "https<!-- -->://m1crosoftsupport.co/login")

![alert 4 logs](Screenshots/alert-4-logs.png)

A concerning result. It looks like the recipient of the email, c.allen did click on the malicious link and the firewall allowed access to the URL. This is a true positive and needs to be escalated as credentials have been compromised. I documented all this in the incident report.

![alert 4 report](Screenshots/alert-4-report.png)

## How I Can Improve:







































