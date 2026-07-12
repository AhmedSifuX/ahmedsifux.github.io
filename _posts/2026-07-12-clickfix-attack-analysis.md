---
categories:
- Social Engineering
date: 2026-07-12
description: Analysis of the ClickFix social engineering technique used to deliver malware.
published: true
tags:
- ClickFix
- DarkGate
- Phishing
- Malware
- MITRE
title: ClickFix Attack Analysis
---

# Introduction

Cybercriminals are constantly finding new ways to trick users into compromising their own devices. One of the latest social engineering techniques is known as the **ClickFix Attack**.

Instead of exploiting software vulnerabilities, ClickFix attacks rely on convincing users to perform actions themselves. Victims are usually presented with fake error messages, verification pages, or technical issues and are instructed to follow a few simple steps to "fix" the problem.

> These steps often involve opening the Run dialog, PowerShell, or Terminal and executing commands provided by the attacker, ultimately  
> leading to malware infection or unauthorized access to the system.

![introfig](/assets/images/posts/Clickfix/clickfix-example.jpg)

---

# Executive Summary

The **ClickFix Attack** is a social engineering technique that tricks users into manually executing malicious commands on their own devices.

Attackers typically deliver these attacks through phishing emails, fake websites, malicious advertisements, or fraudulent verification pages. The victim is shown a fake issue or security check and is guided through a series of steps that appear harmless but actually execute malicious code.

Because the victim performs the action voluntarily, traditional security controls may have difficulty detecting or preventing the attack in its early stages.

This report provides an overview of the ClickFix attack technique, explains how it works, highlights common delivery methods, and discusses detection and mitigation strategies that organizations can use to reduce the risk of compromise.

---

# What is ClickFix?

ClickFix is a social engineering technique that tricks users into running malicious commands on their own devices. Instead of exploiting vulnerabilities, attackers rely on the victim to manually execute the malicious action.

# How the Attack Works

The victim is presented with a fake error message, CAPTCHA, or verification page and is instructed to follow a few steps to fix the issue. These steps usually involve copying and executing a command using Run, PowerShell, or Terminal.

Once executed, the command downloads and runs the attacker's payload.

# Attack Chain

1. The victim opens a phishing email or malicious website.
2. A fake error or verification page is displayed.
3. The victim copies and executes a command.
4. The command downloads and runs malware.
5. The attacker gains access to the system or steals data.

---

# Technical Analysis

One of the earliest observed uses of the ClickFix technique was identified by Microsoft Threat Intelligence between March and June 2024 in campaigns attributed to the threat actor **Storm-1607**.

![Fig1](/assets/images/posts/Clickfix/Fig3-Storm-1607-phishing-email.webp)

The attackers distributed phishing emails containing HTML attachments disguised as invoices or payment reports. When opened, the attachment displayed a fake Microsoft Word document along with an error message claiming that the file could not be opened correctly.

![Fig2](/assets/images/posts/Clickfix/Fig4-HTML-attachment.webp)

The victim was instructed to click a **"How to fix"** button, which silently copied a malicious command to the clipboard. The page then provided step-by-step instructions to open **Windows Terminal** and paste the copied command.

![Fig3](/assets/images/posts/Clickfix/Fig5-ClickFix-lure.webp)

Once executed, the command downloaded and installed **DarkGate**, a malware loader capable of establishing command-and-control communications, downloading additional payloads, and performing various malicious activities on the infected system.

---

Another ClickFix campaign observed in June 2025 impersonated the US Social Security Administration (SSA) to deliver ScreenConnect, a legitimate remote management tool frequently abused by threat actors.

The campaign started with phishing emails sent from a compromised domain and used social engineering techniques to convince victims that there was an issue with their social security statement.

![fig4](/assets/images/posts/Clickfix/Fig8-Phishing-email.webp)

The email contained a Download Statement button that redirected users through a Google Ads redirector before eventually landing on a spoofed SSA website hosted on access-ssa-gov[.]es.

![Fig5](/assets/images/posts/Clickfix/Fig9-ClickFix-landing-page.webp)

The fake website displayed a CAPTCHA verification prompt as part of the ClickFix technique. The verification process guided victims into executing a malicious PowerShell command, which downloaded and launched the ScreenConnect payload.

![Fig6](/assets/images/posts/Clickfix/Fig10-ClickFix-instructions.webp)

Once installed, ScreenConnect provided attackers with remote access to the victim's system, allowing them to steal data, deploy additional malware, or maintain persistent access.

---
---

# MITRE ATT&CK Mapping

| Tactic                | Technique                                                | ID        |
| --------------------- | -------------------------------------------------------- | --------- |
| Initial Access        | Phishing                                                 | T1566     |
| Execution             | Command and Scripting Interpreter: PowerShell            | T1059.001 |
| Execution             | Command and Scripting Interpreter: Windows Command Shell | T1059.003 |
| Defense Evasion       | User Execution                                           | T1204     |
| Command and Control   | Application Layer Protocol                               | T1071     |
| Ingress Tool Transfer | Tool Transfer                                            | T1105     |

# Detection Opportunities

Organizations should monitor for:

* Unusual use of `powershell.exe`, `cmd.exe`, or `wt.exe` launched by users shortly after opening emails or visiting websites.
* Clipboard activity followed by command execution.
* PowerShell commands downloading content from external sources.
* Connections to newly registered or suspicious domains.
* Execution of remote access tools or information stealers shortly after user interaction.

# Indicators of Compromise (IOCs)

### Domains

* `access-ssa-gov[.]es`
* `reports_528647.html` (phishing attachment)

### Malware Families

* DarkGate
* ScreenConnect

### Suspicious Activity

* User-initiated execution of PowerShell or Terminal commands copied from web pages.
* Downloads initiated through command-line tools or scripts.
* Unexpected outbound connections to attacker-controlled infrastructure.

# Mitigation & Prevention

* Train users to never execute commands provided by websites or emails.
* Block or restrict PowerShell usage where possible.
* Enable logging for PowerShell and command-line activity.
* Monitor and restrict the use of remote management tools.
* Use email security solutions to detect phishing attachments and malicious links.
* Implement web filtering to block access to suspicious or newly registered domains.
* Deploy EDR solutions capable of detecting malicious script execution and abnormal process behavior.

---

# Conclusion

ClickFix attacks demonstrate how modern threat actors are increasingly relying on social engineering rather than technical exploits to compromise systems. By convincing users to execute malicious commands themselves, attackers can bypass traditional security controls and successfully deliver malware or remote access tools.

The growing adoption of this technique by multiple threat actors highlights the importance of user awareness, behavioral monitoring, and strong endpoint security controls to reduce the risk of compromise.

---

## References

- Microsoft Security Blog - Think before you Click(Fix): Analyzing the ClickFix social engineering technique.
- MITRE ATT&CK Framework 
