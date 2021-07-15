
# 5. Banner Ads

## Introduction
Banner ads occupy a spot within an app's layout, either at the top or bottom of the device screen.

**Note: Pangle only supports 300x250(point) and 320x50(point) for the traffic outside of Chinese Mainland at this time.**

## Precondition
Create an app and banner ad placement on Pangle platform
 - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Banner Ads]
    - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)
    
## Banner Ads Implementation

The main steps to integrate banner ads are:

- Load an banner ad.
- Register ad event callbacks.
- Display the ad.

### Load an banner ad

Banner ads are requested by `loadAdData` method on the  `BUNativeExpressBannerView` object. The `BUNativeExpressBannerView` object requires three parameters: a String 'slotID' , a  Object 'rootViewController' and a CGSize 'adsize' when instantiated and initialized. After that the property 'delegate' also need to be set to `BUNativeExpressBannerView` object before loading the ad. 



Once the loading succeeds, you need to display the banner ad by the `addSubview` method on the banner's superview.

#### Create the BUNativeExpressBannerView Object

Generally speaking，the `slotID` is your ad unit ID, the `rootViewController` is a place where the banner is located and the `adsize` , which should be passed in **point** for iOS , is the customized size that must be the same scale ratio as the pangle platform configuration of the banner view. 

Requied：

| Field Definition | Field Name | Field Type | Remarks                                                      |
| ---------------- | ---------- | ---------- | ------------------------------------------------------------ |
| slotID           | slot  ID   | NSString   | ad placement ID                                              |
| adSize           | ad size    | CGSize     | Ad size must be the same scale ratio as the pangle platform configuration, it should be passed in 'point' on iOS |

Optional：

| Field Definition | Field Name        | Field Type | Remarks                                   |
| ---------------- | ----------------- | ---------- | ----------------------------------------- |
| interval         | Rotation interval | NSInteger  | The interval of rotation was 30s to 120s. |

You can create the **BUNativeExpressBannerView** Object by this method as below, if the banner placemet you created on Pangle platform without the function of refresh ads automatically itself. 

```objective-c
self.bannerView = [[BUNativeExpressBannerView alloc] initWithSlotID:slotID rootViewController:self adSize:CGSizeMake(screenWidth, bannerHeigh)];
```

Oppositely use this method with the parameter **interval**.

```objective-c
self.bannerView = [[BUNativeExpressBannerView alloc]initWithSlotID:slotID rootViewController:self adSize:size interval:30];
```



#### load a banner ad

Call `loadAdData` on `BUNativeExpressBannerView` to load a banner ad. And make sure to have set the delegate property to be notified of events related to the native ad interactions.

```objective-c
self.bannerView.frame = CGRectMake(0, self.view.height - bannerHeigh, screenWidth, bannerHeigh);
self.bannerView.delegate = self;
[self.bannerView loadAdData];
```



### Register ad event callbacks

Once the banner ad is loaded, these ad event callbacks provided by the protocol **BUNativeExpressBannerViewDelegate** will be invoked at the corresponding time to notify its delegate.

```objective-c

#import "BUDExpressBannerViewController.h"
#import <BUAdSDK/BUAdSDK.h>

@interface BUDExpressBannerViewController () <BUNativeExpressBannerViewDelegate>

@end

@implementation BUDExpressBannerViewController

#pragma BUNativeExpressBannerViewDelegate
- (void)nativeExpressBannerAdViewDidLoad:(BUNativeExpressBannerView *)bannerAdView {
  
}

- (void)nativeExpressBannerAdView:(BUNativeExpressBannerView *)bannerAdView didLoadFailWithError:(NSError *)error {
  
}

- (void)nativeExpressBannerAdViewRenderSuccess:(BUNativeExpressBannerView *)bannerAdView {
  
}

- (void)nativeExpressBannerAdViewRenderFail:(BUNativeExpressBannerView *)bannerAdView error:(NSError *)error {
  
}

- (void)nativeExpressBannerAdViewWillBecomVisible:(BUNativeExpressBannerView *)bannerAdView {
  
}

- (void)nativeExpressBannerAdViewDidClick:(BUNativeExpressBannerView *)bannerAdView {
}

- (void)nativeExpressBannerAdViewDidCloseOtherController:(BUNativeExpressBannerView *)bannerAdView interactionType:(BUInteractionType)interactionType {
  
}

- (void)nativeExpressBannerAdView:(BUNativeExpressBannerView *)bannerAdView dislikeWithReason:(NSArray<BUDislikeWords *> *)filterwords {
    [bannerAdView removeFromSuperview];
    self.bannerView = nil;
}

- (void)nativeExpressBannerAdViewDidRemoved:(BUNativeExpressBannerView *)bannerAdView{
  
}

@end
```



### BUNativeExpressBannerViewDelegate Callback Instruction

|      BUNativeExpressBannerViewDelegate Callback      |      Description         |
|----------------------------------------------------------------------|--------------------------------------------------------|
| nativeExpressBannerAdViewDidLoad:                                   | This method is called when bannerAdView ad slot loaded successfully.          |
| nativeExpressBannerAdView: didLoadFailWithError:                    | This method is called when bannerAdView ad slot failed to load.               |
| nativeExpressBannerAdViewRenderSuccess:                             | This method is called when rendering a nativeExpressAdView successed.          |
| nativeExpressBannerAdViewRenderFail:error:                          | This method is called when a nativeExpressAdView failed to render.If the rendering fails due to network or hardware reasons, you can change the phone or the network environment. It is recommended to upgrade to the latest version of the Pangle platform.  |
| nativeExpressBannerAdViewWillBecomVisible:                          | This method is called when bannerAdView ad slot shows new ad.               |
| nativeExpressBannerAdViewDidClick:                                  | This method is called when bannerAdView is clicked.                          |
| nativeExpressBannerAdView:dislikeWithReason:                        | This method is called when the user clicked dislike button and chose dislike reasons.  |
| nativeExpressBannerAdViewDidCloseOtherController: interactionType:  | This method is called when another controller has been closed.  interactionType : open appstore in app or open the webpage or view video ad details page.    |
| nativeExpressBannerAdViewDidRemoved: | This method is called when the Ad view container is forced to be removed. |

### Display the ad

When `nativeExpressBannerAdViewRenderSuccess` callback is invoked, you can display the banner ad by calling `addView()` to add banner view to your app layout.

```objective-c
- (void)showBanner {
    [self.view addSubview:self.bannerView];
}
```

**Note: Banner ad could only be displayed after receiving `nativeExpressBannerAdViewRenderSuccess` event.**

```objective-c
- (void)nativeExpressBannerAdViewRenderSuccess:(BUNativeExpressBannerView *)bannerAdView {
 //After the callback method, the advertisement is displayed, which can ensure the smooth playing and display, and the user experience is better.
}
```

## Test with test ads

Now you have finished the integration. If you wanna test your apps, make sure you use test ads rather than live, production ads. The easiest way to load test ads is to use test mode. It's been specially configured to return test ads for every request, and you're free to use it in your own apps while coding, testing, and debugging. 

Refer to the [How to add a test device?](https://www.pangleglobal.com/help/doc/5fba365f7b550100157bfc06) to add your device to the test devices on Pangle platform.
