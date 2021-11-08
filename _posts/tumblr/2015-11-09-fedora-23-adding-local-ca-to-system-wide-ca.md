---
layout: post
title: Fedora 23 - Adding local CA to system-wide CA bundles
date: '2015-11-09T17:04:29+00:00'
tags: []
tumblr_url: https://broadcaststorm.tumblr.com/post/132894430653/fedora-23-adding-local-ca-to-system-wide-ca
---
I had the occasion to start trying out Fedora 23 and wanted to register my own Certificate Authority certificate system-wide. &nbsp;The root cause - my YUM repository was SSL-protected with a untrusted CA certificate. &nbsp;The&nbsp; **dnf** &nbsp;command produced the following error message (excerpted):

```
_Curl error (60): Peer certificate cannot be authenticated with given CA certificates for URL [Peer’s Certificate issuer is not recognized.]
```

The answer is clear - I need to get Fedora 23 to recognize and trust my local CA certificate. &nbsp;

## System CA Certificate Bundles

For many releases now, the **/etc/pki** &nbsp;directory has housed most things x509 certificate related. &nbsp;Fedora 23 is no different. &nbsp;Starting with Fedora 19, the [Shared Certificate Authority](http://bit.ly/1PyioKy) capability was introduced. This enhancement definitely helped&nbsp;centralize the management for trusting non-public certificate authorities. &nbsp;

**/etc/pki/ca-trust** &nbsp;now exists as the parent directory from which to add new CA certificates for reference globally. &nbsp;Two subdirectories exist:

- **/etc/pki/ca-trust/source** - The source for the existing CA bundle provided with Fedora (containing officially trusted CA’s by the Mozilla project) as well as locally trusted certificates. &nbsp;  
- **/etc/pki/ca-trust/extracted** &nbsp;- The dynamically generated trusted CA bundles for each service type (email, tbs, etc.). &nbsp;The certificate system generates the files in here.  

## Deploying local CA system-wide

First, you’ll need your CA’s public certificate exported in the PEM format. &nbsp;Without seeming too basic, I will point out that the filename should be fairly descriptive about the contents. &nbsp;For example:

**CA.Public–DOMAIN–Exp2016.pem**

There is little doubt what should be contained in that file. &nbsp;Having dealt with certificates for quite some time, even though the openssl suite can reveal all, this simply scheme saves a lot of time. &nbsp;Every now and then, you should use those tools just to make sure a junior admin ran the correct commands to generate that file though!

Second, place your local CA’s public certificate in the following location:

```
/etc/pki/ca-trust/source/anchors
```

Here’s the important part for NSS based systems (Firefox) - **make sure the file is world readable.** &nbsp; The _p11-kit-trust.so_&nbsp;replacement for _libnssckbi.so_&nbsp;would lead one to believe that the rebuilt CA files (next step) are all that is needed. &nbsp;After an hour of troubleshooting, it became clear that Firefox had to open them directly.

Finally, run the **update-ca-trust** &nbsp;command. &nbsp;The command produced no output for me after it ran. &nbsp;This is primarily used for the other certificate systems (see list of links below). &nbsp;

## Testing the changes

The original showstopper was package installs. &nbsp;So, fired up **dnf** &nbsp;commands and installations ran without an issue. &nbsp;But what about other services used on the new Fedora 23 system?

- **Firefox** - Yes! &nbsp;It worked just fine so long as you have the CA certificates world readable. &nbsp;Below are some links I ran across if you don’t want to mess with certificates at a system level:&nbsp;

  - For a single user (me!), you can follow these instructions from [nixCraft](http://bit.ly/1QehX9C). &nbsp;These instructions definitely resolved my issue even if it was unsatisfying. &nbsp;
  - For something more scalable (multiple users),&nbsp;you can try building a central cert8.db file via&nbsp;[http://bit.ly/1lgSKyQ](http://bit.ly/1lgSKyQ)

As I run across more services, I’ll post them here.

## Certificate Management Libraries/Systems

Taken from the Fedora 19 Feature Request page for Shared System Certificates

- PKCS#11 from P11-KIT
- NSS from LIBNSSCKBI.SO
- OpenSSL
- GnuTLS
- Java

The p11-kit system is designed to provide a simple Fedora management interface to build the trust files for each system listed above. &nbsp;To test the certificates added to the system, you can use the instructions underneath the Features&nbsp;“testing” link here -&nbsp;[http://bit.ly/1Y0yEXF](http://bit.ly/1Y0yEXF)

The system also comes with the following nifty command:

```bash
/usr/bin/trust [list|extract|extract-compat|anchor]
```

The **list** sub-command will list all the certificates and their type.

## Foot notes

1. My personal CA signing environment is not located on this F23 install  
2. This CA trust infrastructure is provided by the RPM **ca-certificates,** &nbsp; **p11-kit** &nbsp;and **p11-kit-trust**.
