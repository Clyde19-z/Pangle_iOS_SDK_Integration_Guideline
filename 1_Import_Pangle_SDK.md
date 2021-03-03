# 1. Import Pangle SDK

## Environment requirement
- IOS 10.X and higher;
- Xcode 12 and higher;
- Supported architecture: x86-64, armv7, arm64, i386

**Note: ios 9 is no longer supported starting from v3.5.0.0**


## Import Pangle SDK through CocoaPods (Recommened):
The simplest way to import the SDK into an iOS project is to use CocoaPods. Open your project's Podfile and add this line to your app's target:

- Note: Import Pangle SDK that the version is higher than v3.4.0.0
```XML
pod 'Ads-Global', '~>3.4.1.1' 
```


- Note: If you wanna import old version of Pangle SDK that the version is lower than v3.4.0.0, as shown below:
```XML
pod 'Bytedance-UnionAD', '~>3.3.6.2'
```



## Download Pangle SDK Manually:
Download and unzip the SDK framework from Pangle Platform directly, and import the following frameworks and bundles into your Xcode project manually:

- `BUAdSDK.framework`
- `BUFoundation.framework`
- `BUAdSDK.bundle`
- `BUVAAuxiliary.framework`

<img src="https://github.com/JohnnyWangMiura/Pangle-iOS-SDK-Integration-Guideline/blob/main/destination.png" />



**Note: When you upgrade the SDK, you need to update all frameworks and bundle files.**


Please make sure that `Copy Bundle Resource` contains `BUAdSDK.bundle`.

<img src="https://github.com/JohnnyWangMiura/Pangle-iOS-SDK-Integration-Guideline/blob/main/bundle.png" />





### Xcode Compiler Option Settings

#### Add Permissions

Add the parameter `-ObjC` to `Other Linker Flags` in build settings, and the SDK supports `- all_ load`

##### Detailed Steps:

<img src="https://github.com/JohnnyWangMiura/Pangle-iOS-SDK-Integration-Guideline/blob/main/permission.png" />




#### Add Dependency Libraries

Project needs to find Link Binary With Libraries in `TARGETS` - > `Build Phases`, click "+", and then add the following dependent libraries in order.

- StoreKit.framework
- MobileCoreServices.framework
- WebKit.framework
- MediaPlayer.framework
- CoreMedia.framework
- AVFoundation.framework
- CoreTelephony.framework
- SystemConfiguration.framework
- AdSupport.framework
- CoreMotion.framework
- Accelerate.framework
- libresolv.9.tbd
- libc++.tbd
- libz.tbd
- libsqlite3.tbd
- libbz2.tbd
- libxml2.tbd
- libiconv.tbd
- libc++abi.tbd (newly required from sdk v3.5.0.0)
- Security.framework


**Note: Add the `ImageIO.framework` if the above dependency library is still reporting errors.**

##### Detailed Steps:

<img src="https://github.com/JohnnyWangMiura/Pangle-iOS-SDK-Integration-Guideline/blob/main/library.png"/>


#### Add language configuration

<img src="https://github.com/JohnnyWangMiura/Pangle-iOS-SDK-Integration-Guideline/blob/main/language.png"/>






