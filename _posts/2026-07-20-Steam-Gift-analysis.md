---
categories:
- Social Engineering
date: 2026-07-20
description: Technical Analysis of a Steam Gift Phishing Campaign Leveraging Browser-in-the-Browser (BitB)
published: true
tags:
- Browser-in-the-Browser
- Phishing
title: Technical Analysis of a Steam Gift Phishing Campaign Leveraging Browser-in-the-Browser (BitB)
---



# Technical Analysis of a Steam Gift Phishing Campaign Leveraging Browser-in-the-Browser (BitB)

![introfig](/assets/images/posts/steamBitB/chat_example.png)

## Executive Summary

This report analyzes a phishing campaign targeting Steam users through a fake $50 gift card promotion.

The attack begins with a Steam Chat message containing a link to `dobros-gifting[.]club`. The website impersonates Steam and uses a fake "Summer Gift Marathon" promotion to convince victims to sign in.

After clicking "SIGN IN", the victim is presented with a fake Steam authentication window that displays `steamcommunity.com` in a simulated address bar. Analysis confirmed behavior consistent with the Browser-in-the-Browser (BitB) phishing technique.

Dynamic analysis also confirmed that submitted credentials are sent via an HTTP POST request to the attacker-controlled domain.

Further JavaScript analysis revealed functionality for credential submission, authentication code handling, and confirmation checking, indicating a multi-stage phishing workflow.

---

## Campaign Overview

The campaign uses a fake Steam gift card promotion to steal user authentication information.

The observed attack flow is:

`Steam Chat Message`

↓

`Phishing Link`

↓

`Fake Steam Gift Page`

↓

`Browser-in-the-Browser Login`

↓

`Credential Submission`

↓

`Attacker-Controlled Backend`

↓

`Potential MFA / Authentication Challenge`

---

## Initial Phishing Lure

The attack begins with a Steam Chat message containing the following text:

> **"STEAM GIVES THE GIFT"**

The message directs the victim to:

`dobros-gifting[.]club/id=489501036`

Steam detected the message as suspicious and automatically disabled the link.

![Figure1](/assets/images/posts/steamBitB/chat_example.png)

> **Figure 1: Initial Steam Chat Phishing Message.** Screenshot showing the phishing message, the `dobros-gifting[.]club` URL, and Steam's "Suspicious Chat" warning.

The attacker uses a free gift as a social engineering lure to convince the victim to visit the external website.

---

## Fake Steam Gift Landing Page

The phishing link redirects the victim to a fake Steam-themed website hosted on:

`dobros-gifting[.]club`

The page advertises a fake "Summer Gift Marathon" offering a $50 Steam gift card.

The page uses:

- Steam branding.
- A $50 gift card incentive.
- A fake availability counter.
- A prominent "SIGN IN" button.

![Figure2](/assets/images/posts/steamBitB/langing.png)

> **Figure 2: Fake Steam Gift Landing Page.** Screenshot showing the "Summer Gift Marathon", $50 gift card offer, availability counter, "SIGN IN" button, and the actual `dobros-gifting[.]club` domain in the browser address bar.

The fake promotion and limited availability counter are used to create urgency and encourage the victim to sign in.

---

## What is Browser-in-the-Browser (BitB)?

Browser-in-the-Browser (BitB) is a phishing technique that creates a fake browser window inside a malicious webpage.

The fake window can imitate a legitimate login popup, including:

- Browser controls.
- Address bar.
- HTTPS lock icon.
- Trusted domain name.
- Legitimate-looking login page.

Because the entire window is controlled by the phishing page, attackers can display a legitimate domain in the fake address bar while collecting credentials through their own infrastructure.

One way to identify a BitB window is to try dragging it outside the original browser window. Since it is part of the webpage, it cannot move beyond the browser boundaries.

![Figure10](/assets/images/posts/steamBitB/comparison.png)

> **Figure 3: Browser-in-the-Browser Concept.** An illustration comparing a legitimate browser authentication popup with a fake BitB window. The image should highlight that the legitimate popup is a separate browser window, while the BitB window is rendered inside and confined to the attacker-controlled webpage.

The technique can be summarized as:

`Attacker-Controlled Website`

↓

`Fake Browser Window`

↓

`Fake Address Bar Showing a Trusted Domain`

↓

`Fake Login Form`

↓

`Credential Submission to Attacker`


---

## Browser-in-the-Browser (BitB) Analysis

After clicking "SIGN IN", a fake Steam authentication window appears.

The simulated window displays:

`steamcommunity.com/login/home/?goto=`

This makes the victim believe they are signing in through Steam's legitimate website.

