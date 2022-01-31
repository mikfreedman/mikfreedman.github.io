---
categories: null
comments: null
date: "2017-06-24T00:00:00Z"
description: null
image:
  credit: null
  creditlink: null
  feature: null
modified: null
share: null
tags: []
title: Running Ghost on Cloud Foundry
aliases: [/post/2017-06-24-ghost-on-cf]
---

At work, we often create blogs for our projects so that we can easily share out progress, and more importantly show people pretty pictures of our progress.

Often we will use a public blog service like [tumblr](https://www.tumblr.com/) to create a password protected blog, which is convenient and probably the right way to go.

Occasionally we will work with a client who cannot or will not use a public service like tumblr and so we need another way.

I have created a github repository: [mikfreedman/ghost-on-cf](https://github.com/mikfreedman/ghost-on-cf) which illustrates how you can deploy the popular blog software [Ghost](https://ghost.org/) onto [Cloud Foundry](https://www.cloudfoundry.org/), a PaaS that many of our clients are using.

This repository externalizes the necessary config that needs to change for Ghost, and actually installs the software itself as an npm module.

This is great, because the traditional approach to configuring Ghost recommends editing a bunch of files and adding random directories all over the place - which doesn't sound very [cloud native](https://pivotal.io/cloud-native).

## Cloud Nativeness

One of the main tenets of a [twelve-factor app](https://12factor.net/) is that we don't store things on the local filesystem, and we store configuration in the environment.

### Configuration

If you checkout [config.js](https://github.com/mikfreedman/ghost-on-cf/blob/master/config.js) you will see a bunch of code that supports environment based configuration like:

```
# Blobber Configuration
'ghost-blobber-service': {
  url: process.env.BLOBBER_API_URL,
  apiKey: process.env.BLOBBER_API_KEY
}
```

or 

```
 var creds = appEnv.getService("ghost-mysql").credentials;
```

The second example utilizes the nice [cfenv](https://github.com/cloudfoundry-community/node-cfenv) node module.

### File storage

By default, Ghost will store uploaded images on the local filesystem, which is also not great from a 12-Factor point of view, this can be fixed by using a [custom storage module](https://docs.ghost.org/docs/using-a-custom-storage-module).

This project is setup to use either S3 or a really basic storage service I wrote called [blobber](https://github.com/mikfreedman/blobber) (caveat emptor). The blobber service requires a custom storage module called [ghost-blobber-service](https://github.com/mikfreedman/ghost-blobber-service)

## More details

Follow the more detailed instructions in the [README](https://github.com/mikfreedman/ghost-on-cf/blob/master/README) and then immediately profit from a cf deployed blog.
