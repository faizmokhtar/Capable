<center><img src="https://user-images.githubusercontent.com/1390908/41822361-a14fafd2-77ee-11e8-8aa6-f18960566d0c.png" width="400">
</center>

---
[![Build Status](https://app.bitrise.io/app/7596a076a75ab2ab/status.svg?token=3kpsJB-PR0sBLRF8NYrwhg)](https://www.bitrise.io/app/7596a076a75ab2ab)
![Platforms](https://img.shields.io/cocoapods/p/Capable.svg)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg)](https://github.com/Carthage/Carthage)
[![Cocoapods compatible](https://img.shields.io/cocoapods/v/Capable.svg)](https://cocoapods.org/pods/Capable)
![SPM](https://img.shields.io/badge/Swift%20Package%20Manager-macOS-blue.svg)
![Documentation](Documentation/badge.svg)

Have you ever thought about adopting accessibility features within you apps to gain your user base instead of spending a lot of time implementing features no-one really ever asked for? 

Most of us did, however there has never been an easy way to tell if anyone benefits from that. Adjusting layouts to be usable for people with low vision can be quite complex in some situations and tracking the user's accessibility settings adds a lot of boilerplate code to your app.

What if there was a simple way to figure out if there's a real need to support accessibility right now. Or even better, which disability exists most across your user base.

Check out the *Example.xcworkspace* to get a quick overview.

## Features

* Get the user's accessibility settings
* Send it with your favorite analytics SDK such as Fabric Answers, App Center Analytics, or Firebase Analytics
* Get notified about any changes
* Use dynamic type with custom fonts by using one line of code without caring about the OS version the user is running on

## Installation

There are currently four different ways to integrate Capable into your apps.

### CocoaPods

```ruby
use_frameworks!

target 'MyApp' do
  pod 'Capable'
end
```

### Carthage

```ruby
github "chrs1885/Capable"
```

### Swift Package Manager (macOS)

```ruby
dependencies: [
    .package(url: "https://github.com/chrs1885/Capable.git", from: "0.1.0")
]
```

### Manually

Simply drop `Capable.xcodeproj` into your project. Also make sure to add
`Capable.framework` to your app’s embedded frameworks found in the General tab of your main project.

## Usage

### Register for (specific) accessibility settings

Firstly, you need to import the Capable framework in your class by adding the following import statement:

```swift
import Capable
```

There are two different ways to initialize the framework instance. You can either set it up to consider all accessibility features

```swift
let capable = Capable()
```

or by passing in only specific feature names

```swift
let capable = Capable(with: [.largerText, .boldText, .shakeToUndo])
```

You can find a list of all accessibility features available on each platform in the [accessibility feature overview](#accessibility-feature-overview) section.

### Get & send accessibility status

If you are interested in a specific accessibility feature, you can retrieve its current status as follows:

```swift
let capable = Capable()
let isVoiceOverEnabled: Bool = capable.isFeatureEnable(feature: .voiceOver)
```

To get a dictionary of all features, that the `Capable` instance has been initialized with you can use:

```swift
let capable = Capable()
let statusMap = capable.statusMap
```
This will return each feature name (key) along with its current value as described in the [accessibility feature overview](#accessibility-feature-overview) section.

The `statusMap` object is compatible with most analytic SDK APIs. Here's a quick example of how to send your data along with user properties or custom events.

```swift
func sendMetrics() {
    let statusMap = self.capable.statusMap
    let eventName = "Capable features received"
    
    // App Center
    MSAnalytics.trackEvent(eventName, withProperties: statusMap)
    
    // Firebase
    Analytics.logEvent(eventName, parameters: statusMap)
    
    // Fabric
    Answers.logCustomEvent(withName: eventName, customAttributes: statusMap)
}
```

### Listen for settings changes

After initialization, notifications for all features that have been registered can be retrieved. To react to changes, you need to add your class as an observer as follows:

```swift
NotificationCenter.default.addObserver(
    self,
    selector: #selector(self.featureStatusChanged),
    name: .CapableFeatureStatusDidChange,
    object: nil)
```

Inside your `featureStatusChanged` you can parse the specific feature and value:

```swift
@objc private func featureStatusChanged(notification: NSNotification) {
    if let featureStatus = notification.object as? FeatureStatus {
        let feature = featureStatus.feature
        let currentValue = featureStatus.statusString
    }
}
```

### Dynamic Type with custom fonts (Capable UIFont extension)

Supporting Dynamic Type along with different OS versions such as iOS 10 and iOS 11 (watchOS 3 and watchOS 4) can be a huge pain, since both versions provide different APIs.

Capable easily auto scales system fonts as well as your custom fonts by providing one line of code:

```swift
let myLabel = UILabel(frame: frame)

// Scalable custom font
let myCustomFont = UIFont(name: "Custom Font Name", size: defaultFontSize)!
myLabel.font = UIFont.scaledFont(for: myCustomFont)

// or
myLabel.font = UIFont.scaledFont(name: "Custom Font Name", size: defaultFontSize)

// Scalable system font
myLabel.font = UIFont.scaledSystemFont(ofSize: defaultFontSize)

// Scalable italic system font
myLabel.font = UIFont.scaledItalicSystemFont(ofSize: defaultFontSize)

// Scalable bold system font
myLabel.font = UIFont.scaledBoldSystemFont(ofSize: defaultFontSize)
```

While these extension APIs are available on tvOS as well, setting the font size in the system settings is not supported on this platforms.

<a id="accessibility-feature-overview"></a> 
## Accessibility feature overview

The following table contains all features that are available AND settable on each platform.

|                            | iOS                | macOS                          | tvOS               | watchOS                        |
| -------------------------- |:------------------:| :-----------------------------:| :-----------------:| :-----------------------------:|
| .assistiveTouch            | :white_check_mark: |                                |                    |                                |
| .boldText                  | :white_check_mark: |                                | :white_check_mark: | &nbsp;:white_check_mark:**\*** |
| .closedCaptioning          | :white_check_mark: |                                | :white_check_mark: |                                |
| .darkerSystemColors        | :white_check_mark: |                                |                    |                                |
| .differentiateWithoutColor |                    | &nbsp;:white_check_mark:**\*** |                    |                                |
| .grayscale                 | :white_check_mark: |                                | :white_check_mark: |                                |
| .guidedAccess              | :white_check_mark: |                                |                    |                                |
| .increaseContrast          |                    | &nbsp;:white_check_mark:**\*** |                    |                                |
| .invertColors              | :white_check_mark: | &nbsp;:white_check_mark:**\*** | :white_check_mark: |                                |
| .largerText                | :white_check_mark: |                                |                    | &nbsp;:white_check_mark:**\*** |
| .monoAudio                 | :white_check_mark: |                                | :white_check_mark: |                                |
| .reduceMotion              | :white_check_mark: | &nbsp;:white_check_mark:**\*** | :white_check_mark: | :white_check_mark:             |
| .reduceTransparency        | :white_check_mark: | &nbsp;:white_check_mark:**\*** | :white_check_mark: |                                |
| .shakeToUndo               | :white_check_mark: |                                |                    |                                |
| .speakScreen               | :white_check_mark: |                                |                    |                                |
| .speakSelection            | :white_check_mark: |                                |                    |                                |
| .switchControl             | :white_check_mark: | &nbsp;:white_check_mark:**\*** | :white_check_mark: |                                |
| .voiceOver                 | :white_check_mark: | &nbsp;:white_check_mark:**\*** | :white_check_mark: | :white_check_mark:             |

*\* Feature status can be read but notifications are not available.*

While most features can only have a status set to **enabled** or **disabled**, the `.largerText` feature offers the font scale set by the user:

### iOS

* XS
* S
* M *(default)*
* L
* XL
* XXL
* XXXL
* Accessibility M
* Accessibility L
* Accessibility XL
* Accessibility XXL
* Accessibility XXXL
* Unknown

### watchOS
* XS
* S *(default watch with 38mm)*
* L *(default watch with 42mm)*
* XL
* XXL
* XXXL
* Unknown

## Ressources

* [Apple - WWDC Session Videos](https://developer.apple.com/videos/frameworks/accessibility/
)
* [Apple - Accessibility for Developers](https://developer.apple.com/accessibility/)

## License

Capable is available under the MIT license. See the [LICENSE](LICENSE) file for more info.