![Figure3](/assets/images/posts/steamBitB/BitB-example.png)

> **Figure 4: Fake Steam Authentication Window.** Screenshot showing the simulated browser window, the displayed `steamcommunity.com` URL, and the Steam login form.

Testing showed that the window could not be dragged outside the original browser window. This indicates that the window is part of the phishing webpage rather than a real browser window.

This behavior is consistent with a Browser-in-the-Browser (BitB) phishing technique.

The attacker uses the fake address bar to display a trusted Steam domain while the victim remains on the attacker-controlled website.

The observed flow is:

`Fake Steam Gift Page`

↓

`SIGN IN`

↓

`Fake Browser Window`

↓

`Displayed steamcommunity.com URL`

↓

`Steam Login Form`

↓

`Credential Submission`

---

## Credential Harvesting Analysis

Dynamic analysis confirmed that credentials entered into the fake Steam login form are sent to the attacker-controlled domain.

The request uses the HTTP `POST` method and includes the submitted `username` and `password`.

![Figure4](/assets/images/posts/steamBitB/POST-request.png)

> **Figure 5: Credential Submission Request.** Screenshot of the Network tab showing the POST request sent to `dobros-gifting[.]club` after submitting test credentials.

![Figure5](/assets/images/posts/steamBitB/payload.png)

> **Figure 6: Credential Request Payload.** Screenshot showing the POST request payload containing the test `username` and `password`.

When invalid test credentials were submitted, the server returned the following response:

`{"success":false,"error":{"result":5,"message":"InvalidPassword"}}`

![Figure6](/assets/images/posts/steamBitB/InvalidPassword.png)

> **Figure 7: Invalid Credential Response.** Screenshot showing the `InvalidPassword` response returned by the phishing backend.

This confirms that submitted credentials are processed by the attacker-controlled infrastructure.

---

## Endpoint Obfuscation

The phishing kit obfuscates its backend API paths before sending requests.

The JavaScript builds logical endpoints such as:

- `/steam/doLogin`
- `/steam/submitCode`
- `/steam/checkConfirmations`

These paths are transformed using the `kT()` function and a value stored in `localStorage.secret`.

This mechanism hides the purpose of the requests when viewed directly in network traffic.

![Figure7](/assets/images/posts/steamBitB/obfuscated-request.png)

> **Figure 8: Obfuscated API Request.** Screenshot showing the long obfuscated request path observed in the Network tab.

The observed credential submission request was mapped to the internal `doLogin` workflow.

The analysis confirms the following flow:

`Credentials`

↓

`doLogin`

↓

`Endpoint Obfuscation`

↓

`POST Request`

↓

`Attacker-Controlled Backend`

↓

`Authentication Response`

The available evidence confirms credential harvesting and multi-stage authentication handling. However, server-side communication between the phishing backend and Steam infrastructure was not confirmed during this analysis.

---

## Indicators of Compromise (IOCs)

| Type | Value | Description |
|------|-------|-------------|
| Domain | `dobros-gifting[.]club` | Phishing website |

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---------|-----------|----|
| Initial Access | Phishing | T1566 |
| Credential Access | Input Capture | T1056 |
| Defense Evasion | Masquerading (Steam impersonation) | T1036 |

The campaign relies on phishing and brand impersonation to obtain user credentials while presenting a fake Steam authentication interface.

---

## Detection Opportunities

Organizations can detect similar activity by monitoring for:

- Access to newly registered or suspicious Steam-themed domains.
- HTTP POST requests containing login credentials to non-Steam domains.
- Browser access to fake Steam login pages.
- Steam branding hosted outside official Steam infrastructure.
- Users visiting domains immediately after receiving Steam Chat messages.

---

## Recommendations

- Block the identified phishing domain.
- Educate users to verify the browser address bar before entering credentials.
- Be aware of Browser-in-the-Browser (BitB) phishing techniques.
- Enable Multi-Factor Authentication (MFA).
- Reset passwords if credentials were submitted.
- Revoke active Steam sessions after suspected compromise.

---

## Conclusion

The analyzed phishing campaign combines multiple social engineering and technical techniques to increase its credibility.

The campaign impersonates Steam, abuses a fake gift card promotion, and uses a Browser-in-the-Browser login window to deceive victims into submitting their credentials.

Dynamic analysis confirmed that credentials are transmitted to attacker-controlled infrastructure, while JavaScript analysis revealed support for multi-stage authentication workflows and endpoint obfuscation.

Although communication between the phishing backend and Steam infrastructure could not be confirmed, the collected evidence demonstrates that the phishing kit is significantly more advanced than a traditional credential harvesting page.