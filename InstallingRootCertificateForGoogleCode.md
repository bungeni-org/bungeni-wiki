# Introduction

On some servers attempting to checkout or update code from googlecode returns this prompt  :
```
...Reject, accept (t)emporarily or accept (p)ermanently? svn: PROPFIND request failed on ..
```

This page attempts to document the problem and the solution.


# Problem

This error appears because the openssl installation on ubuntu does not install any root certificate authorities. When  using secure checkout using svn it warns that there is no root authority certificate installed and provides a prompt. This can be a major problem when attempting a checkout using an automated script.


# Resolution

This is page lists the recognized root authorities on googlecode :

[What SSL certificates does Google Checkout accept?](http://code.google.com/support/bin/answer.py?hl=en&answer=57856)

We have to install a root certificate in svn :

```
sudo -s
wget -O equifax-secureca.cer http://www.geotrust.com/resources/root_certificates/certificates/Equifax_Secure_Certificate_Authority.cer
cp equifax-secureca.cer /etc/ssl/certs
chmod 444 /etc/ssl/certs/equifax-secureca.cer
```

The above downloads a root authority certificate and copies it to /etc/ssl/certs
Now we have to let subversion know where to look for the root authority certificate.

Edit the file `/etc/subversion/servers` and add the following entry right at the end. If there is already an entry for ssl-authority-files - add the downloaded certificate at the end.

```
ssl-authority-files = /etc/ssl/certs/equifax-secureca.cer;
```