---
categories:
- Threat Intelligence
- Smishing
date: 2026-07-07
description: Threat Intelligence analysis of recent smishing campaigns
  targeting Egyptian users.
published: true
tags:
- phishing
- smishing
- MITRE
- Egypt
title: Analysis of Smishing Campaigns Targeting Egyptian Mobile Users
---

# Analysis of Smishing Campaigns Targeting Egyptian Mobile Users

![SMS EXAMPLE](/assets/images/posts/smishing-campaign/Screenshot_10.jpg)

Smishing (SMS Phishing) continues to be one of the most effective social
engineering techniques because it targets users through a communication
channel they generally trust. Recent campaigns targeting Egyptian users
abused the names of **Egypt Post**, **Fawry**, **InstaPay**, and
government entities to steal sensitive financial information.

> Attackers don't need sophisticated malware when they can convince
> victims to willingly hand over their credentials.

------------------------------------------------------------------------

# Executive Summary

The campaign relies on localized Arabic SMS messages that create urgency
and encourage victims to click malicious links. Fake websites closely
imitate legitimate services and request payment card information,
National IDs, and One-Time Passwords (OTP).

The infrastructure used in these campaigns changes frequently, making
domain- and IP-based blocking alone insufficient.

------------------------------------------------------------------------

# Attack Flow

``` text
SMS Message
     │
     ▼
Victim Clicks Link
     │
     ▼
Fake Payment Portal
     │
     ▼
Victim Enters:
 • National ID
 • Card Number
 • CVV
 • OTP
     │
     ▼
Credentials Sent to Attacker
     │
     ▼
Financial Fraud
```

------------------------------------------------------------------------

# Financial Lure Campaign

Attackers impersonate trusted financial and logistics providers by
sending SMS messages claiming:

-   A package delivery failed.
-   A small shipping fee must be paid.
-   Address verification is required.

Victims are redirected to cloned payment portals designed to closely
resemble legitimate services.

## Objectives

-   Credential Harvesting
-   Card Theft
-   OTP Collection
-   Financial Fraud

------------------------------------------------------------------------

# Government Penalty Campaign

Another variation impersonates the Egyptian Traffic Police or Public
Prosecution.

Typical lure:

-   Unpaid traffic fine
-   Immediate payment required
-   Legal consequences if ignored

Victims are redirected to fake government payment portals requesting
personal and banking information.

------------------------------------------------------------------------

# Smishing Triad

These campaigns share characteristics commonly associated with the
**Smishing Triad**, a cybercriminal group known for large-scale SMS
phishing operations.

## Common TTPs

-   SMS Phishing
-   Brand Impersonation
-   Arabic Localization
-   Fake Web Portals
-   Credential Harvesting
-   OTP Interception
-   Financial Fraud

------------------------------------------------------------------------

# MITRE ATT&CK Mapping

  Tactic              Technique                         Purpose
  ------------------- --------------------------------- ------------------------
  Initial Access      T1660 -- Phishing                 Malicious SMS
  Defense Evasion     T1655 -- Masquerading             Fake Brands
  Credential Access   T1417 -- Input Capture            Collect Sensitive Data
  Exfiltration        T1646 -- Exfiltration over C2     Send Stolen Data
  Impact              T1640 -- Account Access Removal   Financial Impact

------------------------------------------------------------------------

# Indicators of Compromise

![IOC1](/assets/images/posts/smishing-campaign/image2.png)

![IOC2](/assets/images/posts/smishing-campaign/image3.png)

![IOC3](/assets/images/posts/smishing-campaign/image4.png)

![IOC4](/assets/images/posts/smishing-campaign/image5.png)

## IP Addresses

-  129.226.158\[.\]249

-  43.160.204\[.\]62

- 43.133.39\[.\]120

## Suspicious Domains

-   egyptpost.baby
-   egyptpost.bond
-   egyptpost.icu
-   egyptpost.quest
-   egyptpost.rest
-   fawry.pro
-   fawry.autos
-   instapay.hair
-   instapay.qpon
-   instapay.sbs
-   instapay.love
-   instapay.pics


------------------------------------------------------------------------

# Sigma Rule Example

``` yaml
title: Access to Suspicious Payment Portal
status: experimental

logsource:
  category: proxy

detection:
  selection:
    url|contains:
      - "egyptpost"
      - "instapay"
      - "fawry"
  condition: selection

level: medium
```

------------------------------------------------------------------------

# Defensive Recommendations

## For Users

-   Never trust payment requests received through SMS.
-   Verify traffic fines using official government websites.
-   Never share OTP codes.
-   Verify URLs before entering sensitive information.

## For Organizations

-   Implement DNS filtering.
-   Block known phishing infrastructure.
-   Deploy URL filtering.
-   Share IOCs with SOC platforms.
-   Conduct continuous security awareness campaigns.

------------------------------------------------------------------------

# Key Takeaways

-   Smishing remains highly effective because it abuses user trust
    rather than software vulnerabilities.
-   Brand impersonation significantly increases success rates.
-   Detection requires behavioral monitoring in addition to IOC
    matching.
-   Continuous user awareness remains one of the strongest defenses.

------------------------------------------------------------------------

# References

-   MITRE ATT&CK
-   Cisco Talos
-   Palo Alto Unit 42
-   DarkAtlas
-   DeXpose
-   Silent Push
