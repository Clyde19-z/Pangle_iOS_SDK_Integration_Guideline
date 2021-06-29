# 6. Native Ads

## Introduction
Native ads are ad assets that are presented to users via UI components that are native to the platform. They're shown using the same classes you already use in your view, and can be formatted to match your app's visual design. When a native ad loads, your app receives an ad object that contains its assets, and the app (rather than the SDK) is then responsible for displaying them. This differs from other ad formats, which don't allow you to customize the appearance of the ad.

## Precondition
Create an app and native ad placement on Pangle platform
 - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Native Ads]
    - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)

## Native Ads Implementation

Broadly speaking,  there are two parts to successfully implementing native ads:

- Load an ad 
- Display the ad  

### Load an ad 

Native ads are loaded via `BUNativeAd` object.

##### Configure the BUAdSlot

Before you can load an ad, you have to initialize and configure the `BUAdSlot`. The following code demonstrates how to initialize a  `BUAdSlot`:

```objective-c
BUAdSlot *adslot = [[BUAdSlot alloc] init];
adslot.ID = @"Your_Ad_Placement_Id";
adslot.AdType = BUAdSlotAdTypeFeed;
adslot.imgSize = [BUSize sizeBy:BUProposalSize_Feed690_388];
```



##### Get notified of native ad events

- To be notified of events related to the native ad interactions, set the delegate property of the native ad:

  ```objective-c
  _nativeAd = [[BUNativeAd alloc] initWithSlot:adslot];
  _nativeAd.delegate = self;
  ```

- Then implement to `BUNativeAdDelegate` to receive the following delegate calls:

  ```objective-c
  #pragma mark - BUNativeAdDelegate
  - (void)nativeAdDidLoad:(BUNativeAd *)nativeAd view:(UIView *)view {
      _statusLabel.text = @"Ad loaded";
  }
  
  - (void)nativeAd:(BUNativeAd *)nativeAd didFailWithError:(NSError *_Nullable)error {
      _statusLabel.text = @"Ad loaded fail";
  }
  
  ...
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



##### Request the ad

Once your `BUAdSlot` is initialized and configured, call `loadAdData` method of `BUNativeAd`to request an ad:

```objective-c
_nativeAd = [[BUNativeAd alloc] initWithSlot:adslot];
_nativeAd.delegate = self;
_nativeAd.rootViewController = self;
[_nativeAd loadAdData];
```



### Display the ad  

Once you have loaded an ad, all that remains is to display it to your users. 

#####  Style native ad layouts

Lay out the `UIViews` that will display native ad assets. You can do this in the Interface Builder as you would when creating any other xib file or manual layout via code.



##### Set native ad assets to your customized native view

For the native ad you have got, there is a corresponding class: `BUNativeAd`.

There are two parts you can get assets after you get an ad.

- `BUMaterialMeta` , a property of `BUNativeAd`

  - You can get  adTitle、adDescription、buttonText of creative button，etc, via `BUMaterialMeta` 

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
    
        _titleLabel.text = _nativeAd.data.AdTitle;// adTitle
        _subTitleLabel.text = _nativeAd.data.AdDescription;// adDescription
        [_customButton setTitle:_nativeAd.data.buttonText?:@"CustomButton" forState:UIControlStateNormal];// buttonText
    }
    ```

  - You can get ad imageUrl via `BUImage` , a indirect property of `BUMaterialMeta` in `BUNativeAd`

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

  - 

- `BUNativeAdRelatedView`, an object need object `BUNativeAd` by call the `refreshData` method

  - You can get logoADImageViewdislikeButton, etc, via `BUNativeAdRelatedView`.

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
    
        self.nativeAdRelatedView = [[BUNativeAdRelatedView alloc] init];
        // required
        [self.nativeAdRelatedView refreshData:_nativeAd];
        // add logo
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

  - You can get videoAdView via `BUNativeAdRelatedView` as well.

    ```objective-c
    - (void)setNativeAd:(BUNativeAd *)nativeAd {
        _nativeAd = nativeAd;
    
        self.nativeAdRelatedView = [[BUNativeAdRelatedView alloc] init];
        // required
        [self.nativeAdRelatedView refreshData:_nativeAd];
      
        // video
        if (mode == BUFeedVideoAdModeImage ||mode == BUFeedADModeSquareVideo) {
            if (self.nativeAdRelatedView.videoAdView) {
              
              //add videoAdView
              UIView *videoView = _nativeAdRelatedView.videoAdView;
              videoView.translatesAutoresizingMaskIntoConstraints = NO;
              [_imageView addSubview:videoView];
              
              ...
            }
        }
    }
    ```



##### Get notified of videoAdView events

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






##### Update the size of native ad view 

You can get the scale of the image or video in two ways：

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


### Note
1. The ad data source is assigned in nativeAdsManagerSuccessToLoad need to call `registerContainer:withClickableViews:clickableViews`  click and bind the View,and to call  `refreshData:` to refresh data.
2. The refreshData: needs to be called after the next native ad is loaded.

### Resource
Demo: [GitHub](https://github.com/bytedance/Bytedance-UnionAD/blob/master/Example/BUDemo/App/Example/controller/BUDFeedViewController.m)

