# SuntengMob for Unity 帮助文档

  Version：2.1.0  

  Time：2017-07  

 如果您正在使用Unity游戏开发引擎，并且使用c#进行开发，就可以导入该插件，然后在Android或者iOS平台中实现横幅、视频、插屏及原生广告的展示，此文档只是简单介绍插件的使用方式，更为详尽的使用方式，请参阅`使用示例Demo`代码。 

  [插件下载](https://github.com/shunfei/suntengMob-sdk-unity/blob/master/suntengads-unity-plugin/suntengMob_v2.1.0.unitypackage)  

  [使用示例Demo](https://github.com/shunfei/suntengMob-sdk-unity/blob/master/sample/suntengMob_sample.unitypackage)  



**开发环境:**  

- Unity 4 或更高版本  


- 发布到Android平台：  

  Android SDK 4.0 或更高版本  


- 发布到iOS平台：  

  iOS6.0 或更高

# 一、准备工作

 在向Unity项目中导入广告插件之前，首先需要去[sunTeng官网](http://ssp.biddingx.com/) 申请展示广告时需要用到的`appSecret` 以及`adUnitId` 等。

# 二、导入插件包  

1. 打开Unity 编辑器，选择**Assets > Import Package > Custom Package**，指向你所下载的`suntengMobPlugin.package ` 文件；  

2. 确认插件中所有的文件都已被选中，然后点击 **Import** 按钮导入插件。

# 三、编写c#脚本

 通过c# 代码调用插件包暴露的API ，实现编写一份代码，能正常在Android 和iOS 两个平台上展示横幅、插屏以及视频广告。

## **1. 初始化操作** 

 在游戏的初始化场景的`Awake();` 方法中初始化广告插件，切记必须在使用广告之前对插件进行初始，具体代码如下：  

```c#  
using MobileAds.Api;

...
void Awake()
{
	#if UNITY_EDITOR
	    string appSecret = "UNUSED";
	#elif UNITY_ANDROID
	    string appSecret ="INSERT_ANDROID_APPSECRET_HERE";
	#elif UNITY_IPHONE
	    string appSecret = "INSERT_iOS_APPSECRET_HERE";
	#else
	    string appSecret = "UNEXPECTED_PLATFORM";
	#endif
	
	MobileAdService adService = MobileAdService.GetInstance();
	//init ad service
	adService.InitAdService(appSecret);
	//adService.SetDebug(true);
}
```

## **2. 横幅广告**

#### 2.1创建并展示一个横幅广告

```c#
using MobileAds.Api;

...

private void RequestBannerView() 
{
    #if UNITY_EDITOR
        string adUnitId = "UNUSED";
    #elif UNITY_ANDROID
        string adUnitId = "INSERT_ANDROID_BANNER_AD_UNIT_ID_HERE";
    #elif (UNITY_5 && UNITY_IOS) || UNITY_IPHONE
        string adUnitId = "INSERT_iOS_BANNER_AD_UNIT_ID_HERE";
    #else
        string adUnitId = "UNEXPECTED_PLATFORM";
    #endif

    // create an 320x50 banner on the top of screen.
    this.bannerView = new BannerView(adUnitId, AdSize.Banner320x50, AdPositon.Top);
    this.bannerView.LoadBannerView();
}
```

 `AdPosition` 是一个用来指定横幅广告在屏幕中展示位置的枚举类；  
 `AdSize` 作用是指定横幅广告的大小，里面内置了4个值，用户也可以通过构造方法 `AdSize(int width,int height);` 传入自定义的值，需要注意的是，这个值不是像素，而是像素密度；  
 `LoadBannerView();` 方法加载广告成功之后,会直接展示在指定的位置。

#### 2.2 横幅广告事件回调
```c#
private void RequestBannerView() 
{
    BannerView bannerView = new BannerView(adUnitId, AdSize.Banner320x50, AdPositon.Top);
  
    //call back when bannerAd has been show successfully.
    bannerView.OnAdShowSuccess += HandleBannerViewShowSuccess;
    //call back when bannerAd was show failed.
    bannerView.OnAdShowFailed += HandleBannerViewShowFailed;  
    //call back when bannerAd has been closed.  
    bannerView.OnAdClosed += HandleBannerVewClose;  
    //call back when bannerAd has been clciked.  
    bannerView.OnAdClicked += HandleBannerViewClick;
}
```

```c#
public void HandleBannerViewShowSuccess(object sender, EventArgs args)
{
    Debug.Log("HandleBannerViewLoaded event received");
}
```

```c#
//广告请求失败时候，回调方法的第二个参数args会有错误信息的描述。
public void HandleBannerViewShowFailed(object sender, CustomEventArgs args)
{
    Debug.Log("HandleBannerViewLoadFailed event received with message:" + args.Message);
}
```  

```c#
public void HandleBannerVewClose(object sender, EventArgs args)
{
    Debug.Log("HandleBannerVewClose event fired");
}
```  

```c#
public void HandleBannerViewClick(object sender, EventArgs args)
{
    Debug.Log("HandleBannerViewClick event fired");
}
```  

> 开发者只需要对自己关心的事件回调做注册，并不强制实现所有的回调。

#### 2.3 隐藏和显示横幅广告  
&emsp;&emsp;`BannerView` 提供了`HideBannerView();` 方法用来隐藏横幅广告，横幅广告被隐藏之后，如果需要再显示它，可以调用`ShowBannerView();` 方法。

### 2.4 销毁横幅广告  
&emsp;&emsp;当一个横幅广告使用完之后，调用`BannerView` 的`DestroyBannerView();` 方法 ，这个方法会通知`BannerView`的实体已经不再被使用，它所占有的内存可以被回收了。

## **3. 插屏广告**

#### 3.1 创建一个插屏广告

```c#
using MobileAds.Api;

...

private void RequestInterstitial()
{
    #if UNITY_EDITOR
        string adUnitId = "UNUSED";
    #elif UNITY_ANDROID
        string adUnitId = "INSERT_ANDROID_INTERSTITIAL_UNIT_ID_HERE";
    #elif (UNITY_5 && UNITY_IOS) || UNITY_IPHONE
        string adUnitId = "INSERT_iOS_INTERSTITIAL_UNIT_ID_HERE";
    #else
        string adUnitId = "UNEXPECTED_PLATFROM";
    #endif

    // create an interstitialAD
    this.interstitial = new InterstitialAd(adUnitId);
    // load interstitialAd
    this.interstitial.LoadAd();
}
```

#### 3.2 展示插屏广告  

```c#
private void ShowInterstitail()
{
    if (this.interstitial != null && this.interstitial.IsLoaded())
    {
        interstitial.ShowAd();
    }
}
```
> 必须要等到插屏广告请求成功，即`IsLoaded();` 方法返回`true` 之后才能展示插屏广告。  

#### 3.3 插屏广告事件回调

```c#
private void RequestInterstitial()
{
    this.interstitial = new InterstitialAd(adUnitId);

    //Register call back for ad event;
    this.interstitial.OnAdLoaded += HandleInterstitialLoaded;
    this.interstitial.OnAdFailedToLoad += HandleInterstitialShowFailed;
    this.interstitial.OnAdDisplayed += HandleInterstitialDisplayed;
    this.interstitial.OnAdClicked += HandleInterstitialClicked;
    this.interstitial.OnAdClosed += HandleInterstitialClosed;
}
```

```c#
public void HandleInterstitialLoaded(object sender, EventArgs args)
{
    Debug.Log("HandleInterstitialLoaded event received");
}
```

```c#
public void HandleInterstitialShowFailed(object sender, AdFailedToLoadEventArgs args)
{
    Debug.Log("HandleInterstitialLoadFailed event received with message:" + args.Message);
}
```

```c#
public void HandleInterstitialDisplayed(object sender, EventArgs args)
{
    Debug.Log("HandleInterstitialDisplayed event received");

}
```

```c#
public void HandleInterstitialClicked(object sender, EventArgs args)
{
    Debug.Log("HandleInterstitialClicked event received");
}
```

```c#
public void HandleInterstitialClosed(object sender, EventArgs args)
{
    Debug.Log("HandleInterstitialClosed event received");
}
```  

`interstitial.OnAdFailedToLoad();` 加载广告时候，触发该回调表示广告请求/竞价失败，展示广告时候触发该回调，表明广告物料已经过期，如果要展示需要重新请求。  

> 开发者不需要实现所有的回调方法，只需要实现自己所关心的的回调就行了。

#### 3.4 销毁插屏广告  

&emsp;插屏广告使用完成之后，开发者也需要调用`interstitial.DestroyAd();`方法通知插屏广告所占用的内存可以被释放了。

## **4. 视频广告**

#### 4.1 请求并下载视频广告的资源  

```c#
using MobileAds.Api;

...

private void RequestVideoAd() 
{
    #if UNITY_EDITOR
        string adUnitId = "UNUSED";
    #elif UNITY_ANDROID
        string adUnitId = "INSERT_ANDROID_VIDEOAD_UNIT_ID_HERE";
    #elif (UNITY_5 && UNITY_IOS) || UNITY_IPHONE
        string adUnitId = "INSERT_iOS_VIDEOAD_UNIT_ID_HERE";
    #else
        string adUnitId = "UNEXPECTED_PLATFORM";
    #endif
    
    this.videoAd = new BaseVideoAd(adUnitId);
    this.videoAd.LoadVidoAd();
}
```

#### 4.2 展示视频广告  

```c#
private void ShowVideoAd() 
{
    if (this.videoAd != null) {
        this.videoAd.ShowVodeoAd();
    }
}
```
> 如果直接调用`ShowVideoAd();` 方法或者在视频广告未加载完成的时候调用，并不能展示出视频广告。  

#### 4.3 视频广告回调事件  

```c#
private void RequestVideoAd() 
{
    this.videoAd = new BaseVideoAd(adUnitId);
   
    //Register for BaseVideoAd event;
    this.videoAd.OnVideoAdLoaded += HandleVideoAdLoaded;
    this.videoAd.OnVideoAdFailedToLoad += HandleVideoAdLoadFailed;
    this.videoAd.OnVideoFinished += HandleVideoAdFinish;
    this.videoAd.OnVideoClose += HandleVideoAdClose;
    this.videoAd.OnPlayError += HandleVideoPlayError;
}
```

```c#
public void HandleVideoAdLoaded(object sender, EventArgs args)
{
    Debug.Log("HandleVideoAdLoaded event received");
}
```

```c#
public void HandleVideoAdLoadFailed(object sender, CustomEventArgs args)
{
    Debug.Log("HandleVideoAdLoadFailed event received with message:" + args.Message);
}
```

```c#
public void HandleVideoAdFinish(object sender, CustomEventArgs args)
{
    Debug.Log("HandleVideoAdFinish event finish");
}
```

```c#
public void HandleVideoAdClose(object sender, CustomEventArgs args)
{
    Debug.Log("HandleVideoAdClose event error");
}
```

```c#
public void HandleVideoPlayError(object sender, CustomEventArgs args) {
    Debug.Log("HandleVideoPlayError event with message：" + args.Message);
}
```

`HandleVideoAdLoaded();` 广告竞价请求成功，并且广告资源下载完毕之后回调；  
`HandleVideoAdLoadFailed();` 广告竞价请求失败、视频资源下载失败等会触发该回调；  
`HandleVideoAdFinish();` 方法只会在视频流程整个完成，并且用户关闭落地页或者下载安装应用成功才会触发；  
`HandleVideoAdClose();` 跳过广告、点击**x**关闭广告等非正常流程回触发该回调；   
`HandleVideoPlayError();` 视频播放出现错误触发该回调；  

>开发者只需要注册自己关心的回调，不强制全部实现。 

## **5.原生广告**

#### 5.1 创建并请求原生广告

``` c#
using MobileAds.Api;

...

private void RequestNativeAd() 
{
    #if UNITY_EDITOR
        string adUnitId = "UNUSED";
    #elif UNITY_ANDROID
        string adUnitId = "INSERT_ANDROID_NATIVEAD_UNIT_ID_HERE";
    #elif (UNITY_5 && UNITY_IOS) || UNITY_IPHONE
        string adUnitId = "INSERT_iOS_NATIVEAD_UNIT_ID_HERE";
    #else
        string adUnitId = "UNEXPECTED_PLATFORM";
    #endif

    //create an CustomNativeAd
    this.mNativeAd = new CustomNativeAd(adUnitId);
    //register gameObject for nativeAd impression
    this.mNativeAd.RegisterGamoObjectForImpression(gameObject);
    this.mNativeAd.LoadAd();
}
```
> **注意：**`RegisterGamoObjectForImpression(GameObject obj);`方法需要传入原生广告展示的`panel(如果广告展示在UGUI上)`或者展示广告的`GameObject`，并且此方法**必须调用**，否则广告无法检测到曝光和发送曝光数据，同时广告的点击也会失效。 

#### 5.2 原生广告回调事件  

``` c#
private void RequestNativeAd() {
    CustomNativeAd mNativeAd = new CustomNativeAd(adUnitId);
    
    this.mNativeAd.OnNativeAdLoaded += HandlerCustomNativeAdLoaded;
    this.mNativeAd.OnNativeAdFailedToLoad += HandlerCustomNativeAdLoadFailed;
}
```

``` c#
private void HandlerCustomNativeAdLoaded(object sender, EventArgs args) {
    mNativeAd.ExecuteOnMainThread(() => {
	    Debug.Log("HandlerCustomNativeAdLoaded event fired");
        ShowNativeAd();
    });
}
```

``` c#
private void HandlerCustomNativeAdLoadFailed(object sender, CustomEventArgs args) {
    mNativeAd.ExecuteOnMainThread(()=> {
        Debug.Log("CustomNativeAd failed load with error: " + args.Message);
    });
}
```

> 原生广告的回调方法是在非主线程中，所以`CustomNativeAd`提供一个`ExecuteOnMainThread(Action action);`方法用来在更新UI或者进行其他必须在主线程上进行的操作。

#### 5.3 在 UGUI上展示原生广告  

```c#
[Header("Text:")]
public Text adTitle;
public Text resultText;
public Text descripionText;

[Header("Buttons:")]
public Text actionText;
public Button actionButtion;

[Header("Images")]
public Image image1;
public Image logo;
public Image icon;

private void ShowNativeAd() {
    if (mNativeAd == null ||!mNativeAd.IsLoaded()){
        return; 
    }
    adTitle.text = mNativeAd.GetTitle();
    descripionText.text = mNativeAd.GetDescription();
    string buttonText = mNativeAd.GetButtonContent();
    if (string.IsNullOrEmpty(buttonText) || "无按钮".Equals(buttonText)){
        actionButtion.gameObject.SetActive(false);
    }else{
        actionText.text = buttonText;
        actionButtion.gameObject.SetActive(true);
    }

    string LogoUrl = mNativeAd.GetLogo();
    if (!string.IsNullOrEmpty(LogoUrl))
        StartCoroutine(mNativeAd.ShowSpriteToImage(LogoUrl, logo));
    string IconUrl = mNativeAd.GetIcon();
    if (!string.IsNullOrEmpty(IconUrl))
        StartCoroutine(mNativeAd.ShowSpriteToImage(IconUrl, icon));
    string image1Url = mNativeAd.GetImages()[0];
    if (!string.IsNullOrEmpty(image1Url))
        StartCoroutine(mNativeAd.ShowSpriteToImage(image1Url, image1));
    mNativeAd.RecordDisplay();//通知检查广告曝光
    mNativeAd.RegisterGameObjectForClick(new GameObject[]{ this.gameObject,
    actionButtion.gameObject});//注册广告点击监听
    }
```

> `ShowSpriteToImage(string url, Image image);`方法的作用是把url表示的图片加载到`UGUI`的`Image`控件上;
> `RecordDisplay();`方法用来通知广告进行曝光检查，必须调用，否则广告不会发送曝光数据;
> `RegisterGameObjectForClick(GameObject[] objs);`方法用来注册广告的点击区域,广告区域上的`Button`控件必须添加到数组中，否则可能导致`Button`点击并不触发广告点击事件。

#### 5.4 在普通游戏物体上显示原生广告  
&emsp;&emsp;和在UGUI上展示原生广告一样，在普通游戏物体(如Quad等)上展示原生广告，也需要注册点击区域和通知广告检查曝光;
&emsp;&emsp;为了能显示图片，可调用`CustomNativeAd`提供的:`ShowTexture2dToGameObject(string url, GameObject obj);`方法。

> 注意：为了能在普通游戏物体上响应广告的点击事件，需要在场景中添加`EventSystem`。

#### 5.5 广告销毁
&emsp;&emsp;在当前场景的`OnDestroy()`方法中调用`CustomNativeAd`的`Dispose()`方法即可回收原生广告所持有的资源；

# 四、注意事项(`重要`)

## 1. Android平台

1、 适配安卓7.0 及以上版本手机，开发者需要修改**Plugins > Android > AndroidManifest.xml**中`provider` 的`android:authorities` 的值，把`com.sunteng.unityads.fileProvider` 替换成`<unity项目发布包名>.fileProvider`，如下所示：  

```xml 
<provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="<unity项目发布包名>.fileProvider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths"/>
</provider>
```

2、 适配安卓7.0 除了上述5.1 之外，还需要修改**Plugins > Android > res > provider_pahts.xml**中的两个`path` 值，具体的操作`provider_paths.xml` 文件中有具体提示；

3、 如果用户有接入其他第三方Android插件，并且插件中也引入了`support-v4-23.0.0.jar` v4包，只需要保留一个；

4、 如果其他第三方Android插件中也有`Androidmanifest.xml` 文件，需要合并；  

5、适配安卓7.0，如果其他第三方插件也注册了`fileProvider` ，需要合并，只能保留一个，否则Unity工程发布成apk文件之后，在安卓设备上不能成功安装。如果其他第三Android插件未提供兼容的接口，可以以第三方插件包为准，把本插件中`Plugins > Android > res > provider_pahts.xml` 的内容合并到第三方插件`fileProvider`注册时`meta-data`标签中所引用的xml文件中，并且必须调用本插件`MobileAdService` 类中的`setFileProviderAuthorities(string)` 方法，把`fileProvider`中的`android:authorities` 标签的值传入；  

```c#
void Awake() {
    MobileAdService adService = MobileAdService.GetInstance();
    adService.SetFileProviderAuthorities("<authorities>");
}
```  

6、 如果不需要适配安卓7.0，请忽略上述操作，并且请删除`AndroidManifest.xml` 里 `provider` 的注册代码,及删除引用的`support-v4-23.jar` 。

## 2. iOS平台

1、需要添加的 Framework ：

```objc
AdSupport.framework
AVFoundation.framework
CoreLocation.framework（关闭基于位置广告的开发者，可以不添加）
CoreTelephony.framework
Foundation.framework
MobileCoreServices.framework
Security.framework
SystemConfiguration.framework
UIKit.framework
libz.tbd
```

2、设置对应 **target** 的编译选项，在**Build Settings > Linking > Other Linker Flags**，添加 `-ObjC` 参数。

3、iOS 8.0+ 中获取地理位置方法，在 `info.plist` 里加入对应的定位请求字段，值可以为空或者填写获取定位请求提示框要显示的内容。`（关闭基于位置广告的开发者，可以不添加）`

```XML
<key>NSLocationWhenInUseUsageDescription</key>
<string>iOS 8 定位权限请求提示语！</string>
<key>NSLocationUsageDescription</key>
<string>iOS 10 定位权限请求提示语！</string>
```

> 说明：由于部分广告会定向投递到某些城市，SDK 需要获取地理位置以支持广告的定向投放。

# **五、其他**  

## 1. debug模式  
&emsp;`MobileAdService` 类中提供了一个`SetDebug(bool);` 方法，用来开关插件包中引用的Android/iOS包中的日志输出，SDK 默认关闭日志输出，传入`true` 的时候，才有日志输出，开发者可以通过使用相关工具查看移动设备的日志输出，建议开发者发布正式包的时候，关闭日志输出。  

```c#
void Awake() 
{
    MobileAdService adService = MobileAdService.GetInstance();
    adService.SetDebug(true);
}
```

## 2. 位置信息开关  
&emsp;`MobileAdService` 类中还提供了一个`SetLocationEnable(bool);` 方法，用来决定是否获取用户的位置信息，SDK 默认开启位置获取,开发者传入`false` 时关闭。  

```c#
void Awake() 
{
    MobileAdService adService = MobileAdService.GetInstance();
    adService.SetLocationEnable(false);
}
```  

## 3. 视频关闭开关  
&emsp;`MobileAdService` 类中提供了一个`SetCloseVideoEnable(bool);` 方法，此方法的作用是用来决定视频的左上角是否出现 “x” 按钮让用户可以直接关闭视频广告，默认不出现，开发者传入`true`时出现。  
```c#
void Awake() 
{
    MobileAdService adService = MobileAdService.GetInstance();
    adService.SetCloseVideoEnable(true);
}
```  