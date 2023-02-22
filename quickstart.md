# Quickstart

AHI have written multiple one page example apps that are identical in appearance and functionality, that enable you, the developer, to view the code and test the functionality of the AHI SDK.

Repo: [https://github.com/ahi-dev/ahi-app-examples/tree/22.0/trunk](https://github.com/ahi-dev/ahi-app-examples/tree/22.0/trunk)

Each example app have been written for both iOS and Android, in multiple languages and frameworks. Here's the current list of example apps:

- Swift UIKit [Apple]
- SwiftUI [Apple]
- Kotlin [Android]
- Jetpack Compose [Android]
- React Native [Hybrid]
- Flutter [Hybrid]

AHI have intentionally focused on the minimum functionality required to use the SDK with the optional and auxiliary functions also provided for convenient integration.

While there are hardware and software requirements for using AHI SDK, this guide does not touch on that; this guide focuses on how to integrate and use the SDK, with code examples found in the example apps.

## Download

Run the following command to retrieve the AHI Examples to your local system:

```sh
git clone -b 22.0 https://github.com/ahi-dev/ahi-app-examples.git
```

## Before you begin

Before you begin developing, please ensure you have the following:

1.  A valid AHI Token - this should have been provided with access to the SDK.
2.  The Example App repository - you can access `git`.
3.  To access the SDK repo, we grant you access with some keys and tokens. We recommend that you add these to your system as environment variables. For Android development, your gradle file (Android) will read from these and fetch the SDKs. Open the Terminal (Mac) or Command Prompt (Windows) and access your system variables through vi or nano by typing `nano .bash` or `nano .zshrc` (or whichever profile your Terminal or Command Prompt uses) and add:

```sh
export AHI_MULTISCAN_AWS_URL=<URL WE PROVIDE>
export AHI_MULTISCAN_AWS_ACCESS_KEY=<ACCESS KEY WE PROVIDE>
export AHI_MULTISCAN_AWS_SECRET_KEY=<SECRET KEY WE PROVIDE>
```

4.  A mobile device, ideally running the latest version of iOS or Android.

Some additional information for your development purposes:

- All function names described are consistent in each app so that you can search for them as required across all example apps.
- In your own app, you will be required to add camera access permissions.
  - iOS: info.plist - `NSCameraUsageDescription = "We need to use your camera to take a scan.";`
  - Android: manifest.xml - `<uses-permission android:name="android.permission.CAMERA" />`
- Our SDK uses the Metric measurement format for all inputs and outputs. If you support Imperial or another measurement format, please ensure that you implement appropriate conversions to Metric before attempting scans.

## Running the app

The first thing you will need to do is download and install your packages.

- Swift/SwiftUI: Cocoapods - `pod install`
- Kotlin/Jetpack: Gradle - `build project`
- Flutter: `pub get`
  - iOS: `pod install`
  - Android: `build project`
- React Native: `npm install`
  - iOS: `pod install`
  - Android: `build project`

If you have installed the dependencies, open the project in your preferred IDE or text editor. Next, you will want to search for a property called `AHI_MULTI_SCAN_TOKEN` and assign the value to the token we supplied, which is of type `String`.

At this point, you should be able to run the app on a device and utilise the functionality.

## Stage one: Setup SDK

The first state of the app requires you to setup the AHI MultiScan to enable performing scans. The "Setup SDK" functionality can be automated by your app as a background function, however for the sake of demonstrating the requirement, it is an actionable event that is triggered by the tap gesture on the button.

The setup button performs two tasks:

- `setupMultiScanSDK()` - will call the AHI MultiScan SDK and pass in the `AHI_MULTI_SCAN_TOKEN`. If this succeeds, it will call:
- `authorizeUser()` - will call the AHI MultiScan SDK and pass in the `AHI_TEST_USER_ID`, `AHI_TEST_USER_SALT`, and `AHI_TEST_USER_CLAIMS` variables, which are of type `String`, `String`, and `Array<String>` respectively.

The `authorizeUser()` values that get passed in are hardcoded for the example app. When you integrate this functionality into your app, you will need to use your own:

- `USER_ID` - A unique user ID which will never change
- `SALT` - A salt string used for hashing your users data for greater privacy of their information.
- `CLAIMS` - An optional array of unique `String`s unique to the user (i.e. firebase creation timestamp).

## Stage two: FaceScan and Resources

If the Setup SDK process was successful, you will then see the second state containing two buttons:

1.  Start FaceScan
2.  Download Resources

Figure 3. After successfully setting up, you will have two options available to use.

### Start FaceScan

This button invokes a function called `startFaceScan()` which performs the following:

1. Defined a `Map` (`Dictionary` for iOS devs) of hardcoded user inputs which adhere to our SDK Schema. You will need to obtain these inputs from your users via some form of user input view.
2. A simple validation containing all the relevant rules which is a function called `areFaceScanConfigOptionsValid()` and returns a `boolean` value.
3. If the inputs are valid, proceed to initiate the scan.
4. FaceScan will take control of your view and perform a FaceScan.
    1.  The FaceScan can be cancelled and will return an Error (Exception for Flutter) and we have defined in code comments the values expected for the cancel event.
    2.  If the FaceScan returns an error, it's handled accordingly.
    3.  If the FaceScan completes successfully, results are printed in the logs, which adhere to the schema.

### Download Resources

This button invokes the function `downloadResources()` which performs three different functions asynchronously:

1. AHI MultiScan SDK function `downloadResourcesInBackground()` - this performs a `void` type function invoking the download of resources, which are required to complete a BodyScan.
2. AHI MultiScan SDK function `areAHIResourcesAvailable()` - this performs a `boolean` type function
    1. `true` - The resources are downloaded and available.
    2. `false` - The resources are not downloaded.
3. AHI MultiScan SDK function - `checkAHIResourcesDownloadSize()` - this performs a `long` type function which returns the size of the download in bytes. We have conveniently provided the method to convert it to MB, however it's up to you how you best use or display this information.

In the event that the resources have not been downloaded, these three functions have been wrapped in a very simple timed loop set to 30 seconds; these three functions will be called every 30 seconds until the `areAHIResourcesAvailable()` returns `true` . We recommend implementing a polling mechanism in your app to provide a better User Experience.

## State three: FaceScan and BodyScan

If the resources are available, you will then see the final state containing two buttons:

1.  Start FaceScan
2.  Start BodyScan

Since we have defined the FaceScan logic above, we will focus on the BodyScan.

### Start BodyScan

This button invokes a function called `startBodyScan()` which performs the following:

1. Defined a `Map` (`Dictionary` for iOS devs) of hardcoded user inputs which adhere to our SDK Schema. You will need to obtain these inputs from your users via some form of user input view.
2. A simple validation containing all the relevant rules which is a function called `areBodyScanConfigOptionsValid()` and returns a `boolean` value.
3. If the inputs are valid, we proceed to initiate the scan.
4. BodyScan will take control of your view and perform a FaceScan.
    1.  The BodyScan can be cancelled and will return an Error (Exception for Flutter) and we have defined in code comments the values expected for the cancel event.
    2.  If the BodyScan returns an error, it's handled accordingly.
    3.  If the BodyScan completes successfully, results are printed in the logs, which adhere to the schema.

## Required functionality

There are a minimum of three required functions in order to get scan results, but we recommend implementing the following five functions to perform all the available scan options.

### `setupMultiScanSDK()`

Uses your token to setup the SDK. Will return an error if the function fails or `null/nil` /empty `String` for success.

### `authorizeUser()`

Requires the three parameters containing a unique user ID, salt and optional array of claims. Will return and error if the function fails or `null/nil` /empty `String` for success.

### `downloadAHIResources()`

A void function that initialises the download of remote resources.

### `startFaceScan()`/`startBodyScan()`

These functions require valid user input that adheres to the respective schema before invoking a scan session. If successful, a `Map` of results which also adheres to the respective schema will be returned. If it failed, a relevant error will be returned.

## Additional functionality

The SDK provides several other functions that can enhance your user experience and the performance of your app.

### `areAHIResourcesAvailable()`

Will return a boolean informing you of the status of the resources availability. The resources are required for BodyScan.

### `checkAHIResourcesDownloadSize()`

Will return the download size in bytes as number value.

### `getBodyScanExtras()`

Requires an `Array` of valid body scan results schema containing the `raw` and `ent` values for a body scan and will return a `Map` object containing a `meshURL` with the path to a 3D avatar file.

### `getMultiScanStatus()`

Will return a status of `ready` , `disconnected` or `unavailable` .

### `getMultiScanDetails()`

Will return a `Map` containing information about your registered session with the AHI MultiScan

### `getUserAuthorisedState()`

Requires the current user ID and will return the authorised state for that user. This can be used to indicate whether you need to call `authoriseUser` .

### `deauthorizeUser()`

Will deauthorise the user from using the MultiScan service.

### `releaseMultiScanSDK()`

Will release the MultiScan SDK session from memory requiring `setupSDK()` to be called again if you need to use it.

### `setMultiScanPersistenceDelegate()`

Recommend using this to provide customised results based on historical user scan data. We refer to this process as "smoothing". You need to provide the previous BodyScan scan results in the format of an `Array<Map<String, Any>>` via your results database.

## Validation

Several functions that support validating your inputs to the SDK, which adhere to our Schemas. We recommend that you use these functions to ensure that your integration will provide the best Quality Assurance.

You will note validation occurs for:

- FaceScan input.
- BodyScan input.
- setMultiScanPersistenceDelegate results.

## Summary

The example apps demonstrate how to use the primary functionality of the AHI MultiScan SDK and how to rapidly integrate that functionality into your app.

We hope you had a smooth integration experience and we welcome any feedback you might have on the example app, the SDK or this guide.
