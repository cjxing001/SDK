# SUnit聚合SDK原生技术对接文档

[TOC]

## **1** 文档概述

本文档描述了SUnit聚合SDK的服务内容，及技术对接方法。

SUnit聚合SDK是SHAREit向游戏合作伙伴提供的一揽子商业化解决方案，包含广告模块、数据采集模块、登录注册模块、以及线上支付服务模块。可供商家根据自身业务需求，集成在自身应用中使用。其中

-	**[广告模块](https://tva1.sinaimg.cn/large/0082zybply1gc4081tgmcj308g02wtbv.jpg)**
  提供了9家主流在线广告平台的接入能力，为商家赋予广告收益的能力。可供选择的广告源包括：AdColony、Admob、AppLovin、Facebook、UnityAd、Vungle、Mopub、Ironsource、Fyber；可支持的广告形态为：插屏和激励视频。
-	**[数据采集模块](https://tva1.sinaimg.cn/large/0082zybply1gc4081tgmcj308g02wtbv.jpg)**
  提供了用户行为数据的采集能力，可供商户自定义采集上报事件，为数据精细化运营服务。
-	**[登录注册模块](https://tva1.sinaimg.cn/large/0082zybply1gc4081tgmcj308g02wtbv.jpg)**
  提供了一套可轻松集成的登录注册功能
-	**线上支付模块**
  提供了覆盖印度、印尼、菲律宾等多国的在线收款能力，支付渠道涉及电子钱包、借记卡、信用卡、网上银行、话费支付、线下网点等多种手段。

## 2 接入准备

### 2.1 获取必要的设置信息

商户在和SHAREit建立合作意向后，SHAREit运营人员向商户分配下述技术对接时必要的配置信息：

| 模块         | 供配置用的账号信息                                           |
| :----------- | :----------------------------------------------------------- |
| 广告         | 【必备】广告AppID、UnitID（根据所选广告平台而定）<br/>【可选】ADMOB_APP_ID（如商户集成Admob广告源，需获取）<br/>【可选】APPLOVIN_SDK_KEY（如商户集成AppLovin广告源，需获取） |
| 游戏         | 【必备】gameSecret（登录时的秘钥）                                          |
| 支付         | 【必备】支付商户号、支付秘钥                                 |
| 数据上报     | 【必备】FLURRY_API_KEY                                       |
| 预埋广告配置 | 【必备】default_layer文件                                    |

>***如果不使用广告功能，也必须传入广告AppID***

### 2.2 获取SDK包

由SHAREit提供SUnit聚合SDK包, 最新版本为 VERSION_SUNIT=1.2.2 (最新版本号由运营提供)
```
// @See shareit_sdk.gradle
implementation "com.sunit:aggregation-android:VERSION_SUNIT"
```

## 3 SDK调用方法

### 3.1 SdkVersion说明

•	minSdkVersion 16 or later
•	compileSdkVersion 26 or later

### 3.2 引入SDK

**1. 在project的build.gradle文件中添加下面代码**
> ***SHAREit start 和 SHAREit end包裹的代码***

**2. 添加本地aar文件**
当前 sdk 以 aar 形式提供, 需要将 aar 文件放到 project/libs 目录下

**3. 添加第三方依赖**
SDK中的第三方依赖库的配置整理到**shareit_sdk.gradle**中，将这个文件放在主module的根目录下，并在build.gradle中引入依赖，具体如下：

```
 // must be applied after your artifact generating plugin (eg. java / com.android.library/ com.android.application)
   apply from: "shareit_sdk.gradle"

```

**4. 添加默认广告配置文件（有利于提高收入）**
将上文2.1接入准备中获取的default_layer文件拷贝到目标工程assets对应的目录下

### 3.3 配置AndroidManifest.xml

将步骤2中申请的“广告AppId”替换如下示例中 "YOUR_APP_ID"

```
<manifest>
    <application>
         <meta-data android:name="com.ushareit.ads.KEY_APP_ID"
             android:value="YOUR_APP_ID"/>
    </application>
</manifest> 
```

> ***YOUR_APP_ID 是必要参数,如未配置,测试模式下, 将抛出 Crash 提示;将初始化失败,无法获取广告***

### 3.4 SDK初始化

以下为初始化配置

```
<!-- 广告渠道，默认SAHREit就可以 -->
<meta-data
android:name="com.sunit.channel"
android:value="SHAREit" />

<!-- 如果只有一个Activity的应用无需声明此项,如果有多个activity,value为游戏场景所在的主Activity路径 -->
<meta-data
android:name="com.sunit.mainActivity"
android:value="com.unityplayer.UnityActivity" />

<!-- 如果只有一个进程 的应用无需声明此项, 默认包名进程.如果多个进程的应用, 需要声明支持广告的进程的名称 -->
<meta-data
android:name="com.sunit.adProcess"
android:value="processName" />

<!-- 声明为'debug'则会使用测试模式（仅测试使用） -->
<meta-data
android:name="com.sunit.mode"
android:value="debug" />
```

### 3.5 混淆配置

聚合SDK提供的aar 文件中已经包含了混淆配置, 不需要修改混淆文件.

> ***如果是在Library Module引入聚合SDK，请避免该module对SDK进行二次混淆.***

### 3.6 广告源的SDK Network依赖

- 广告Admob集成

如需集成Admob广告源， 需在AndroidManifest.xml文件中配置如下内容：

```
<meta-data
 android:name="com.google.android.gms.ads.APPLICATION_ID"
 android:value="ADMOB_APP_ID" />
```

> ***ADMOB_APP_ID 由 SHAREit 服务对接人提供***

- 广告AppLovin集成

如需要集成 AppLovin广告源，需在AndroidManifest.xml 文件中配置如下内容：

```
<meta-data
 android:name="applovin.sdk.key"
 android:value="APPLOVIN_SDK_KEY" />
```

> ***APPLOVIN_SDK_KEY 由 SHAREit 服务对接人提供***

### 3.7 Flurry数据采集依赖

- Flurry数据上报集成
集成 Flurry，需在AndroidManifest.xml文件中配置如下内容：

```
<meta-data
  android:name="flurry.sdk.API_KEY"
  android:value="FLURRY_API_KEY" />  
```

>***FLURRY_API_KEY 由 SHAREit 服务对接人提供***

### 3.8 SD卡STORAGE权限

1. 我们强烈建议对接App需要WRITE_EXTERNAL_STORAGE权限，并且须按如下样例复写，此权限有利于提升广告收入

```
<uses-permission      
    android:name="android.permission.WRITE_EXTERNAL_STORAGE"
    android:maxSdkVersion="28"
    tools:replace="android:maxSdkVersion"/>
```


2. 在打开app时主动调用申请权限逻辑,代码实例如下:

```
   if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_EXTERNAL_STORAGE)
           != PackageManager.PERMISSION_GRANTED || ContextCompat.checkSelfPermission(this,
                Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
       // Permission is not granted
       ActivityCompat.requestPermissions(this,
               new String[]{Manifest.permission.READ_EXTERNAL_STORAGE, Manifest.permission.WRITE_EXTERNAL_STORAGE},
               0);
   }
```