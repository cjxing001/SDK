# SUnit聚合SDK原生技术对接文档

[TOC]

## **1** 文档概述

本文档描述了SUnit聚合SDK的服务内容，及技术对接方法。

SUnit聚合SDK是SHAREit向游戏合作伙伴提供的一揽子商业化解决方案，包含广告模块、数据采集模块、登录注册模块、以及线上支付服务模块。可供商家根据自身业务需求，集成在自身应用中使用。其中

-	**广告模块**
  提供了9家主流在线广告平台的接入能力，为商家赋予广告收益的能力。可供选择的广告源包括：AdColony、Admob、AppLovin、Facebook、UnityAd、Vungle、Mopub、Ironsource、Fyber；可支持的广告形态为：插屏和激励视频。
-	**数据采集模块**
  提供了用户行为数据的采集能力，可供商户自定义采集上报事件，为数据精细化运营服务。
-	**登录注册模块**
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

## 4 接口参数说明

### 4.1 数据上报

#### 4.1.1 gameLevelStart“游戏关卡开始”数据上报接口（必加）

游戏关卡开始时上报，level表示当前关卡。
```
SHAREitAggregation.gameLevelStart(String level);
```

玩家死亡后关卡重新开始也要再次上报关卡开始事件。
如果游戏没有关卡概念，类似一局游戏或一场游戏，游戏开始也要上报此事件。
此时参数level使用-1,如：

```
SHAREitAggregation.gameLevelStart(“-1”);
```

#### 4.1.2 gameLevelEnd “游戏关卡结束”数据上报接口（必加）

游戏关卡结束时上报，level表示当前关卡。

```
SHAREitAggregation.gameLevelEnd(String level);
```

如果玩家在游戏过程中按返回键主动退出游戏或玩家死亡，也视为关卡结束，需要上报关卡结束事件。
如果游戏没有关卡概念，类似一局游戏或一场游戏，游戏结束时也要上报此事件。
参数level使用-1,如：

```
SHAREitAggregation.gameLevelEnd(“-1”);
```

#### 4.1.3 onEvent 通用数据上报接口（无事件上报可不用关注此方法）

通用数据上报是通过接口onEvent 进行上报，接口具体参数如下

```
void onEvent(final Context context, final String eventId, final HashMap<String, String> params) 
```
//参数说明：
context： Context对象
eventId：上报事件名称（添加的事件名称，需要通知SHAREit运营）
params：上报事件参数 （参数的key名称必须要小写）
SHAREitAggregation.onEvent(context, eventId, params);

### 4.2 登录注册

#### 4.2.1 isLogin 用户是否登录

```
GameLoginHelper.getInstance().isLogin();
```
返回值： 
 true： 已登录
 false：未登录

#### 4.2.2 userLogin 拉起登录界面

```
SHAREitAggregation.userLogin(context, gameSecret, new 
    GameLoginHelper.OnLoginCompleteListener() {
    @Override
    public void onLoginSuccess(String userid，String username, String avatarUrl) {
    }
});
```
参数说明：
 context： 表示当前调用此方法所在的上下文
 gameSecret： 参考：2.1 必要账号申请
 OnLoginCompleteListener： 登录回调回调函数
 onLoginSuccess： 登录成功才会回调

#### 4.2.3 getUserId 获取登录用户Id

```
GameLoginHelper.getInstance().getUserId()
//如果成功登录过则返回userId；否则返回null.
```

#### 4.2.4 退出登录

```
GameLoginHelper.getInstance().logout();
```
返回值：
 true：退出登录成功； 
 false：退出登录失败

### 4.3 广告请求和展示

> ***下述参数中的unitID对应各家广告源生成的「Unit Space ID」，可协同SHAREit服务对接人获取***

#### 4.3.1 插屏广告

**1. loadAd 请求**
```
InterstitialAd.loadAd(String unitId);
```
**2. isAdReady 判断可见性，showAd前判断一次即可**
针对插屏场景scene，我们暂时定义了三种场景，请尽量使用我们定义的场景，方便后期数据观察：
**SDK定义场景**：如果不符合这三个场景可以自行定义
1.ShareItAd.GAME_LEVEL_START 关卡或对局开始时弹插屏广告
2.ShareItAd.GAME_LEVEL_END 关卡或对局结束时弹插屏广告
3.ShareItAd.GAME_REGAIN_FOCUS 重新回到游戏界面时弹出广告(用户退出到桌面又回到游戏、用户锁屏后解锁回到游戏)：

