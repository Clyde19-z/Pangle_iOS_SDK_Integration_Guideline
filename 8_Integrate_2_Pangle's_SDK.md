
# 8. How to integrate both [CN] Pangle SDK and [Outside CN] Pangle SDK in one project

Starting from version 3.4.0.0, pangle iOS SDK will be divided into `[CN] Pangle SDK` and `[Outside CN] Pangle SDK`.

The SDK is divided into three General libraries, one CN library and one Outside_CN library.

| -| - |
|---------------------|------------------------------------------------------------|
| General libraries   | BUAdSDK.bundle、BUAdSDK.framework、BUFoundation.framework  |
| Outside_CN library  | BUVAAuxiliary.framework                                    |
| CN library          | BUCNAuxiliary.framework                                    |



## Import Outside_CN Pangle SDK
### Manually
When you wanna only import `Outside_CN Pangle SDK` separately, you need to import the `General libraries` and `Outside_CN library`：BUAdSDK.bundle、BUAdSDK.framework、BUFoundation.framework、BUVAAuxiliary.framework
### Pod
```xml
pod 'Ads-Global'
```

## Import CN Pangle SDK
### Manually
When you wanna only import `CN Pangle SDK` separately, you need to import import the `General libraries` and `CN library`：BUAdSDK.bundle、BUAdSDK.framework、BUFoundation.framework、BUCNAuxiliary.framework
### Pod
```xml
pod 'Ads-CN'
```

## Import both CN Pangle SDK and Outside_CN Pangle SDK
### Manually
All five files need to be imported：BUAdSDK.bundle、BUAdSDK.framework、BUFoundation.framework、BUVAAuxiliary.framework、BUCNAuxiliary.framework

### Pod
```xml
pod 'Ads-Global' , :subspecs => ['BUAdSDK','Domestic'] 
```

If you want to import both `CN Pangle SDK` and `Outside_CN Pangle SDK`, `setTerritory` is required (mark the user as CN or Outside CN), it should be finished before initializing Pangle SDK.

Publishers should locate the user devices directly. We would suggest to locate users with the following ways:

- Locate user devices through IP address.
- Locate user devices through GPS info, GPS should be collected by developers, Pangle SDK won't collect GPS info.
- Locate user devices through developer's user account infomation(If app support) or internal ids.

Once the user is located, then call `setTerritory` according to the user location.


**Note**：
- **The method `setTerritory` must be called before initializing Pangle SDK.**
- Publishers are responsible for locating the users.
- Pangle will not return ads when the territory is incorrect. (For example, you Set Territory to BUAdSDKTerritory_CN but the user's IP is outside of China Mainland and vice versa.)

```objective-c
///This property should be set when both BUVAAuxiliary.framework and BUCNAuxiliary.framework are imported in your project, otherwise it does not need to be set. You must set Territory before set APPID.
///@param territory : Regional value: 1.BUAdSDKTerritory_CN  2.BUAdSDKTerritory_NO_CN
[BUAdSDKManager setTerritory: (BUAdSDKTerritory)territory];
```

