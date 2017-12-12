## Table of Contents
1. [What is the O2 Mobile SDK?](#what)
2. [Main SDK Features](#features)
3. [Why would I use the O2 Mobile SDK?](#why)
4. [Privacy Policy](#privacy)
5. [Starting Requirements](#requirements)
6. [Onboarding your Apps for SDK Authentication](#onboarding)
7. [High-Level Architecture Overview](#architecture)
8. [How the SDK works](#how)
9. [Default (Player) Controls UX](#ux)

## What is the O2 Mobile SDK? <a name="what"></a>
The O2 Mobile SDK (OMSDK or SDK) is a native iOS SDK with the sole purpose for playing and monetizing videos from the Oath O2 video platform in your app. The OMSDK is written in Swift and is delivered as a framework. You can include this in your app projects either via CocoaPods or Carthage. Currently, Swift Package Manager not supported on iOS or tvOS.

As part of playing videos, the OMSDK also handles video ads (pre-roll, mid-roll, and post-roll) and associated videos and ads playback and performance analytics. Analytics are focused on tracking what is played, how far it is played (e.g., deciles, quartiles), and details about the actual device or network. For more details on the analytics supported or to access the analytics data, you will work with the [Video Support Team](mailto:video.support@oath.com) to build reports that focus specifically on your app’s video and ads performance.

The SDK includes a complete default video player controls UX (user experience), which includes a limited albeit robust set of customization options. The controls implementation is fully open source, and the SDK architecture allows for you to include your own fully customized controls UX, should you not be interested in the built-in default one.

## Main SDK Features <a name="features"></a>
* Playback of one or more individual videos or a single playlist of videos
* Video playback of VOD (video on demand), 360°, and LIVE streaming video types
* Supports either .mp4 or .m3u8 (HLS) formats
* Video ads (VAST support of .mp4 format only)
* Tap an ad to access ad URL (more information) via an in-app-browser
* Full video and ads analytics
* Default video player controls UX (full source code open sourced)
* HLS support for multiple closed captioning (CC) and SAP audio languages
* Mute/Unmute support
* Automatic filtering of geo-restricted videos
* Complete apps control of the frame where videos play
* Native iOS Picture-in-picture support
* Apple AirPlay support

## Why would I use the O2 Mobile SDK? <a name="why"></a>
The O2 Mobile SDK is used to natively play O2 videos. If you have a native app, you should use the SDK. The main reason as to why you’d want to use the SDK, is because you get all the ads and analytics for free. The ads are important for monetization. Analytics are important for tracking your app’s video and video ads performance and usage. This helps you understand what your users are watching with your app, and how much.

There are several technical advantages to using the native OMSDK over a web player-based solution. We won’t go into these in depth in this document, but here are some of the advantages:
* Performance
* Mobile network awareness
* Frugal memory, thread, and device resource usage
* Security – webviews / embedded browsers are known weak points
* Fine-grained control, less limits
* Some platforms don’t have webviews (e.g., Apple TV)
* More customization options

## Advertising Info and User Tracking <a name="privacy"></a>
The O2 Mobile SDK does not track anything that is not related to playing videos or video ads. We use the IDFA (ID for advertisers) value and respect the user's settings for Limit Ad Tracking (iOS enforces this anyway). The device geolocation is determined by our backend video servers based on IP address, for the purposes of determining and filtering out content that is geo-restricted by content owners. The SDK does not explicitly use the built-in Location Services APIs, and thus does not require your users to grant access to device location data.

## Starting Requirements <a name="requirements"></a>
* **Apple Xcode 8+**
* **Swift 3.1 or 3.2***
* **CocoaPods or Carthage**
* **Mobile device running iOS 8 or later or AppleTV device running tvOS 9 or later**
* **Account in the O2 Portal, and access to Oath-ingested video content**
* **Onboarded application bundle ID**

**\* Note**: Swift 4 support is coming early in Q1 of 2018.

## Onboarding your Apps for SDK Authentication <a name="onboarding"></a>
In order for the OMSDK to authenticate itself for video playback within your app, we pass the containing app’s unique App Store bundle identifier to our back end service. You need to email the [Video Support Team](mailto:video.support@oath.com) to register all of your app bundle IDs for OMSDK usage. You can also register multiple bundle IDs against your same app entity. Possible reasons to do this, is to allow for a dev/test app bundle ID or an enterprise bundle ID, that can co-exist on a device alongside your production app. Also, both iOS and Android app bundle IDs can either be the same or different – for the same app. Registration not only authenticates your application, but it ensures your backend video and ads analytics are all configured properly. In addition, this registration information also defines all the video content your app is allowed to playback through the SDK.

The sample projects are all set up to use the following test-only bundle ID: `com.aol.mobile.one.testapp`

## High-Level Architecture Overview <a name="architecture"></a>
At a high-level, the OMSDK architecture is composed of the following components:
* SDK Core
* A set of Video Renderers that are either built-in (e.g., flat 2D and RYOT 360°) or external plugins (e.g., Verizon Envrmnt 360°, custom, etc.)
* O2 VRM (video rights management) VAST Ads Engine
* Video and Ads Analytics module
* Default video player controls UX implementation

Our modular approach makes it easy for us (or you) to add new renderers in the future, or for you to add your own custom video player controls UX implementation. Under the hood, we rely on the built-in iOS [`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer) to handle the actual video playback.

## How the SDK works <a name="how"></a>
At a very basic level, the OMSDK controls only the video frame. Because of this, you are completely in control of your app’s design and UX (look and feel). You can control whether videos play in a small view, in-place over a thumbnail image, or at full-screen. Your app also has complete control over device rotation, use of view/navigation controllers, scrollers, and any transitions between them. The SDK does not dictate any overall visual design or behavior on your app.

If you choose to use the SDK’s built-in default player controls UX implementation, then that part of the video UX is imposed on you. All controls rendering is also done within the frame you provide for the video. Regardless of which controls UX you use, we currently do not allow any customization or overriding of the ads playback UX (which is different from the normal video playback UX), so that visual interface is dictated, and you cannot override it. Future customization options are planned here.

To play a video, you follow these very basic steps:
1. Initialize an instance of the OMSDK
2. Initialize a new `Player` object with a video ID
3. Attach it to a view controller
4. Play it

That’s it!

Behind the scenes, what happens is this … the initialization of an instance of the SDK takes your app’s bundle ID, and passes it to our back-end micro services to authenticate your application for video playback within the O2 video platform. The server passes back all the necessary playback and authentication information down to that instance of the SDK, for use during it’s lifespan. When you construct a new `Player` object from that SDK instance, it communicates with our micro services to obtain all the necessary video metadata (e.g., thumbnails and video URLs, duration, etc.). This Player`` object will play and replay the associated video until deinitialized.

More specifically, before a video plays, the SDK’s Ads Engine tries to fulfill a pre-roll ad. While the request for an ad is being processed, the video starts buffering in the background. If an ad is returned in a reasonable amount of time, the `Player` plays the ad using the built-in ads UX. When the ad finishes, the video playback begins. If no ad is to be shown, or the ad request times out, the video playback begins directly.

The runtime circumstances and algorithm for getting an ad or not, are not in the scope of this documentation. Suffice to say, there are many considerations to this (e.g., content owner/seller rules, geolocation, frequency capping, etc.). For more information and details on how ads are served to the OMSDK, please email the [Video Support Team](mailto:video.support@oath.com).

**Note**: The SDK only operates with an active network connection – without it, you will not be able to do anything.

## Default (Player) Controls UX <a name="ux"></a>
The default player controls UX contains the following elements:
* Play/Pause/Replay button (with loading animation)
* ± 10 second skip buttons
* Previous and Next buttons
* Seekbar
* Video title
* Elapsed time
* Video duration
* LIVE indicator
* 360° View Orientation Compass / Orientation Reset button
* Closed Captioning/SAP Settings button
* Picture-in-Picture (PiP) button
* AirPlay button
* 4 app-custom sidebar buttons

This video controls implementation allows for a few runtime customizations, that you can set on a player by player basis. This includes:
* Setting the tint color for the controls (e.g., matching your app’s brand)
* Hiding various elements of the controls (useful for smaller view versus full-screen playback)
* Setting any of the 4 app-custom sidebar buttons

The built-in tint color of the default video player controls UX is <span style="color:magenta">pink/magenta</span>. This is deliberate; feel free to keep it or change it to mesh with your app’s design or brand theme. The built-in tint color of the ad’s UX is <span style="color:gold">yellow/gold</span>. This cannot be changed at this time. Because of this, we advise that you don’t tint your main controls yellow as well. You also don’t want to use black or gray, because of video contrast, your controls will not be very visible. White, on the other hand is fair game. There is a slightly darkened canvas that is layered above the video, but below the controls layer, that helps make the controls pop out more.

The player controls are shown under several well-established circumstances. This includes whenever the video is paused, such as before a video starts (with AutoPlay off) or while buffering, after any video finishes (with AutoPlay off), or after all videos linked to the player finish. They also will display (fade in) on demand whenever a video is tapped. If a video is actively playing, the controls will automatically hide (fade out) after a predetermined number of seconds. At any time the controls are shown, they can be quickly hidden by tapping on the video (not over a button, of course).

The default player controls UX implementation includes 4 optional app-specific sidebar buttons. You can set any or all of these to use as you see fit. This was built to allow for app-specific video overlay customization in anticipation for up to 4 new behaviors. Because these 4 sidebar buttons are built right into the default controls UX, they will automatically fade in/out with the rest of the video controls. There is no need to handle any of that logic or attempt to synchronize to the animation timings.

The complete implementation of the default player controls UX is open-source and provided to serve as an implementation sample of its own. Feel free to inspect it, copy it, modify it at will.

The default iOS Custom Controls UX implementation repo can be found here: 
[O2 Mobile SDK Controls for iOS](https://github.com/aol-public/OneMobileSDK-controls-ios)
