# 6. Native Ads

## Introduction
Native ads allow you to customize the look and feel of the ads that appear in your app. You design the ads: how they look, where they’re placed, and how they work within your existing app design. This differs from other ad formats, which don't allow you to customize the appearance of the ad.



**Note: Pangle supports 4 forms outside the chinese mainland: Large image with 1.91:1 ratio、1280\*720 video、square image、 square video.**



## Precondition
Create an app and native ad placement on Pangle platform
 - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Native Ads]
    - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)

## Native Ads Implementation

Native ads are ad assets that are presented to users via UI components. It can be formatted to match your app's visual design. When a native ad loads, your app receives an ad object that contains its assets, and the app is then responsible for displaying them.

Broadly speaking, there are three steps to successfully implement Native Ads:

- Design your native ad layout.
- Load an ad. 
- Display the ad content in your app.

### Design your native ad layout

Before loading a native ad, you should have finished the design of a native ad. This can be done either with the Interface Builder or programmatically.

Interface Builder Example as below:

<img src="https://github.com/Clyde19-z/Pangle_iOS_SDK_Integration_Guideline/blob/main/nativeUI.png"/>

Then get the native example view from Nib file in the mainBundle:

```objective-c
BUDNativeExampleView *adView = [[NSBundle mainBundle]loadNibNamed:@"BUDNativeExampleView" owner:nil options:nil].firstObject;
```



### Load an ad 

Native ads are loaded via `BUNativeAd` object, which has the `loadAdData` method used to load native ads. The `BUNativeAd` object requires `BUAdSlot` 、 `RootViewController`、 `BUNativeAdDelegate` instances.The Native Ad Object is returned as a parameter in the  `nativeAdDidLoad:view:` callback.



#### Configure the BUAdSlot

Before you can load an ad, it is needed to initialize and configure the `BUAdSlot`. 

Requied：

| Field Definition | Field Name | Field Type | Remarks                                                      |
| ---------------- | ---------- | ---------- | ------------------------------------------------------------ |
| ID               | slot ID    | NSString   | ad placement ID                                              |
| AdType           | ad type    | NS_ENUM    | ad type                                                      |
| imgSize          | image size | BUSize     | image size, which is relevant to  NS_ENUM BUProposalSize, responsible for getting the view with the best results by using the predefined size in pixels. |

The following code demonstrates how to initialize a `BUAdSlot`:

```objective-c
BUAdSlot *adslot = [[BUAdSlot alloc] init];
adslot.ID = @"Your_Ad_Placement_Id";
adslot.AdType = BUAdSlotAdTypeFeed;
adslot.imgSize = [BUSize sizeBy:BUProposalSize_Feed690_388];
```



#### Set RootViewController

The root view controller handling ad actions is requred to be set to the `BUNativeAd`:

```objective-c
_nativeAd = [[BUNativeAd alloc] initWithSlot:adslot];
_nativeAd.rootViewController = self;
```



#### Register BUNativeAdDelegate callbacks

- To be notified of events related to the native ad interactions, set the delegate property of the native ad:

  ```objective-c
  _nativeAd.delegate = self;
  ```

- Then implement to `BUNativeAdDelegate` to receive the following delegate calls. The methods of the protocol are all optional.

  The following example shows how to implement the delegate protocol:

  ```objective-c
  @interface BUDFeedViewController () <BUNativeAdDelegate>
    
  @end
    
  @implementation BUDFeedViewController
  
      #pragma mark - BUNativeAdDelegate
      - (void)nativeAdDidLoad:(BUNativeAd *)nativeAd view:(UIView *)view {
          _statusLabel.text = @"Ad loaded";
      }
  
      - (void)nativeAd:(BUNativeAd *)nativeAd didFailWithError:(NSError *_Nullable)error {
          _statusLabel.text = @"Ad loaded fail";
      }
  
      ...
  @end
  ```

- BUNativeAdDelegate Callback Description

  This protocol includes some message that's sent to the delegate after calls `loadAdData`. You can get the results of the request via calls:

  | BUNativeAdDelegate Callback                       | Description                                                  |
  | ------------------------------------------------- | ------------------------------------------------------------ |
  | nativeAdDidLoad:view:                             | This method is called when native ad material loaded successfully. |
  | nativeAd:didFailWithError:                        | This method is called when native ad materia failed to load. |
  | nativeAdDidBecomeVisible:                         | This method is called when native ad slot has been shown.    |
  | nativeAdDidCloseOtherController: interactionType: | This method is called when another controller has been closed.                                                                   interactionType : open appstore in app or open the webpage or view video ad details page. |
  | nativeAdDidClick:withView:                        | This method is called when native ad is clicked.             |
  | nativeAd:dislikeWithReason:                       | This method is called when the user clicked dislike reasons.                                                                              Only used for dislikeButton in BUNativeAdRelatedView.h |
  | nativeAd:adContainerViewDidRemoved:               | This method is called when the Ad view container is forced to be removed. |

