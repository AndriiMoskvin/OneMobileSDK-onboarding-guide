# O2 Mobile SDK Tutorial – for iOS and tvOS <a name="head"></a>

Welcome to the O2 Mobile SDK (OMSDK or SDK). The purpose of this document is to provide you with everything you need to know to get started with and use the O2 Mobile SDK. The audience for this document is an experienced iOS Developer with in-depth knowledge of using Xcode, the Swift Programming Language, iOS API programming, and either CocoaPods or Carthage.

This document will describe basic concepts and then will link you to sample projects and code, that are kept up-to-date with the latest versions of the the developer tools, API, and language we support. Our iOS and tvOS samples use CocoaPods as the default dependency manager (except for any Carthage-specific samples). 

As always, we highly appreciate, welcome, and value all feedback on this documentation or the OMSDK in any way, shape, or form. If you have any suggestions for corrections, additions, or any further clarity, please don’t hesitate to email the [Video Support Team](mailto:video.support@oath.com).

## Table of Contents
[O2 Mobile SDK Tutorial – for iOS and tvOS](#head)
1. [TL;DR – Quick Start](#qs)
2. [Tutorial 0 – Library Integration](#t0)
3. [Tutorial 1 – Playing Videos](#t1)
	1. [Playing with AutoPlay on/off](#t11)
	2. [Showing thumbnails for videos](#t12)
	3. [Modifying metadata of the player(ANDROID ONLY)](#t13)
4. [Tutorial 2 – Customizing the Default Controls UX](#t2)
	1. [Changing colors](#t21)
	2. [Hiding Various Content Controls buttons](#t22)
	3. [Using the 4 Custom Sidebar buttons](#t23)
	4. [Using Custom Content Controls](#t24)
	5. [Mute/Unmute](#t25)
	6. [Full screen](#t26)
	7. [Closed Captioning / SAP Settings button](#t27)
5. [Tutorial 3 – Observing the Player](#t3)
	1. [Pausing or Resume Playback and Seeking](#t31)
	2. [Looping Playback](#t32)
	3. [LIVE, VOD, or 360°?](#t33)
	4. [Manually Hooking up Previous or Next Videos](#t34)
6. [Tutorial 4 – Error Handling in the SDK](#t4)
	1. [SDK Initialization Errors](#t41)
	2. [Player Initialization Errors](#t42)
	3. [Restricted Videos](#t43)
7. [Specific Notes for tvOS Apps](#tvos)
8. [Tutorial 5 – Playing videos on tvOS](#t5)
9. [Next Steps – Getting O2 Video/Playlist IDs into your apps](#go2)
10. [Next Steps – Controlling Ads via your O2 Portal Account](#co2)

## TL;DR – Quick Start <a name="qs"></a>
Want to dive right in, quickly and directly, you can jump here to get started.

### Tutorial 0 – Library Integration <a name="t0"></a>
<details>
	<summary>iOS</summary>

##### iOS framework supports following package managers:
* [Cocoapods](http://cocoapods.org)
* [Carthage](https://github.com/Carthage/Carthage)

##### Cocoapods

To integrate framework via Cocoapods you need to specify it in your `Podfile`
```ruby
pod 'OneMobileSDK-Xcode9.1'
```

Then run the following command:

```bash
$ pod install
```

##### Carthage

To integrate framework via Carthage you need to specify it in your `Cartfile`
```ruby
github "vidible/OneMobileSDK-releases"
```
Run `carthage update` to build the framework and drag the built `*.framework` into your Xcode project.
</details>

<details>
	<summary>Android</summary>

##### Gradle integration

In `allprojects.repositories` section of project `build.gradle` add link to our maven repository :

```gradle
allprojects {
    repositories {
        google()
        jcenter()
        maven { url 'https://raw.github.com/aol-public/OneMobileSDK-releases-android/maven/' }
    }
}
```

In `android.dependencies` section of module `build.gradle` add dependency to OMSDK library:

```gradle
apply plugin: 'com.android.application'

android {
    // ...
    
    dependencies {
        implementation 'com.aol.one.publishers.android:sdk:2.17'
        // ...
    }
}
```
</details>


### Tutorial 1 – Playing Videos <a name="t1"></a>
This tutorial shows you how to quickly init the OMSDK and play videos using all the default options and behaviors, with very little code.

To start using the OMSDK, you will first need to construct an instance of __OneSDK__.

<details>
	<summary>iOS</summary>
	
```swift
OneSDK.Provider.default.getConfiguration().onComplete(callback: {
	switch $0 {
		case .value(let player):
		...
		case .error(let error):
		...
	}
})
```
</details>
<details>
	<summary>Android</summary>
	
```java
new OneSDKBuilder(getApplicationContext())
	.create(new OneSDKBuilder.Callback() {
	@Override
	public void onSuccess(@NonNull OneSDK oneSDK) {
		...
	}
	@Override
	public void onFailure(@NonNull Exception e) {
		...
	}
});
```
</details>

<br>
Playing a single video, a list of individual videos, or videos from an O2 Playlist are all done the same way. The only difference between playing a single video or multiple videos is that the SDK strings multiple videos together, connects up the previous and next player controls UX buttons, and if AutoPlay is on - plays them straight through.

Using oneSDK instance from above you can build player for __videoId__

<details>
	<summary>iOS</summary>
	
```swift
OneSDK.Provider.default.getConfiguration().map(OneSDK.init).then {
	$0.getPlayer(videoID: videoId)
	}.onComplete(callback: handle)
```
</details>
<details>
	<summary>Android</summary>
	
```java
oneSDK.createBuilder()
        .buildForVideo(String videoId, Player.Callback callback);
```
</details>

<br>

or for an array of __videoIds__

<details>
	<summary>iOS</summary>
	
```swift
OneSDK.Provider.default.getConfiguration().map(OneSDK.init).then {
	$0.getPlayer(videoIDs: videoIds)
	}.onComplete(callback: handle)
```
</details>
<details>
	<summary>Android</summary>
	
```java
oneSDK.createBuilder()
        .buildForVideoList(String[] videoIds, Player.Callback callback);
```
</details>

<br>

or for __playlistId__

<details>
	<summary>iOS</summary>
	
```swift
OneSDK.Provider.default.getConfiguration().map(OneSDK.init).then {
	$0.getPlayer(playlistID: playlistId)
	}.onComplete(callback: handle)
```
</details>
<details>
	<summary>Android</summary>
	
```java
oneSDK.createBuilder()
        .buildForPlaylist(String playlistId, Player.Callback callback);
```
</details>

<br>
For further interaction with Player objects please refer to our examples:

<details>
	<summary>iOS</summary>
	...
</details>
<details>
	<summary>Android</summary>
	
##### [Play videos in  PlayerFragment](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/VideoInFragment)

##### [Play videos in PlayerView](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/VideoInView)
</details>

#### Playing with Autoplay on/off <a name="t11"></a>

By default, OMSDK plays videos with AutoPlay mode on. This means, that as soon as you construct a `Player`, the first video queues to play immediately (first, calling for an ad, of course). In this case, no further user action is required. As soon as the ad or the video is ready to play, it will.

To override this behavior you can build player setting desired Autoplay value:

<details>
	<summary>iOS</summary>
	
```swift
OneSDK.Provider.default.getConfiguration()
	.map(OneSDK.init)
	.then { $0.getPlayer(videoID: videoID, autoplay: true) }
	.onComplete(callback: handle)
```
</details>
<details>
	<summary>Android</summary>
	
```java
oneSDK.createBuilder()
        .setAutoplay(false)
        // .buildFor...
```
</details>

<br>
If AutoPlay mode is off, the user will have to tap the play button to start the playback process. Alternatively, you can programmatically do this by controlling the Player object.

#### Showing thumbnails for videos <a name="t12"></a>
By default, OMSDK does not show thumbnails embeded in videos but it has all the api for that.

Here is a  simple example how to show content thumbnails:

<details>
	<summary>iOS</summary>
...
</details>
<details>
	<summary>Android</summary>
	
##### [Load Content Thumbnails](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ContentLoadThumbnails)
</details>

#### Modifying video metadata for the player (ANDROID ONLY) <a name="t13"></a>
By default, OMSDK does all the work for you and provides you with Player object that is fully ready to use. As an alternative, you can check and/or modify all the video metadata before constructing player.

Here is a  simple example on changing video title:

<details>
	<summary>Android</summary>
	
##### [Override Content Metadata](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ContentOverrideMetadata)
</details>

### Tutorial 2 – Customizing the Default Controls UX <a name="t2"></a>

This tutorial shows you how to customize the default controls UX.

#### Changing colors <a name="t21"></a>

You can change colors of advertisement controls, content controls and LIVE video indicator dot.

Here is a  simple example how to set different colors to default controls:
<details>
	<summary>iOS</summary>
Ad/content color can be cahnged by setting `tintColor` to `PlayerViewController.contentControlsViewController.view`. For change LIVE dot color need to set `PlayerViewController.contentControlsViewController.props` like below:
```swift
playerViewController.contentControlsViewController.props = ContentControlsViewController.Props.player(ContentControlsViewController.Props.Player { player in
	player.item = ContentControlsViewController.Props.Player.Item.playable(ContentControlsViewController.Props.Player.Item.Controls { controls in
	controls.live.dotColor = UIColor.blue
	})
})
```
If dot should be same color as controls nothing must be set.
</details>
<details>
	<summary>Android</summary>
	
##### [Themed Controls](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ControlsThemedControls)
</details>

#### Hiding Various Content Controls buttons <a name="t22"></a>

You can change the look of the default content controls UX on a player-by-player basis to suit your app design needs. The elements that can be hidden include:

* ± 10 second skip buttons
* Previous and Next buttons
* Seekbar
* Video title
* Elapsed time
* Video duration
* Closed Captioning/SAP Settings button
* Picture-in-Picture (PiP) button
* AirPlay/Chromecast button

If you hide title and bottom element buttons such as CC/SAP, PiP, and AirPlay/Chromecast, the seekbar will fall down closer to the bottom of the video frame, to cover the gap usually left for those elements. See this tutorial for examples of how to hide/show these elements.

Here is a  simple example how to modify default controls:
<details>
	<summary>iOS</summary>
Need to cahnge `controls` props in following way:
```swift
playerViewController.contentControlsViewController.props = ContentControlsViewController.Props.player(ContentControlsViewController.Props.Player { player in
	player.item = ContentControlsViewController.Props.Player.Item.playable(ContentControlsViewController.Props.Player.Item.Controls { controls in
	controls.airplay = .hidden
    controls.pictureInPictureControl = .impossible
    ...
	})
})
```
</details>
<details>
	<summary>Android</summary>
	
##### [Modified Content Controls](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ControlsModifiedContentControls)
</details>

#### Using the 4 Custom Sidebar buttons <a name="t23"></a>

The Sidebar buttons are part of the default player controls UX and are there for you to add up to 4 different overlays/behaviors to your player. You provide the button graphics – icons for normal, selected, and highlighted modes, and you provide a handler to be called in the case of a button tap. The SDK will handle showing/hiding the buttons along with the other player controls.

Use this code to add your custom buttons to sidebar:

<details>
	<summary>iOS</summary>
	
```swift
playerViewController.contentControlsViewController.sidebarProps = [
            .init(
                isEnabled: true,
                isSelected: false,
                icons: SideBarView.ButtonProps.Icons(
                    normal: UIImage(named: "icon-fav")!,
                    selected: UIImage(named: "icon-fav-active")!,
                    highlighted: nil),
                handler: { }),
            ...]
```
</details>
<details>
	<summary>Android</summary>
<br>
When using PlayerFragment:
	
```java
playerFragment.getPlayerView().getContentControls()
	.getSidePanel().addView(View yourButton);
```

When using PlayerView:
	
```java
playerView.getContentControls()
	.getSidePanel().addView(View yourButton);
```
</details>

#### Using Custom Content Controls <a name="t24"></a>

OMSDK provides embeded controls for the player and you are free to customize them any way you like. If that is not enogh for you, you can make you own fully custom controls and hook them up to our player. 

Here is a  simple example how to implement and use custom content controls:

<details>
	<summary>iOS</summary>

Use this sample to see how to add custom controls. The `PlayerViewController` has `contentControlsViewController` which type is `ContentControlsViewController`. To create custom controls need to subclass `ContentControlsViewController` and set its instance to `contentControlsViewController`. `DefaultControlsViewController` is good example of this.
</details>
<details>
	<summary>Android</summary>
	
##### [Custom Content Controls](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ControlsCustomContentControls)
</details>

#### Mute/Unmute <a name="t25"></a>

You can easily control the mute state of the Player object directly.
<details>
	<summary>iOS</summary>
`Player` has API for mute/unmute functions. To use it need to call instance of `Player` inside `PlayerViewController` that has `mute()` and `unmute()` functions.
```swift
let player = playerViewController.player
if player?.props.isMuted == true {
    player?.unmute()
} else {
    player?.mute()
}
```
</details>
<details>
	<summary>Android</summary>
<br>
To mute:

```java
player.setMute(true);
```
To unmute:

```java
player.setMute(false);
```
Here is an example how to implement volume controls using the OMSDK's SideBar:
##### [Sidebar Volume Control](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ControlsSidebarVolume)
</details>

#### Full screen <a name="t26"></a>

If player should support full screen, you can refer to this example:
<details>
	<summary>iOS</summary>
[Full screen example iOS](https://github.com/aol-public/OneMobileSDK-playground-ios/tree/master/Examples/iOS%20Fullscreen)
</details>
<details>
	<summary>Android</summary>
	
##### [Sidebar Full Screen](https://github.com/aol-public/OneMobileSDK-examples-android/tree/master/ControlsSidebarFullscreen)
</details>

#### Closed Captioning / SAP Settings button <a name="t27"></a>

This new feature of the OMSDK is generally dependent on having this information in the HLS stream. There are ways to filter out what CC languages and SAP audio tracks are available. Also, there’s a way to control what the choices are for a given video. One reason to control this may be to implement a “sticky” closed captioning setting. By default, turning CC on only applies the current playing video. A next or previous video would not have CC on by default. If you wanted your app to support a sticky setting for this, you would do it yourself. This part of this tutorial will show you how to accomplish this.

### Tutorial 3 – Observing the Player <a name="t3"></a>

**insert tutorial link here**

This tutorial sample shows you how to observe just about everything you can observe from OMSDK `Player` objects. As you would suspect, many properties that can be observed, can also be set or manipulated.

##### Current Playback State and Position <a name="t31"></a>

Determining the current state of the `Player` is a key need for apps … most app-level video playback logic starts here. In addition to the play/pause state, also includes the current position. Once you can query for these property values, you can also programmatically modify them.

##### Looping Playback <a name="t32"></a>

If your app has some need to loop a `Player` (one video or many), such as running a kiosk-style interface, for example. This is an easy operation to accomplish with the OMSDK. Look in this example, to see how to determine when playback finishes, and how to reset the video index back to the first video and start it over.

##### LIVE, VOD, or 360°? <a name="t33"></a>

You may need to inspect some more metadata on the video, such as what type of video this is – LIVE, video on demand, or 360°. This tutorial sample shows how to inspect this. You may need to make certain app design or UX decisions accordingly, based on the type of video that’s currently playing.

##### Manually Hooking up Previous or Next Videos <a name="t34"></a>

There are many legitimate app UX circumstance, that can dictate the dynamicness of a video player – meaning, that not every app design will simply be setup to operate off fixed playlists or lists of videos. As such, the Player can be modified on the fly to dynamically handle what video is played when the previous or next buttons are tapped. This example tutorial has sample code that shows you precisely how to do this. However, be judicious with the usage of this behavior, and make sure it matches a natural flow of content for the user.

### Tutorial 4 – Error Handling in the SDK <a name="t4"></a>
**insert tutorial link here**

This tutorial sample shows you how to handle various different types of errors that can occur when using the OMSDK and how to catch and identify them. How you handle these in your app is up to you. The SDK is designed to either return a valid SDK or Player instance otherwise it returns an error. There is no middle ground. If you don’t get a valid instance, you should look at the error result instead to determine why. This section describes some common issues.

##### SDK Initialization Errors <a name="t41"></a>

For various reasons, the SDK may fail to initialize. The most common reason for this, is you’re trying to use the OMSDK without first having [onboarded your app’s bundle ID](##Onboarding\ your\ Apps\ for\ SDK\ Authentication). In this case, you’ll get an error that looks like something like this:
```
{
  "error": "Not found - com.company.ungregisteredapp"
} 
```

##### Player Initialization Errors <a name="t42"></a>

For various reasons, the `Player` may fail to initialize. 

##### Restricted Videos <a name="t43"></a>

Videos can be restricted for playback in two very distinct ways. The first is geo restricted content. The second is device restricted content. If you’re attempting to initialize a `Player` with content that’s restricted against your device or geolocation, that content is automatically filtered out. Only valid, playable video IDs are accepted, and have their metadata pulled into the `Player` instance. If you end up with no `Player` instance, it’s because there are no valid video IDs for it to operate on. So, you get an error this this effect.

## Specific Notes for tvOS Apps <a name="tvos"></a>

The OMSDK supports tvOS with the same source framework as iOS. Besides not having a finger to tap on the screen, the biggest difference for tvOS is that you cannot use the SDK’s default video player controls UX. Similarly, you cannot create your own custom video player controls UX. You must play videos using the standard built-in tvOS video player, with remote-control support.

Because there is no way to tap on the screen, you cannot access the ad URL. Additionally, tvOS has no support for web views – so there would be no consistent way to render the ad URL.

### Tutorial 5 – Playing Videos on tvOS <a name="t5"></a>
**insert tutorial link here**

This tutorial sample shows you how to do many of the same things as iOS as described above in [Tutorial 1](###Tutorial\ 1\ –\ Playing\ Videos), but for tvOS. In terms of the OMSDK, the biggest difference is that you cannot use the default Custom Controls UX with tvOS – you must use the built-in `AVPlayerViewController` controls. With this, you get direct access to the advanced Siri Remote control features, for example.

## Next Steps – Getting O2 Video/Playlist IDs into your apps <a name="go2"></a>
The OMSDK operates on O2 video and playlist IDs. That said, it is the application’s responsibility to dynamically acquire video IDs and/or playlist IDs either via your own CMS (content management system) or perhaps via a direct O2 Search API call. Since apps are generally dynamic in their content (video or otherwise), you need to figure out how to deliver these content IDs into your app, so they can be passed to the SDK to play against. Although unadvised, the easiest possible approach is to hardcode one or more playlist ID[s] into an app, and let those playlists dynamically change their content via the O2 Portal. The upside to this is you don’t need a CMS or further server communications on your end to get video information into your app, and thus to the SDK. The downside, is that if any of those IDs are ever deleted, the app will virtually be useless in terms of O2 video playback.

For more information about the O2 Search API, the O2 Portal, or creation and manipulation of playlists, please email the [Video Support Team](mailto:video.support@oath.com).

## Next Steps – Controlling Ads via your O2 Portal Account <a name="co2"></a>

**Need support team help here**. How to get set up for Portal, ads, etc. How to monetize with ads

