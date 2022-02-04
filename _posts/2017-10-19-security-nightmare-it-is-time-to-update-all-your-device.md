---
title: 'Security nightmare - It is time to update all your device'
excerpt: Not a week goes by without new security vulnerabilities. The Wi-Fi vulnerability KRACK and the security chip vulnerability ROCA are some of the more severe ones, as they affect almost all devices.
permalink: /2017/10/19/security-nightmare-it-is-time-to-update-all-your-device/
categories:
  - Security
tags:
  - IoT
  - ROCA
  - RSA
  - TPM
  - WiFi
  - WPA2
---
What a week - security wise. Many vulnerabilities have been uncovered. You are most likely affected by one or more of these. Read on and start updating all your devices.

## Wi-Fi WPA2 vulnerability

![Hacked Wi-Fi]({{ site.baseurl }}/wp-content/uploads/2017/10/wifihacked-300x226.jpg){: .align-right}

Years ago, we all moved our Wi-Fi to WPA security protocol as WEP was deemed unsecure. Now vulnerability is found in WPA1/2 too, making it possible for malicious attackers to inspect and modify the tracking between computer and access point.

The vulnerability is known as KRACK (Key Reinstallation Attacks) and are in the Wi-Fi standard, so all devices are affected – laptops, access points, printers, phones… anything with Wi-Fi. The vulnerability is at client side, but many access points acts are repeaters etc., so do patch all Wi-Fi devices, otherwise the communication might be compromised.

Bleeping Computers are keeping a [list of affected devices](https://www.bleepingcomputer.com/news/security/list-of-firmware-and-driver-updates-for-krack-wpa2-vulnerability/) and firmware and driver updates to mitigate the problem.

[Microsoft fixed the issue on October 10th](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2017-13080) and rolled out the update on Patch Tuesday, so if you are keeping your device up-to-date, then you are all safe. Apple has not yet released a patch.

{% include video id="Oh4WURZoR98" provider="youtube" %}

[Read more about KRACK](https://www.krackattacks.com/).

## RSA key generation vulnerability

It does not sound intimidating, but this is a major vulnerability affecting Google Chromebooks, HP, Lenovo and Fujitsu PCs and laptops, SmartCards, routers, IoT devices – all devices that has a hardware secure chip (like TPM) from Infineon Technologies produced since 2012.

RSA keys are used to securely store secrets such as passwords, encrypt data (e.g. BitLocker) and generate certificate keys used in secure communication and sender/receiver attestation. It also affects [digital ID’s such as those used by the Estonian government](https://arstechnica.com/information-technology/2017/10/crypto-failure-cripples-millions-of-high-security-keys-750k-estonian-ids/), based on SmartCard technology.

The RSA generated prime numbers are not truly random, making it possible to crack the private key via the public key. Depending on the size of the RSA key, they estimate it takes:

* 512-bit RSA keys - 2 CPU hours (the cost of $0.06)
* 1024-bit RSA keys - 97 CPU days (the cost of $40-$80)
* 2048-bit RSA keys - 140.8 CPU years, (the cost of $20,000 - $40,000)

Based on an Intel E5-2650 v3@3GHz Q2/2014. 97 CPU days are nothing, as the crack can run in parallel and compute resources have become cheap with all the Cloud offerings.

To mitigate the ROCA security vulnerability requires firmware upgrades of the hardware secure chip. [Microsoft](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2017-13080) (Windows 7+) and [Google](https://sites.google.com/a/chromium.org/dev/chromium-os/tpm_firmware_update) and [others](https://char.gd/blog/2017/wifi-has-been-broken-heres-the-companies-that-have-already-fixed-it) has released patches.

Read more about the ROCA [CVE-2017-15361](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-15361) vulnerability.

## Securing my devices

It is time to update all my devices, but it is going to take time.

This is the list of devices I need to update: 5 Laptops, 1 Chromebook, 1 Xbox One, 1 Windows tablet, 2 iPads, 2 iPhones, 1 Android, 1 Windows Phone, 1 Ubiquity Access Point, 1 Sagemcom router (Owned by the ISP), 1 Amazon Echo, 1 Samsung TV, 1 Panasonic TV and countless IoT devices

Who am I kidding. Some of my devices are old (like my TVs), so the manufacturer will properly never release a patch.

![Comic missing patches]({{ site.baseurl }}/wp-content/uploads/2017/10/WPA2-vulnerability.jpg){: .align-center}

Like it is not worrisome enough, then a [privilege-escalation vulnerability in Linux kernel](https://thehackernews.com/2017/10/linux-privilege-escalation.html) was also discovered. This means even more stuff to update.
