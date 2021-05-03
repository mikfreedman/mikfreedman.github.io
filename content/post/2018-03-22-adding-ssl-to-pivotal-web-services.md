---
comments: null
date: "2018-03-22T00:00:00Z"
image:
  credit: null
  creditlink: null
  feature: null
modified: 2017-08-03 11:21:44 -0400
share: null
tags:
- pws
- cloudfoundry
title: Adding SSL to Pivotal Web Services (PWS)
---
If you have registered a custom domain that you would like to use with [Pivotal Web Services](https://run.pivotal.io/) (PWS) or any other Cloud Foundry instance, you may **also** want to use SSL, here's how.

These instructions were cribbed from the following places:

* [Routes and Domains](https://docs.run.pivotal.io/devguide/deploy-apps/routes-domains.html)
* [Pivotal SSL](https://docs.run.pivotal.io/marketplace/pivotal-ssl.html)
* [Certbot](https://certbot.eff.org/)
* [Let's Encrypt](https://letsencrypt.org/)

## Create a domain on PWS

```bash
cf create-domain space-name example.com
```
* You must be an org manager to create domain like this.

## Update your DNS records

I chose to only map a single subdomain to PWS, which used a CNAME record to alias the entry for my application, see [the instructions](https://docs.run.pivotal.io/devguide/deploy-apps/routes-domains.html) for more details.

```
app.example.com. CNAME app.cfapps.io # example DNS configuration
```

## Create and map your route

```bash
 cf create-route app example.com
 cf map-route application example.com --hostname app
 ```
 * Note that you **need** to have both this route, and the cf app domain route mapped for this to work. In the case of PWS this route will be *app.cfapps.io*. You may at this point want to update your application's reference to its URL (not always necessary).

## Setup an SSL Certificate.

Obtain an SSL Certificate for your domain from somewhere. 

I used the instructions at: [certbot](https://certbot.eff.org/) to obtain a free certificate. It turns out, the Certbot command line application has to be installed on the server itself to work correctly!

I temporarily pointed an *ANAME* record at a VM I spun up, and ran the *certbot* commandline tool on that VM in standalone mode. This allowed *certbot* to verify my ownership of the domain, and subsequently issue a certificate, which I downloaded to my local machine.

Alternatively, you can easily use certbot manually with a dns challenge from your local machine, like so:

```
sudo certbot -d example.com --manual --preferred-challenges dns certonly
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer None
Obtaining a new certificate
Performing the following challenges:
dns-01 challenge for blog.apbg.io

-------------------------------------------------------------------------------
NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
-------------------------------------------------------------------------------
(Y)es/(N)o: Y

-------------------------------------------------------------------------------
Please deploy a DNS TXT record under the name
_acme-challenge.blog.apbg.io with the following value:

<acme-challenge-key>

Before continuing, verify the record is deployed.
-------------------------------------------------------------------------------
Press Enter to Continue
Waiting for verification...
Cleaning up challenges
```

You may be able to script this refresh by checking out some of the solutions here: [How to use Let's Encrypt DNS challenge validation?](https://serverfault.com/questions/750902/how-to-use-lets-encrypt-dns-challenge-validation), how this works with Pivotal's SSL service is left as an exercise for the reader.

I then used the Pivotal SSL service to register this cert on PWS.

```
$ cf create-service ssl basic app.example.com # create SSL service
$ cf service app.example.com


Service instance: app.example.com
Service: ssl
Bound apps:
Tags:
Plan: basic
Description: Upload your SSL certificate for your app(s) on your custom domain
Documentation url: http://docs.run.pivotal.io/marketplace/pivotal-ssl.html
Dashboard: https://ssl-service.run.pivotal.io/instances/instance-id
```

I followed the Dashboard link in my browser, and uploaded my certificate file and private key that were generated earlier.

Once I had obtained my SSL alias address from the Dashboard, which looked something like: *app.example.com-190091.ssl.run.pivotal.io* I updated my *CNAME* record accordingly.

After waiting until everything delegated correctly, I was able to access my PWS hosted site securely using SSL, via the link: https://app.example.com. I was able to setup up http -> https redirection as well.
