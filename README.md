Zoom SDK Guide
<br>
![GitHub downloads](https://img.shields.io/github/downloads/george-lim/zoom-sdk-guide/total.svg)
[![GitHub release](https://img.shields.io/github/release/george-lim/zoom-sdk-guide.svg)](https://github.com/george-lim/zoom-sdk-guide/releases)
[![GitHub issues](https://img.shields.io/github/issues/george-lim/zoom-sdk-guide.svg)](https://github.com/george-lim/zoom-sdk-guide/issues)
[![GitHub pull requests](https://img.shields.io/github/issues-pr/george-lim/zoom-sdk-guide.svg)](https://github.com/george-lim/zoom-sdk-guide/pulls)
[![license](https://img.shields.io/github/license/george-lim/zoom-sdk-guide.svg)](https://github.com/george-lim/zoom-sdk-guide/blob/master/LICENSE)
===============

Zoom's official MobileRTC documentation is fairly confusing and unclear. Additionally, it is written for Objective-C, so programmers wishing to write Swift programs may not know how to bridge MobileRTC into Swift. This project tries to give Swift programmers a better understanding of how to properly integrate MobileRTC into new applications.

1. [Bridging Headers](#bridging-headers)
1. [Privacy Setup](#privacy-setup)
1. [Link MobileRTC Framework and Bundle](#link-mobilertc-framework-and-bundle)
1. [Bitcode Setup](#bitcode-setup)

1. [Zoom Service Setup](#zoom-service-setup)

# Bridging Headers

## Create a new Swift project
Start off by creating a new swift project, just as you would normally. A blank, single view Swift project will be sufficient.

## Create a bridging header for your project
The first problem we have to tackle is the fact that the MobileRTC SDK is written in Objective-C. That means that naturally, if we are making a Swift application, we cannot simply link the framework into our project, call `import MobileRTC` and begin programming.

Instead, we have to create a bridging header for our project that allows us to interface with frameworks made in Objective-C. Go into Xcode and add a new file to your project (`Command+N`). Choose `Objective-C File` and call it whatever you'd like. After the file has been added to the project, Xcode will immediately ask `Would you like to configure an Objective-C bridging header?`. Choose `Create Bridging Header` and delete the Objective-C file.

Because we created a Swift project and attempted to add an Objective-C file, Xcode automatically guesses that our project might require connecting Objective-C code with Swift code, which prompts it to present an option to create a bridging header. By choosing to create a bridging header, Xcode will automatically setup and link the bridging header file to your project. Since we have no immediate use for the blank Objective-C file we created, we can remove it from our project entirely.

You should now have a new file in your project called `(PROJECT_NAME)-Bridging-Header.h`. Here, we will specify the frameworks that are written in Objective-C and expose them to Swift. **Any framework written here will be accessible to any Swift file without the need to import.**

# Privacy Setup
MobileRTC requires access to the user's camera, microphone, and photo library in order to function. We are required to explicitly provide a description for each privacy type in our `info.plist` file in order to prevent crashes in the app MobileRTC requests the permissions. Additionally, you should include the bluetooth peripheral usage and calendars usage descriptions as well to pass your app review. To do this, we first navigate to `info.plist` in our project.

## Privacy - Camera Usage Description
Add a new key in the `info.plist` file and type `Privacy - Camera Usage Description`. Xcode should automatically find the key you are looking for. In the value tab, type a description message that will be shown to users when your app requires the camera usage permission. The message used from the [Zoom Service sample app](https://github.com/george-lim/zoom-service/tree/master/Example) is `For people to see you during meetings, (PROJECT_NAME) needs access to your camera.`

## Privacy - Microphone Usage Description
Repeat the same process for the microphone usage description. The key is `Privacy - Microphone Usage Description` and a sample value is `For people to hear you during meetings, (PROJECT_NAME) needs access to your microphone.`

## Privacy - Photo Library Usage Description
Repeat the same process for the photo library usage description. The key is `Privacy - Photo Library Usage Description` and the sample value is `To share photos, (PROJECT_NAME) needs access to your photo library.`

## Privacy - Bluetooth Peripheral Usage Description
Repeat the same process for the bluetooth peripheral usage description. The key is `Privacy - Bluetooth Peripheral Usage Description` and the sample value is `To use Bluetooth devices to communicate, (PROJECT_NAME) needs access to your Bluetooth peripherals.`

## Privacy - Calendars Usage Description
Repeat the same process for the calendars usage description. The key is `Privacy - Calendars Usage Description` and the sample value is `To integrate Zoom meetings with your schedule, (PROJECT_NAME) needs access to your calendar.`

# Link MobileRTC Framework and Bundle
Download the [MobileRTC framework](http://hybridupdate.zoom.us/latest/rtc/iOS-MobileRTC-Stack-with-Device-only-framework-master.zip) and unzip the contents. Create a folder in your project called `lib`. Copy the contents of the `/lib/` folder from the uncompressed zoom-ios-mobilertc folder into your project directory `/lib/` you just created and copy the `Zoom iOS MobileRTC.pdf` file from `/doc/` into your project for reference. Delete `zoom-ios-mobilertc.zip` as well as the uncompressed folder.

## Importing MobileRTCResources.bundle
Navigate to your project directory and go to `/lib/`. With Xcode open, drag and drop the `MobileRTCResources.bundle` into your project and make sure that your project target is checked off.

## Importing MobileRTC.framework and all of its dependencies
In Xcode, click on your project target and go to the `General` tab on the top. Scroll down until you see the section `Linked Frameworks and Libraries`. Drag and drop the `MobileRTC.framework` file from `/lib/` in your project directory.

From Zoom's official documentation, MobileRTC requires a few other frameworks in order to function correctly. These frameworks include:

```
libsqlite3.tbd
libstdc++.6.0.9.tbd
libz.1.2.5.tbd
VideoToolbox.framework
CoreBluetooth.framework
```

Click on the little `+` button under the `Linked Frameworks and Libraries` section and use the search filter to find and add all of the required frameworks.

## Linking MobileRTC to Swift
In Xcode, navigate back to the bridging header file we created earlier. Add the line `#import <MobileRTC/MobileRTC.h>` to the header file and now, your project should recognize all MobileRTC functions, delegates, etc. You can test this out by going to `ViewController.swift` and typing `MobileRTC` in your `viewDidLoad()` function and seeing if Xcode presents any options for you.

# Bitcode Setup
By this point, if you have tried to build your project, you may have realized that Xcode throws a linker error at you. Upon closer inspection, it looks like MobileRTC does not contain bitcode, which makes Xcode unhappy. To fix this error, navigate to your project target and click on the `Build Settings` tab. Search `bitcode` and set the `Enable Bitcode` setting value from `Yes` to `No`. Building the project now should work without any problems.

**At this point, you have fully integrated MobileRTC into your program. You can refer to the `Zoom iOS MobileRTC.pdf` file from earlier to look at how to setup the API for MobileRTC and begin using the framework. The rest of the guide will focus on integrating Zoom Service into the app.**

# Zoom Service Setup
## Adding ZoomService.swift
Download the [ZoomService.swift](https://github.com/george-lim/zoom-service/blob/master/ZoomService.swift) file and add it to your project, making sure that your project target is checked off for the file.

## Authenticate SDK function call in AppDelegate.swift
In your `AppDelegate.swift` file, add the line `ZoomService.sharedInstance.authenticateSDK()` in your `didFinishLaunchingWithOptions` function.

## Next Steps
Navigate to the [Zoom Service](https://github.com/george-lim/zoom-service) GitHub project and follow the `Usage` guide to setup MobileRTC authentication and begin using the functions provided by Zoom Service.
