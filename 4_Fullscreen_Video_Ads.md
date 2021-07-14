
# 4. Fullscreen Video Ads



## Introduction

Fullscreen Video ads, also known as the Interstitial Video Ads, are full-screen video ads with 15-30 seconds long that cover the interface of an app until closed by the user. It can be skipped after 5 seconds. They're typically displayed at natural transition points in the flow of an app, such as between activities or during the pause between levels in a game. When an app shows an interstitial ad, the user has the choice to either tap on the ad and continue to its destination or close it and return to the app.

**Note: 'Fullscreen Video Ads' mentioned here is named as 'Interstitial Video Ads' on Pangle's platform.**



## Precondition

Create an app and fullscreen video ad placement on Pangle platform

   - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)
      
  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Interstitial Video Ads]
    
      - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)



### Pangle Platform Parameter Setting：

- `Orienation`: Select the orientation of the video.




## FullscreenVideo Implementation

The main steps to integrate fullscreen video ads are:

- Load a fullscreen video ad.
- Register ad event callbacks.
- Display the ad.
- Preload a fullscreen video ad.

### Load a Fullscreen Video Ad

Loading a fullscreen ad is accomplished using the `loadAdData`  method on the  `BUFullscreenVideoAd` object. The loaded `BUFullscreenVideoAd` object is provided as a parameter in the `fullscreenVideoMaterialMetaAdDidLoad:`and `fullscreenVideoAdVideoDataDidLoad:` callback.



#### Create the Fullscreen Video Object

Fullscreen video ad is requested and shown by `BUFullscreenVideoAd` object, which needed to be created before loading ads.  The `BUFullscreenVideoAd` object requires one parameter: a String 'slotID' which is your ad unit ID when instantiated and initialized.

Requied：

| Field Definition | Field Name | Field Type | Remarks     |
|------------------|------------|------------|-------------|
| SlotID           | slot  ID   | NSString   | ad placement ID |

```objective-c
// It is required to generate a new BUFullscreenVideoAd object each time calling the loadAdData method to request the latest full-screen video ad. Please do not reuse the local cache full-scren video ad.
self.fullscreenVideoAd = [[BUFullscreenVideoAd alloc] initWithSlotID:@"Your_Ad_Placement_Id"];
```



#### Load a Fullscreen Video Ad 

Calling the `loadAdData` method on the `BUFullscreenVideoAd` object to load a fullscreen video ad.

```objective-c
[self.fullscreenVideoAd loadAdData];
```

**Note:**  It is required to generate a new `BUFullscreenVideoAd` object each time calling the`loadAdData ` method to request the latest full-screen video ad. Please do not reuse the local cache full-scren video ad.



### Register Ad Event Callbacks

In order to receive notifications for fullscreen video ad lifecycle and interactive events , you must implement the protocol **BUFullscreenVideoAdDelegate ** and assign it to the `delegate` property of the returned ad.

```objective-c
#import "BUDFullscreenViewController.h"
#import <BUAdSDK/BUAdSDK.h>

@interface BUDFullscreenViewController () <BUFullscreenVideoAdDelegate>
@property (nonatomic, strong) BUFullscreenVideoAd *fullscreenVideoAd;

@end

@implementation BUDFullscreenViewController

- (void)loadFullscreenVideoAdWithSlotID:(NSString *)slotID {
		...
    self.fullscreenVideoAd.delegate = self;
    [self.fullscreenVideoAd loadAdData];
}

#pragma mark BURewardedVideoAdDelegate
- (void)fullscreenVideoMaterialMetaAdDidLoad:(BUFullscreenVideoAd *)fullscreenVideoAd {

}

- (void)fullscreenVideoAdVideoDataDidLoad:(BUFullscreenVideoAd *)fullscreenVideoAd {

}

- (void)fullscreenVideoAd:(BUFullscreenVideoAd *)fullscreenVideoAd didFailWithError:(NSError *)error {

}

- (void)fullscreenVideoAdDidClickSkip:(BUFullscreenVideoAd *)fullscreenVideoAd {
    
}

- (void)fullscreenVideoAdDidClick:(BUFullscreenVideoAd *)fullscreenVideoAd {
    
}

- (void)fullscreenVideoAdDidClose:(BUFullscreenVideoAd *)fullscreenVideoAd {
    
}

- (void)fullscreenVideoAdDidPlayFinish:(BUFullscreenVideoAd *)fullscreenVideoAd didFailWithError:(NSError *_Nullable)error{
}

- (void)fullscreenVideoAdWillVisible:(BUFullscreenVideoAd *)fullscreenVideoAd{
    
}

- (void)fullscreenVideoAdDidVisible:(BUFullscreenVideoAd *)fullscreenVideoAd{
   
}

- (void)fullscreenVideoAdWillClose:(BUFullscreenVideoAd *)fullscreenVideoAd{
   
}

- (void)fullscreenVideoAdCallback:(BUFullscreenVideoAd *)fullscreenVideoAd withType:(BUFullScreenVideoAdType)fullscreenVideoAdType{
   
}

@end
```