```
//scene 广告展示的场景
InterstitialAd.isAdReady(String unitId, String scene)
```

```
//如果业务需求需要循环调用此方法检测广告缓存 scene规定为ShareItAd.LOOP
InterstitialAd.isAdReady(unitId,ShareItAd.LOOP);
//show插屏广告前传入scene
InterstitialAd.isAdReady(unitId,scene);
```

**scene需要传入，方便做数据分析**

> ***此接口必须要在主线程中调用***

**3. showAd 展示（需配合isAdReady使用）**

目前提供了两种展示模式：

- 接入方未管理广告缓存（loadAd请求不带adLoadListener参数）
  SDK 将根据 unitId 查找已有的缓存对象. 无缓存时将展示失败（配合isAdReady使用）
```
InterstitialAd.showAd(String unitId, IAdShowListener adShowListener)
```
示例：
```
//load        
InterstitialAd.loadAd(unitId, null);

//show
if (InterstitialAd.isAdReady(unitId,scene,subPortal)) {
    RewardedAd.showAd(unitId, null);
}
```
- 接入方自己管理缓存（loadAd请求带adLoadListener参数）

  可以将缓存的广告对象直接进行展示. 广告对象过期或被销毁等情况将展示失败(需配合adLoadListenner使用)
```
InterstitialAd.showAd(AdWrapper adWrapper, IAdShowListener adShowListener)
```
示例:
```
InterstitialAd.loadAd(unitId, new IAdLoadListener() {
    @Override
    public void onAdLoaded(String unitId, AdWrapper adWrapper) {
        InterstitialAd.showAd(adWrapper, null);
    }

    @Override
    public void onAdError(String unitId, AdException adError) {
    }
});
```
IAdShowListener  具体参考：4.3.4 Listener接口说明
> ***此接口必须要在主线程中调用***


#### 4.3.2 激励视频广告

**1. loadAd 请求**

```
RewardedAd.loadAd(String unitId);
```

**2. isAdReady 判断可见性，showAd前判断一次即可**

```
//scene 广告展示的场景
//subPortal 此次广告场景中的入口
RewardedAd.isAdReady(String unitId, String scene, String subPortal)
```
**(特殊场景)**如果业务需要循环调用此方法检测广告缓存,用来切换按钮或图标状态，scene规定为“loop”,subPortal不用传

```
RewardedAd.isAdReady(unitId,ShareItAd.LOOP);
```
以下为用户真正需要点击展示广告时使用相应场景
```
//show激励视频广告前传入scene，和subPortal
RewardedAd.isAdReady(unitId,scene,subPortal);
```
**scene、subPortal 需要传入，方便做数据分析**

> ***此接口必须要在主线程中调用***

**3. showAd 展示**
目前提供了两种展示模式：

- 接入方未管理广告缓存（loadAd请求不带adLoadListener参数）
  SDK 将根据 unitId 查找已有的缓存对象. 无缓存时将展示失败（配合isAdReady使用）

```
RewardedAd.showAd(String unitId, IAdShowListener adShowListener)
```
示例：
```
//load        
RewardedAd.loadAd(unitId,null);

//show
if (RewardedAd.isAdReady(unitId,scene, subPortal)) {
    RewardedAd.showAd(unitId, null);
}
```
- 接入方自己管理缓存（loadAd请求带adLoadListener参数）

  可以将缓存的广告对象直接进行展示. 广告对象过期或被销毁等情况将展示失败(需配合adLoadListenner使用)

```
RewardedAd.showAd(AdWrapper adWrapper, IAdShowListener adShowListener)
```
示例:
```
RewardedAd.loadAd(unitId, new IAdLoadListener() {
    @Override
    public void onAdLoaded(String unitId, AdWrapper adWrapper) {
        RewardedAd.showAd(adWrapper, null);
    }

    @Override
    public void onAdError(String unitId, AdException adError) {

    }
});
```
IAdShowListener  具体参考：4.3.4 Listener接口说明
> ***此接口必须要在主线程中调用***

