+++
date = '2025-01-25T22:39:43+01:00'
draft = false
title = 'Grandfathering'
+++

# Grandfathering

I recently changed the business model of my app [PlayTales](https://apps.apple.com/us/app/playtales/id6444850972) from paying upfront to free with in-app purchase. A process also known as grandfathering.

I got it completely wrong and it turned into a massive headache and patch release on day 1. Mainly because of what I think is terribly misleading [documentation](https://developer.apple.com/documentation/storekit/supporting-business-model-changes-by-using-the-app-transaction) on Apple's part.

<!--more-->

Well, at least there is documentation, you might say, there is even a whole article called [Supporting Business Model Changes](https://developer.apple.com/documentation/storekit/supporting-business-model-changes-by-using-the-app-transaction). Don't get me wrong, it's good that it exists.

The process itself is simple: You get something called an [AppTransaction](https://developer.apple.com/documentation/storekit/apptransaction) from from StoreKit. It has a property called [originalAppVersion](https://developer.apple.com/documentation/storekit/apptransaction/originalappversion). This is the version your customer originally purchased your app with.

Compare that version to the version of your new business model. If it is smaller, the customer is eligible for the free Pro tier. Simple, right?

There is even sample code:

```swift
let shared = try await AppTransaction.shared

if case .verified(let appTransaction) = shared {
    // Hard-code the major version number in which the app's business model changed.
    let newBusinessModelMajorVersion = "2" 

    // Get the major version number of the version the customer originally purchased.
    let versionComponents = appTransaction.originalAppVersion.split(separator: ".")
    let originalMajorVersion = versionComponents[0]

    if originalMajorVersion < newBusinessModelMajorVersion {
        // This customer purchased the app before the business model changed.
        // Deliver content that they're entitled to based on their app purchase.
    }
    else {
        // This customer purchased the app after the business model changed.
    }
}
```
Take a close look at this line of code in particular:
```swift
let versionComponents = appTransaction.originalAppVersion.split(separator: ".")
```
To me this reads like my original version is something like *1.3.5* and my new business model version will be *2.0.0*. And that's how I implemented and shipped it.

And that's where things went wrong. An hour after the release, the first support emails came in from customers complaining that they had to pay again - perfect. Luckily I set an analytics property for the original app version. To my surprise, they read 114, 121, ... and a few 219s. NOT 1.3.5 or 2.0.0 🤬

Turns out it's all in the [documentation](https://developer.apple.com/documentation/storekit/apptransaction/originalappversion). You just have to read it carefully and completely.

> The string value contains the original value of the CFBundleShortVersionString for apps running in macOS, and the original value of the CFBundleVersion for apps running on all other platforms.

It returns the build number, not the version number. 🤯

But why in God's name are they parsing and splitting it as if it were a version number in the sample code?!!

> In the sandbox testing environment, the originalAppVersion value is always 1.0.

Perhaps that is why... 

<!--![A Picture of a smiley](images/image.png)-->