#### Request the ad

Once your `BUNativeAd` have been initialized and configured , call `loadAdData` method on`BUNativeAd ` object to request an ad:

```objective-c
[_nativeAd loadAdData];
```

**Sample code:**

```objective-c
// Your FeedViewController

#import "BUDFeedViewController.h"
#import <BUAdSDK/BUAdSDK.h>
#import "BUDNativeExampleView.h"

@interface BUDFeedViewController () <BUVideoAdViewDelegate,BUNativeAdDelegate>
  
@property (nonatomic, strong) UILabel *statusLabel;
@property (nonatomic, strong) BUNativeAd *nativeAd;
@property (nonatomic, strong) UIScrollView *scrollView;
@property (nonatomic, strong) BUDNativeExampleView *adView;

@end

@implementation BUDFeedViewController

- (void)viewDidLoad {
    [super viewDidLoad];
  }

- (void)loadAd:(UIButton *)sender {
    if (_adView) {
        [_adView removeFromSuperview];
        _adView = nil;
    }
  	// Configure the BUAdSlot
    BUAdSlot *adslot = [[BUAdSlot alloc] init];
    adslot.ID = @"Your_Ad_Placement_Id";
    adslot.AdType = BUAdSlotAdTypeFeed;
    adslot.imgSize = [BUSize sizeBy:BUProposalSize_Feed690_388];
    
    // initialize and configure the BUNativeAd object
    _nativeAd = [[BUNativeAd alloc] initWithSlot:adslot];
    _nativeAd.rootViewController = self;
    _nativeAd.delegate = self;
  
    // load the Ad with BUNativeAd
    [_nativeAd loadAdData];
}

#pragma mark - BUNativeAdDelegate

- (void)nativeAdDidLoad:(BUNativeAd *)nativeAd view:(UIView *)view {
    _statusLabel.text = @"Ad loaded";
}

- (void)nativeAd:(BUNativeAd *)nativeAd didFailWithError:(NSError *_Nullable)error {
    _statusLabel.text = @"Ad loaded fail";
}
...
  
@end

```



### Display the ad content in your app  

Once you have loaded an ad, all that remains is to display it to your users.(Though it doesn't necessarily have to do so immediately).



#### Set native ad assets to your customized native view

For the native ad you have accessed, there is a corresponding class: `BUNativeAd`.

There are two parts you can obtain assets  with the ad.

- `BUMaterialMeta` , a property of `BUNativeAd`

  - You can get adTitle、adDescription、buttonText of creative button, etc, via `BUMaterialMeta` .

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
    
        _titleLabel.text = _nativeAd.data.AdTitle;// adTitle
        _subTitleLabel.text = _nativeAd.data.AdDescription;// adDescription
        [_customButton setTitle:_nativeAd.data.buttonText?:@"CustomButton" forState:UIControlStateNormal];// buttonText
    }
    ```

  - You can get ad imageUrl via `BUImage` , a indirect property of `BUMaterialMeta` in `BUNativeAd`.

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
        // image
        if (mode == BUFeedADModeLargeImage || mode == BUFeedADModeSquareImage) {
            NSString *imageUrl = _nativeAd.data.imageAry.firstObject.imageURL;
            if (imageUrl) {
                ...
                self.imageView.image = [UIImage imageWithData:data];
            }
        }
     }
    ```