**4. showRewardedBadgeView（此方法调用可能会比较频繁,请耐心接入）**
```
//scene 广告展示的场景
//subPortal 此次广告场景中的子入口
RewardedAd.showRewardedBadgeView(scene, String... subPortal);
```
**scene、subPortal 需要传入**

- 方法目的：统计激励视频可点击广告入口到用户点击此入口展示广告之间的比例
  方便做漏斗分析，分析各个激励视频入口情况

- 涉及场景：所有**包含激励视频入口**场景

- 上报场景：**进入场景**（打开弹窗或打开各个游戏场景）或**重新回到该场景**（锁屏、home键、跳转其他场景后返回到原来场景）
或者理解为包含激励视频入口按钮的场景或弹窗,得到焦点

- 调用时机：游戏场景得到焦点,激励视频入口**按钮可见并且可点击**,上报此页面内**所有**激励视频入口。

- Android原生简单示例：Activity、Fragmen或Dialog中onResume方法。如当前为首页，并且有两个激励视频按钮
```
@Override
protected void onResume() {
    super.onResume();
    if(按钮均可点击&&按钮可见) {
    RewardedAd.showRewardedBadgeView(ShareItAd.HOME,"portal1","portal2");
    }
}
```

- cocos简单示例：如当前为首页，并且有两个激励视频按钮
  首页场景订阅 cc.game.EVENT_HIDE cc.game.EVENT_SHOW 事件,当触发 EVENT_SHOW 事件时上报当前场景或弹框的所有可点击并且可见入口
