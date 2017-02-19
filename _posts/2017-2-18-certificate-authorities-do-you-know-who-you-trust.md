---
layout: post
title: Certificate Authorities - Do You Know Who You Trust?
comments: True
excerpt_separator: <!--more-->
---

HTTPS (aka HTTP over the secure [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) protocol) provide a secure communication channel between web browsers and servers to guard against [man-in-the-middle attacks](https://www.owasp.org/index.php/Man-in-the-middle_attack). Although researchers have identified and reported a [few](https://drownattack.com/) [vulnerabilities](http://heartbleed.com/), TLS is still the best option out there and all [websites should be using it](https://codeahoy.com/2017/01/18/if-your-site-isnt-using-https-you-are-doing-it-wrong/).

<!--more-->

Arguably, the most famous TLS fiasco was not a vulnerability. In 2015, users discovered that [Lenovo had betrayed their trust](https://www.cnet.com/news/superfish-torments-lenovo-owners-with-more-than-adware/) after they silently pre-loaded a **very dangerous** adware on their laptops. The adware installed a universal self-signed certificate authority with the intention of collecting data about users and injecting ads into both encrypted and non-encrypted sites. If it was just an adware, it might not have been that terrible. However, it wasn't just *any* adware: **the certificate used the same private key and exposed user traffic to eavesdropping by anyone who was determined**. All user communication including emails, bank transactions, messages, and passwords got exposed. Lenovo acknowledged that it "[messed up](http://www.pcworld.com/article/2886690/lenovo-cto-admits-company-messed-up-and-will-publish-superfish-removal-tool-on-friday.html)", [apologized to users](https://redmondmag.com/Blogs/The-Schwartz-Report/2015/02/Lenovo-CTO-Finally-Apologizes.aspx) and removed the [Superfish](https://en.wikipedia.org/wiki/Superfish) adware from its devices.

![lenovo-super-fish]({{ site.url }}/img/lenovo-super-fish.png)


It is not always the computer manufacturers: certificate authorities that are trusted entities are known to [violate user trust as well](https://thenextweb.com/insider/2015/04/02/google-to-drop-chinas-cnnic-root-certificate-authority-after-trust-breach/):

> a Chinese certificate authority issued valid security certificates for a number of domains, including Google’s, without their permission, which resulted in a major trust breach in the crypto chain.
>
CNNIC had delegated its authority to Egyptian intermediary MCS Holdings to issue the certificates in question and the company installed it in a man-in-the-middle proxy internally.

If China feels far away, [Symantec fired its employees](http://www.itpro.co.uk/security/25315/symantec-employees-fired-over-fake-security-certificates) after it discovered they issued fake google security certificates.

I'm not a security expert so take my advice with a grain of salt. But you **should not ignore security warnings from your web browser or bypass them**. Web browsers do a pretty good job of recognizing potential threats to security and warn users. Operating systems and web browsers also allow users to see which certificates authorities they trust and give users an option to delete or mark them as untrusted.

![firefox-root-ca]({{ site.url }}/img/firefox-root-ca.png)

For more information on how to view which certificates your web browser trusts, [this blog has good information](https://certsimple.com/blog/control-the-ssl-cas-your-browser-trusts).