#### BUFullscreenVideoAdDelegate Callback Instruction

|   BUFullscreenVideoAdDelegate Callback      |       Description        |
|---------------------------------------------------|--------------------------------------------------------------------------|
| fullscreenVideoMaterialMetaAdDidLoad:             | This method is called when video ad material loaded successfully.        |
| fullscreenVideoAd: didFailWithError:              | This method is called when video ad material failed to load.             |
| fullscreenVideoAdVideoDataDidLoad:                | This method is called when video cached successfully.                    |
| fullscreenVideoAdWillVisible:                     | This method is called when video ad slot will be showing.                |
| fullscreenVideoAdDidVisible:                      | This method is called when video ad slot has been shown.                 |
| fullscreenVideoAdDidClick:                        | This method is called when video ad is clicked.                          |
| fullscreenVideoAdWillClose:                       | This method is called when video ad is about to close.                   |
| fullscreenVideoAdDidClose:                        | This method is called when video ad is closed.                           |
| fullscreenVideoAdDidPlayFinish: didFailWithError: | This method is called when video ad play completed or an error occurred. |
| fullscreenVideoAdDidClickSkip:                    | This method is called when the user clicked skip button.                 |
| fullscreenVideoAdCallback: withType:              | This  method is used to get the type of fullscreen video ad.             |



### Display the Ad

Fullscreen video should be displayed during natural pauses in the flow of an app. Between levels of a game is a good example, or after the user completes a task.

To show a Fullscreen video ad, check the `fullscreenVideoMaterialMetaAdDidLoad:` callback to verify that if the ad is returned.  It is recommended to use the `fullscreenVideoAdVideoDataDidLoad:` method to verify if it's finished loading and cached successfully. Then call `showAdFromRootViewController:` to show a Fullscreen video. The `rootViewController` is needed to pass for this method.

```objective-c
if (self.fullscreenAd) {
   [self.fullscreenAd showAdFromRootViewController:self];
}
```

**Note**: To have a better user experience, we recommend to show `fullscreenVideoAd` after `fullscreenVideoAdVideoDataDidLoad:` callback is triggered. It means the video has been downloaded successfully.



### Preload a Fullscreen video Ad

A best practice is to load another fullscreen video in the `fullscreenVideoAdDidClose` method on `BUFullscreenVideoAdDelegate` so that the next fullscreen video starts loading as soon as the previous one is dismissed:

```objective-c
- (void)fullscreenVideoAdDidClose:(BUFullscreenVideoAd *)fullscreenVideoAd {
   // The original BUFullscreenVideoAd object can be set to nil in this callback
   // Start loading the next fullscreen video ad here.

}
```

**Note:** `BUFullscreenVideoAd` is a one-time-use object. This means that once a fullscreen video is shown, the object can't be used to load another ad. To request another fullscreen video, you'll need to create a new `BUFullscreenVideoAd` object.



## Test with test ads

Now you have finished the integration. If you wanna test your apps, make sure you use test ads rather than live, production ads. The easiest way to load test ads is to use test mode. It's been specially configured to return test ads for every request, and you're free to use it in your own apps while coding, testing, and debugging. 

Refer to the [How to add a test device?](https://www.pangleglobal.com/help/doc/5fba365f7b550100157bfc06) to add your device to the test devices on Pangle platform.


### Note
1. All the rootViewController parameters in Ad APIs must be provided to process ad redirects. In the SDK, all redirects use the **present** method of UIViewController. Therefore, make sure that the passed rootViewController parameters are **not null** and **do not have other present controllers**. Otherwise the present will fail because presentedViewController already exists.
2. In order to ensure smooth playback and display, we recommend to check the `fullscreenVideoAdVideoDataDidLoad:` callback before showing the ad to verify if the video is finished loading.
3. The `isAdValid` method has been deprecated since V3.3.0.0. Please do not use this field to verify whether the fullscreen video ad is valid.

### Resource
Demo: [GitHub](https://github.com/bytedance/Bytedance-UnionAD/blob/master/Example/BUDemo/BUDemo/App/Example/controller/BUDFullscreenViewController.m)

