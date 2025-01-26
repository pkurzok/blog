+++
date = '2025-01-25T22:39:43+01:00'
draft = true
title = 'Grandfathering'
+++

# Grandfathering

I recently changed the business model of my app [PlayTales](https://apps.apple.com/us/app/playtales/id6444850972) from paying upfront to free with in-app purchase. A process also known as grandfathering.

I got it completely wrong and it turned into a massive headache and patch release on day 1. Mainly because of what I think is terribly misleading documentation on Apple's part.

<!--more-->



<!--![A Picture of a smiley](images/image.png)-->


[Supporting business model changes](https://developer.apple.com/documentation/storekit/supporting-business-model-changes-by-using-the-app-transaction)


[originalAppVersion](https://developer.apple.com/documentation/storekit/apptransaction/originalappversion)

> The string value contains the original value of the CFBundleShortVersionString for apps running in macOS, and the original value of the CFBundleVersion for apps running on all other platforms.

Fair

> In the sandbox testing environment, the originalAppVersion value is always 1.0.


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