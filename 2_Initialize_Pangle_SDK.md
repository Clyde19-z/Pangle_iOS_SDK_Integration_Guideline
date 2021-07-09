# 2. Initialize Pangle SDK



Initialize Pangle SDK asynchronously is supported **since the v3.6.0.0**. And there is a `BUAdSDKConfiguration` class,  through which any setup should be passed in the new initialization flow. Please call `startWithSyncCompletionHandler:`  or `startWithAsyncCompletionHandler:`on Class `BUAdSDKManager`  to initializes the SDK before you send any ad requests（ **the old initialization method still works in the new sdk**）. Init only need to be called once per app’s lifecycle, we **strongly recommend** to do this in the method `application:didFinishLaunchingWithOptions:` on app launch.



```objective-c
// The synchronize initialization method of Pangle
// @param completionHandler Callback to the initialization state of the calling thread
+ (void)startWithSyncCompletionHandler:(BUCompletionHandler)completionHandler;

// The asynchronize initialization method of Pangle
// @param completionHandler Callback to the initialization state of the non-main thread
+ (void)startWithAsyncCompletionHandler:(BUCompletionHandler)completionHandler;
```



You will be informed the result of the initialization by the block callback  `BUCompletionHandler`

```objective-c
typedef void (^BUCompletionHandler)(BOOL success,NSError *error);
```



**Note: Be sure to pass your AppID to Object `BUAdSDKConfiguration` before initializing the SDK after v3.6.0.0.**

Example as below:

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    // setUP BUAdSDK
    BUAdSDKConfiguration *configuration = [BUAdSDKConfiguration configuration];
  
    // This property must be set when integrating 'CN Pangle SDK' and 'Outside_CN Pangle SDK' into an APP, otherwise there is no need to set. You‘d better set Territory first if needed.
    configuration.territory = BUAdSDKTerritory_NO_CN;
    // GDPR: 0 close privacy protection, 1 open privacy protection. optional
    configuration.GDPR = @(0);
    // Coppa: 0 adult, 1 child. optional
    configuration.coppa = @(0);
  	// pass appID to initialize Pangle sdk
    configuration.appID = @"Your_App_Id";
  
#if DEBUG
  	// configure development mode. default BUAdSDKLogLevelNone
    configuration.logLevel = BUAdSDKLogLevelDebug;
#endif
  
    [BUAdSDKManager startWithAsyncCompletionHandler:^(BOOL success, NSError *error) {
        // Pangle SDK has been successfully initialized
        // load pangle ads after this method is triggered.
				if (success) {
            
        }
      	...
    }];
       
    return YES;
}
```



**Before v3.6.0.0**,  please call the method `setAppID:`  on Class `BUAdSDKManager` to initialize Pangle SDK directly.

Example as below:

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
   
    // initialize AD SDK
  
    // This property must be set when integrating 'CN Pangle SDK' and 'Outside_CN Pangle SDK' into an APP, otherwise there is no need to set. You‘d better set Territory first if needed.
    [BUAdSDKManager setTerritory:BUAdSDKTerritory_CN];
    // GDPR: 0 close privacy protection, 1 open privacy protection. optional
    [BUAdSDKManager setGDPR:0];
    // Coppa: 0 adult, 1 child. optional
    [BUAdSDKManager setCoppa:0]; 
    // pass appID to initialize Pangle sdk
    [BUAdSDKManager setAppID:@"Your_App_Id"];
  
#if DEBUG
    // Whether to open log. default is none.
    [BUAdSDKManager setLoglevel:BUAdSDKLogLevelDebug];
#endif
  

    return YES;
}
```



**Warning: Ads may be preloaded by the Pangle Ads SDK or mediation partner SDKs after initialization. If you need to obtain consent from users in the European Economic Area (EEA) or users under age, please ensure you do so before initializing the Pangle Ads SDK.**

### Interface Instruction
Currently, the `BUAdSDKConfiguration` Class provides the following method and property.

```objective-c
@interface BUAdSDKConfiguration : NSObject

+ (instancetype)configuration;

// This property should be set when integrating non-China and china areas at the same time,
// otherwise it need'nt to be set.you‘d better set Territory first,  if you need to set them
@property (nonatomic, assign) BUAdSDKTerritory territory;

// Register the App key that’s already been applied before requesting an ad from TikTok Audience Network.
// the unique identifier of the App
@property (nonatomic, copy) NSString *appID;

// Configure development mode. default BUAdSDKLogLevelNone
@property (nonatomic, assign) BUAdSDKLogLevel logLevel;

// the COPPA of the user, COPPA is the short of Children's Online Privacy Protection Rule,
// the interface only works in the United States.
// Coppa 0 adult, 1 child
// You can change its value at any time
@property (nonatomic, strong) NSNumber *coppa;

// additional user information.
@property (nonatomic, copy) NSString *userExtData;

// Solve the problem when your WKWebview post message empty,
// default is BUOfflineTypeWebview
@property (nonatomic, assign) BUOfflineType webViewOfflineType;

// Custom set the GDPR of the user,GDPR is the short of General Data Protection Regulation,the interface only works in The European.
// GDPR 0 close privacy protection, 1 open privacy protection
// You can change its value at any time
@property (nonatomic, strong) NSNumber *GDPR;

// Custom set the CCPA of the user,CCPA is the short of General Data Protection Regulation,the interface only works in USA.
// CCPA  0: "sale" of personal information is permitted, 1: user has opted out of "sale" of personal information -1: default
@property (nonatomic, strong) NSNumber *CCPA;

@property (nonatomic, strong) NSNumber *themeStatus;

/// Custom set the AB vid of the user. Array element type is NSNumber
@property (nonatomic, strong) NSArray<NSNumber *> *abvids;

// Custom set the tob ab sdk version of the user.
@property (atomic, copy) NSString *abSDKVersion;

// Custom set idfa value
// You can change its value at any time
@property (nonatomic, copy) NSString *customIdfa;

/**
 Whether to allow SDK to modify the category and options of AVAudioSession when playing audio, default is NO.
 The category set by the SDK is AVAudioSessionCategoryAmbient, and the options are AVAudioSessionCategoryOptionDuckOthers
 */
@property (atomic, assign) BOOL allowModifyAudioSessionSetting;

@end

NS_ASSUME_NONNULL_END
```

See SDK Demo Project or [GitHub](https://github.com/bytedance/Bytedance-UnionAD/blob/master/Example/BUDemo/AppDelegate.m) for more details.



## GDPR
Pangle provides a tool for publishers to request consent for personalized ads as well as to handle certain requirements. Publishers can use the following method to handle these requests by showing a single a form, as all of the configuration happens in the Funding Choices UI.

```objective-c
+ (void)openGDPRPrivacyFromRootViewController:(UIViewController *)rootViewController confirm:(BUConfirmGDPR)confirm;
```

Publishers could also make a tool themselves to request consent.

## COPPA
Pangle also compliances with Children’s Online Privacy Protection Act (COPPA), Pangle SDK provides setCoppa method for publishers. At the moment, Pangle won't return ads for Children (Under the age of 13).


## Redirect Readme

**All the rootViewController parameters in Ad APIs must be provided to process ad redirects. In the SDK, all redirects use the present method. Therefore, make sure that the passed rootViewController parameters are not null and do not have other present controllers. Otherwise the present will fail because presentedViewController already exists.**
