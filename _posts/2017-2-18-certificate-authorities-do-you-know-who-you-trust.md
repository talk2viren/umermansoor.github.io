---
layout: post
title: Certificate Authorities - Do You Know Who You Trust?
comments: True
excerpt_separator: <!--more-->
---

HTTPS (aka *HTTP over the secure [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) protocol*) provide a secure communication channel between web browsers and servers to guard against [man-in-the-middle attacks](https://www.owasp.org/index.php/Man-in-the-middle_attack). Although researchers have identified and reported a [few](https://drownattack.com/) [vulnerabilities](http://heartbleed.com/), TLS is still the best option out there and all [websites should be using it](https://codeahoy.com/2017/01/18/if-your-site-isnt-using-https-you-are-doing-it-wrong/).

<!--more-->

Arguably, the most famous TLS fiasco was not a vulnerability but an enormously miscalculated and incompetent attempt to increase ad revenues by a top selling laptop manufacturer. "[Lenovo incident / scandal](http://www.slate.com/articles/technology/bitwise/2015/02/lenovo_superfish_scandal_the_result_of_evil_or_incompetence.html)" got its start when Lenovo thought it would be a brilliant idea to pre-install an adware ([Superfish](https://en.wikipedia.org/wiki/Superfish)) on their laptops to inject ads on webpages, both encrypted and non-encrypted. To allow Superfish to view and alter encrypted traffic, they pre-loaded its [self-signed](https://en.wikipedia.org/wiki/Self-signed_certificate), [root certificate](https://en.wikipedia.org/wiki/Root_certificate) on their laptops. By doing this, Lenovo let Superfish become man-in-the-middle and allowed it to view and alter traffic without the user ever knowing. If that was the end of the story, it might not have been all that terrible. However, by installing the self-signed root certificate, **which used the same private key on all laptops**, Lenovo exposed sensitive and confidential communication of their users to attackers or eavesdroppers connected to the same WiFi. Communication including emails, bank transactions, messages, and passwords were all exposed. After [users complained](https://forums.lenovo.com/t5/Security-Malware/Potentially-Unwanted-Program-Superfish-VisualDiscovery/td-p/1794457) and there was public outcry, Lenovo finally acknowledged that it "[messed up](http://www.pcworld.com/article/2886690/lenovo-cto-admits-company-messed-up-and-will-publish-superfish-removal-tool-on-friday.html)" and apologized to users for betraying their trust. It quickly dumped Superfish. Microsoft stepped in and provided an update of Windows Defender to remove Superfish. I'm happy to report that Superfish is dead. (*So long, and no-thanks for the fish.*)

![lenovo-super-fish]({{ site.url }}/img/lenovo-super-fish.png)

It is not always the incompetence of laptop vendors - user trust has been violated intentionally for [malicious purposes](https://thenextweb.com/insider/2015/04/02/google-to-drop-chinas-cnnic-root-certificate-authority-after-trust-breach/) as well:

> a Chinese certificate authority issued valid security certificates for a number of domains, including Google’s, without their permission, which resulted in a major trust breach in the crypto chain.
>
CNNIC had delegated its authority to Egyptian intermediary MCS Holdings to issue the certificates in question and the company installed it in a man-in-the-middle proxy internally.

If China feels far away, [Symantec fired its employees](http://www.itpro.co.uk/security/25315/symantec-employees-fired-over-fake-security-certificates) after it discovered they issued fake google security certificates.

As a user, you can protect your communication and your privacy by **never ignoring security warnings from your web browser**. Web browsers, especially Chrome and Firefox, do a pretty good job of recognizing potential threats and warning users, so don't skip through unless you are absolutely sure what you are doing. They also have a **"[Removal of Trust](https://www.chromium.org/Home/chromium-security/root-ca-policy#TOC-Removal-of-Trust)"** policy where they would distrust a root certificate authority if it is compromised, even if it is trusted by the operating system. If you like to see which root certificate authorities are trusted by your browser, [this blog has good information](https://certsimple.com/blog/control-the-ssl-cas-your-browser-trusts).

See you next time!
