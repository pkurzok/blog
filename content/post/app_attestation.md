+++
date = '2025-02-24T16:45:23+01:00'
draft = true
title = 'App Attestation'
+++

## App Attestation

App Attestation is a service of the  [DeviceCheck framework](https://developer.apple.com/documentation/devicecheck) and, to quote the documentation, is used to *ensure that the requests your server receives come from legitimate instances of your application*.

### Why bother?
Today, it is easier than ever to intercept an application's traffic. Even on device without sophisticated setup. You can download Proxyman from the AppStore and read the HTTP traffic of all apps that don't implement any protection like certificate pinning or other measures.

<!--more-->

Once you see the requests being made, you can also access header information such as access keys or api tokens.

At the same time to access beeing tivial, the value of those tokens has never been higher. More and more applications use services from cloud providers like Google, Firebase, etc. Especially since the introduction of GPT services to provide access to generative models, these paid services have been heavily integrated. If you hijack an access token and use it outside of your application, someone can make your cloud service bill explode.

### How does it improve security?
App Attestation does not protect your app's communications from being intercepted. But it does protect your service from being reused or even misused. In technical terms, it protects you from so-called replay attacks.

Even if an attacker gains access to an access token and attestation payload, he cannot use it. Imagine that your requests can only be sent once.