- `BUNativeAdRelatedView`, an object need to pass `BUNativeAd` when call the `refreshData` method on which.

  - You can add ad logo、ad label、dislikeButton, etc, via `BUNativeAdRelatedView`.The

    `Logo` and `ad label` are required to add to the `Native ads`.  `logoADImageView` is recommended, which contains logo and ad label. It needed to actively add dislikeButton in order to deal with the feedback and improve the accuracy of ad.

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
    
        self.nativeAdRelatedView = [[BUNativeAdRelatedView alloc] init];
        // required
        [self.nativeAdRelatedView refreshData:_nativeAd];
        // add Pangle AD logo
        UIView *logo = _nativeAdRelatedView.logoADImageView;
        [_imageView addSubview:logo];
        ...
        // add dislikeView
        UIButton *dislikeView = _nativeAdRelatedView.dislikeButton;
        [self addSubview:dislikeView];
        ...
        // required
        [_nativeAd registerContainer:self withClickableViews:@[self.customButton]];
    }
    ```

    **Note：**

    1. The `refreshData:` needs to be called on `BUNativeAdRelatedView` every time you get new datas in order to show ad perfectly.
    2. The `registerContainer:withClickableViews:clickableViews`  must be called on `BUNativeAd` , which provides data binding of native ads and reporting of click events, to register and bind the view to be clicked, including images, buttons, etc, otherwise we can't confirm whether the display is an ad. And The click events  (jump to ad page, download, call, etc.) registered by BUNativeAd are controlled by the SDK. 

    

  - You can get video view via `BUNativeAdRelatedView` as well.

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
    
        self.nativeAdRelatedView = [[BUNativeAdRelatedView alloc] init];
      
        // video
        if (mode == BUFeedVideoAdModeImage ||mode == BUFeedADModeSquareVideo) {
            if (self.nativeAdRelatedView.videoAdView) {
              
              //add videoAdView
              UIView *videoView = _nativeAdRelatedView.videoAdView;
              [_imageView addSubview:videoView];
              
              ...
            }
        }
    }
    ```

#### Get notified of videoAdView events

- To be notified of events related to the native ad `videoAdView` interactions if the ad inclueds a video, set the delegate property of the `BUVideoAdView`:

  ```objective-c
  adView.nativeAdRelatedView.videoAdView.delegate = self;
  ```

- Then implement to `BUVideoAdViewDelegate` to receive the following delegate calls:

  ```objective-c
  #pragma mark - BUVideoAdViewDelegate
  
  - (void)playerReadyToPlay:(BUVideoAdView *)videoAdView {
      
  }
  
  - (void)videoAdView:(BUVideoAdView *)videoAdView didLoadFailWithError:(NSError *)error {
    
  }
  
  ...
  ```

- BUVideoAdViewDelegate Callback Description

  | BUVideoAdViewDelegate Callback                      | Description                                                  |
  | --------------------------------------------------- | ------------------------------------------------------------ |
  | playerReadyToPlay:                                  | This method is called when videoadview ready to play.        |
  | videoAdView:didLoadFailWithError:                   | This method is called when videoadview failed to play.       |
  | videoAdView:stateDidChanged:                        | This method is called when videoadview playback status changed. |
  | playerDidPlayFinish:                                | This method is called when videoadview end of play.          |
  | videoAdViewDidClick:                                | This method is called when videoadview is clicked.           |
  | videoAdViewFinishViewDidClick:                      | This method is called when videoadview's finish view is clicked. |
  | videoAdViewDidCloseOtherController:interactionType: | This method is called when another controller has been closed. |




#### Update the size of native ad view 

You can get the aspect ratio of the image or video in two ways：

-  via `BUImage`in the `BUNativeAd.data.imageAry.firstObject`

```objective-c
- (CGFloat)getHeight:(CGFloat)width {
    
    CGFloat height = 140;
    BUImage *image = _nativeAd.data.imageAry.firstObject;
   
    CGFloat imageHeight = image.height?:0;
    CGFloat imageWidth = image.width?:1;

    CGFloat scale = imageHeight/imageWidth; 
    return (width - 40)*scale + height;
}
```

- via the ENUM `BUFeedADMode`

  ```objective-c
  typedef NS_ENUM(NSInteger, BUFeedADMode) {
    	...
      BUFeedADModeLargeImage  = 3,//  1.91:1, minSize:1200*628px, Wide Picture
      BUFeedVideoAdModeImage  = 5,//  1.77:1, 1280*720, Horizontal Video
      BUFeedADModeSquareImage = 33,// 1:1, minSize640*640px, Square Picture
      BUFeedADModeSquareVideo = 50,// 1:1, 640*640, Square Video
  };
  
  BUFeedADMode mode = nativeAd.data.imageMode;
  ```



## Test with test ads

Now you have finished the integration. If you wanna test your apps, make sure you use test ads rather than live, production ads. The easiest way to load test ads is to use test mode. It's been specially configured to return test ads for every request, and you're free to use it in your own apps while coding, testing, and debugging. 

Refer to the [How to add a test device?](https://www.pangleglobal.com/help/doc/5fba365f7b550100157bfc06) to add your device to the test devices on Pangle platform.



### Resource
Demo: [GitHub](https://github.com/bytedance/Bytedance-UnionAD/blob/master/Example/BUDemo/App/Example/controller/BUDFeedViewController.m)

