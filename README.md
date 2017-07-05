# SuntengMob SDK for Unity

Unity 广告SDK

## 如何使用

- [Getting Started with Unity plugin](https://github.com/shunfei/suntengMob-sdk-unity/blob/master/docs/Get_Started.md)  

## Download

[SuntengMob-sdk-Unity-Release](https://github.com/shunfei/suntengMob-sdk-unity/releases)

**Sample App Download**

[Sample Apk](https://github.com/shunfei/suntengMob-sdk-unity/blob/master/sample/UnityAdsSample.apk)
 
## Change Log

07/05/2017 - [v2.1.0](https://github.com/shunfei/suntengMob-sdk-unity/releases/tag/v2.1.0)  

* New support for NativeAd;
* Fix some bugs.

02/16/2017 - [v2.0.0](https://github.com/shunfei/suntengMob-sdk-unity/releases/tag/v2.0.0)

* 替换底层引用SDK,把原有视频和普通广告SDK合并;  
* 修改部分使用和回调接口;  
* 新增部分功能;
* 修改部分已知Bug及优化。

02/16/2017 - [v1.0.0](https://github.com/shunfei/suntengMob-sdk-unity/releases/tag/v1.0.0)

* Initial release.  

## 效果展示

**Sample App下载**
 [Sample Apk](https://github.com/shunfei/suntengMob-sdk-unity/blob/master/sample/UnityAdsSample.apk)  

**Sample Package 下载**
[Sample Package](https://github.com/shunfei/suntengMob-sdk-unity/blob/master/sample/suntengMob_sample.unitypackage)

> 注意事项：如果您使用的是 **Sample Package** ,并且用Unity 编译器导出项目到**Android 7.0** 及以上机器中，请修改**Plugins > Android > AndroidManifest.xml**中`provider` 的`android:authorities` 的值,把`com.sunteng.unityads.download.fileProvider` 替换成`<unity项目发布包名>.download.fileProvider`,除了此之外，还需要修改**Plugins > Android > res > provider_pahts.xml**中的两个`path` 值，具体的操作`provider_paths.xml` 文件中有具体提示。