```
cc.game.on(cc.game.EVENT_SHOW, function () {
   xx.showReWardedBadgeView(ShareItAd.HOME,"portal1","portal2");
});
```
流程图示例：
![img5](https://tva1.sinaimg.cn/large/006tNbRwly1gb218j63hwj30zf0gmacu.jpg)

> ***此接口必须要在主线程中调用***

#### 4.3.3 插屏广告和激励视频广告加载逻辑最佳实践

1. 进入游戏后调用一次load

```
InterstitialAd.loadAd(String unitId);
RewardedAd.loadAd(String unitId);
```

2. 到达广告展示时机,调用 isReady, 如果返回true,则进行展示,如果返回false,则直接调用load

插屏广告代码示例：

```
if (InterstitialAd.isAdReady(unitId,"home")){
    InterstitialAd.showAd(unitId,null);
} else {
    InterstitialAd.loadAd(unitId);
}
```

激励视频广告代码示例：
```
if (RewardedAd.isAdReady(unitId,"home","portal1")){
    RewardedAd.showAd(unitId,null);
} else {
    RewardedAd.loadAd(unitId);
}
```

3. 如果没有等待 load callbak 的场景,可以不注册广告load的listener

#### 4.3.4 Listener接口说明

```
public interface IAdShowListener {
    // 广告展示失败时将回调
    void onAdShowFailed(String unitId, AdException adException);
    // 广告展示回调, adSourceName 表示请求到的广告来自于哪个广告平台
    void onAdImpression(String unitId, String adSourceName);
    // 广告点击时回调, adSourceName 表示请求到的广告来自于哪个广告平台
    void onAdClicked(String unitId, String adSourceName);
    // 激励视频广告在达到激励条件时回调, adSourceName 表示请求到的广告来自于哪个广告平台; 插屏广告无此回调
    void onAdRewarded(String unitId, String adSourceName);
    // 广告关闭时回调, adSourceName 表示请求到的广告来自于哪个广告平台; 激励视频广告可以根据 infoMap 中的 "hasReward" 字段来判断广告关闭时是否已经激励
    void onAdClosed(String unitId, String adSourceName, boolean hasRewarded);
}
```

#### 4.3.5 Banner广告

**1. preloadBannerAd 预加载(版本>1.2.2后此方法废弃)**

```
//如下方法可以使用在初始化到showBanner之间，方便showBanner时能直接有广告展示
BannerAd.preloadBannerAd(String unitId);
```

**2. showBanner 展示**
```
//此方法调用后开始请求并展示Banner广告，默认支持自动刷新
//如下方法传入Activity和展示位置，不传Activity默认获取最上层主Activity
//参数 gravity 表示展示位置，目前只支持上下:BannerAd.TOP  BannerAd.BOTTOM
BannerAd.showBanner(String unitId , int gravity);
//如下方法传入 Activity 和 承载BannerAd的容器view
BannerAd.showBanner(String unitId , Activity activity , int gravity);
BannerAd.showBanner(String unitId , ViewGroup container);
```

**3. hiddenBannerAd 隐藏**
```
//隐藏正在展示的广告并停止自动刷新
BannerAd.hiddenBannerAd();
```

### 4.4 支付模块请求
接入方法，详见: **https://docs.shareitpay.in**

## 5 Demo说明

Demo参见：**AggregationDemo.zip**
Demo中提供了各接口使用的完整对接示例。

## 6 注意事项
1. 出于安全考虑，支付秘钥请务必存放于服务端，勿存客户端
2. 由于sdk内广告皆是海外广告源，国内基本没什么广告库存，测试时尽量开启vpn
3. 如无需集成广告源Admob或AppLovin时，请注释掉相应的依赖库
4. errorCode: 9016意思是在加载一次广告后,广告会自动请求.不用再调用load.后续调用 isRewardedAdReady 返回 true,直接展示就可以
5. 如SUnit聚合SDK所在module的build.gradle包含下方代码：

```
implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'])
```

6. 请注意把share_sdk.gradle中下方代码注释掉，否则会造成类重复错误
   
```
implementation(name: 'sunit-release-1.0.x.x_common', ext: 'aar')
```

## 7 Change Log

### 1.2.2
2020-02-17

1. 优化 consumer proguard file

### 1.2.1.2
2020-02-14

1. 增加 'load_strategy' = 3, 顺序延迟提前返显

### 1.2.0.4

2020-02-12
1. 统一子sdk的版本号

### 1.2.0

2020-02-11

1. 更改为 maven 依赖方式
2. 针对 1.1.2.3 版本修复埋点bug

### 1.1.2.3

2020-01-19

1. 4.3.1.2 isAdReady 增加方法说明
2. 4.3.2.2 isAdReady 增加子入口参数和说明
3. 4.3.2.4 showRewardedBadgeView 增加子入口参数和说明

**注意：4.3.2.4 改为必须接入，此次改动涉及所有插屏和激励视频广告场景和入口信息，需要对接方整理一份所有广告场景以及场景入口命名的Exscel表格同步到SHAREit运营，后期会做入口数据分析**

如：

| 广告场景                 | 场景（scene）                                    | 入口（subPortal） |
| ------------------------ | ------------------------------------------------ | :---------------- |
| **插屏**                 | 插屏广告场景尽量填写**SDK定义场景，详看4.3.1.2** |                   |
| 关卡结束插屏广告         | itl_game_level_end                               |                   |
| 关卡开始插屏广告         | itl_game_level_start                             |                   |
| 重新回到游戏界面插屏广告 | itl_game_regain_focus                            |                   |
| ...                      | ...                                              |                   |
| **激励视频**             | 激励视频广告根据自己的游戏场景自行定义           |                   |
| 游戏死亡复活激励视频广告 | gameover                                         | resurgence        |
| ...                      | ...                                              |                   |


### 1.1.1.3

2020-02-11

升级到1.1.1.1不需要特殊的步骤

### 1.1.1.1

2020-01-13

升级到1.1.1.1不需要特殊的步骤

### 1.1.0.1

2020-01-08

升级到1.1.0.1不需要特殊的步骤

### 1.0.9.2

2020-01-07

1. 2.1预埋广告配置
2. 3.2.4 添加默认广告配置文件
3. 删除6.3自动申请权限方式，提供申请权限方法 3.8

### 1.0.8.8

2019-12-31

1. 3.5修改初始化，读取配置文件自动初始化
2. 4.1此版本移除gameStart和gameEnd事件上报
3. 6.3 增加自动申请权限方式

### 1.0.8.4

2019-12-23

1. 3.3广告源的SDK Network依赖(Mopub)
2. 4.3.5 增加banner说明

### 1.0.4.0

2019-11-04

1. 接口参数说明
2. 注意事项里读写权限授权相关问题

### 1.0.1.0

2019-10-22

1. AppID必须要申请
2. 登录增加横屏适配
3. 对isAdReady进行说明
4. 对showAd进行说明
5. Fix bug

### 1.0.0.0

2019-09-10

1. 初始文档