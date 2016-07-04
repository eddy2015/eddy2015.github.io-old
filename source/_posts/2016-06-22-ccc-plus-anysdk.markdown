---
layout: post
title: "CocosCreator 集成 AnySDK 教程"
date: 2016-06-22 08:39:30 +0800
comments: true
categories: Cocos-Creator AnySDK
---
本文主要介绍怎么在 CocosCreator 项目中集成 AnySDK 。<!--more-->

##本人开发环境
---
CocosCreator 1.1.1, AnySDK 2.1.2, AnySDK_Framework_JS_Android_2.1.2, MacOS 10.10.3

##Android 平台集成 AnySDK 详细步骤
---
由于本人使用 git 对项目进行版本控制管理，所以下列步骤中会有一些与 git 相关的操作。如果你没有使用 git，那么你可以直接忽略下列步骤中与 git 相关的操作。

###获取 AnySDK Framework
安装 AnySDK 客户端，然后在 AnySDK 客户端的【安妮市场】【分类】【框架】中找到 JS(Android) 框架并下载。如下图：
![f25635beee3f69c5224a0db5da811e64.png](http://moefq.com/images/2016/06/22/f25635beee3f69c5224a0db5da811e64.png)

下载完成后打开 JS(Android) 框架文件夹即可看到 ``AnySDKFiles/Store/Frameworks/AnySDK_Framework_JS_Android_2.1.2/AnySDK_Framework_JS(Android)/`` 这样的目录。如下图：
![c53ec646e60c26cea4b0a1b11a5e6457.png](http://moefq.com/images/2016/06/22/c53ec646e60c26cea4b0a1b11a5e6457.png)

下文统一使用 ``AnySDK_Framework_JS(Android)`` 指代 AnySDK Framework 文件目录。

###构建并编译 CocosCreator 项目

下文使用 ``your-proj`` 指代你的 CocosCreator 项目路径。

####修改项目中的 gitignore 文件。

删除 your-proj/.gitignore 文件中以下路径：

	build/

在 your-proj/.gitignore 文件中添加以下路径：

	build/jsb-default/src/
	build/jsb-default/res/
	build/jsb-default/publish/
	build/jsb-default/simulator/
	
	build/jsb-default/frameworks/runtime-src/proj.android/bin/
	build/jsb-default/frameworks/runtime-src/proj.android/gen/
	build/jsb-default/frameworks/runtime-src/proj.android/obj/
	build/jsb-default/frameworks/runtime-src/proj.android/assets/

	build/jsb-default/frameworks/cocos2d-x/cocos/platform/android/java/bin/
	build/jsb-default/frameworks/cocos2d-x/cocos/platform/android/java/gen/

####构建发布 Android 平台
使用 CocosCreator 打开项目，点击 【项目】【构建发布】，**发布平台选择 “Android”， 模板选择 “default”**。然后点击 “构建”按钮 对项目进行构建。构建完毕会在 your-proj 目录下生成一个 ``build/jsb-default/`` 目录。

构建完成后，点击 “编译”按钮 对项目进行编译，编译过程大概20分钟左右。编译完成后，安装编译生成的 apk 到手机上查看是否运行正常。

如果在构建和编译过程中出现错误，请参考 CocosCreator 官方的[跨平台发布游戏文档](http://www.cocos.com/docs/creator/publish/index.html)。

注意：该步骤中的编译操作并不是必须的，只是为了保证你的编译发布 Android 平台相关开发环境已经配置完成。这样在集成 AnySDK 的出现错误的时候，就可以排除开发环境配置相关的问题了。

###获取 anysdk jsb 绑定文件

####添加 anysdk jsb 文件
在 build/jsb-default/frameworks/cocos2d-x/ 目录下新建 ``anysdk`` 文件夹，然后拷贝 AnySDK_Framework_JS(Android)/ 目录下的 ``src`` 文件夹到新建的 anysdk 文件夹中。

####修改 build-cfg.json 文件
在 build/jsb-default/frameworks/runtime-src/proj.android/build-cfg.json 文件中添加：

	{
		"from": "../../cocos2d-x/anysdk/src",
		"to": ""
	}

如下图，**注意：不要遗漏了添加代码前面分隔的逗号 “,”。**
![1f49aec5017613daee5937b2b0459084.png](http://moefq.com/images/2016/06/22/1f49aec5017613daee5937b2b0459084.png)

####修改 jsb.js 文件
在 build/jsb-default/frameworks/cocos2d-x/cocos/scripting/js-bindings/script/jsb.js 文件中添加：

	if 	(jsb.fileUtils.isFileExist('jsb_anysdk_constants.js') || jsb.fileUtils.isFileExist('jsb_anysdk_constants.jsc')) {
    	if (cc.sys.os == cc.sys.OS_IOS || cc.sys.os == cc.sys.OS_ANDROID) {
        	require('jsb_anysdk_constants.js');
    	}
	}

	if (jsb.fileUtils.isFileExist('jsb_anysdk.js') || jsb.fileUtils.isFileExist('jsb_anysdk.jsc')) {
    	if (cc.sys.os == cc.sys.OS_IOS || cc.sys.os == cc.sys.OS_ANDROID) {
        	require('jsb_anysdk.js');
    	}
	}
	
如下图
![429e4831912ce5493a1d90614612d16f.png](http://moefq.com/images/2016/06/22/429e4831912ce5493a1d90614612d16f.png)

###拷贝anysdk framework stl库
####添加 protocols 文件

首先，查看所接入项目的 build/jsb-default/frameworks/runtime-src/proj.android/jni/application.mk 文件第一行找到 stl 库类型设置。 如下：

    APP_STL := gnustl_static

然后，进入所接入项目的 build/jsb-default/frameworks/runtime-src/proj.android 目录，新建 ``protocols`` 文件夹。根据上面查看到的stl类型，选取 AnySDK_Framework_JS(Android)/framework/protocols_gnustl_static/ 库，然后将该目录下的 ``android`` 和 ``include`` 文件夹拷贝到 protocols 目录下。

####添加 libPluginProtocol.jar 文件

在 build/jsb-default/frameworks/runtime-src/proj.android/ 目录下新建 ``libs`` 文件夹，然后将 build/jsb-default/frameworks/runtime-src/proj.android/protocols/android 目录下的 libPluginProtocol.jar 文件移到 libs 目录下。

####添加 res 文件

将 AnySDK_Framework_JS(Android)/framework/protocols_gnustl_static/ 目录下的 ``res`` 文件夹，拷贝到 build/jsb-default/frameworks/runtime-src/proj.android/ 目录下，**注意选择合并，避免文件覆盖**。 

###添加 Classes 文件
由于 CocosCreator 1.1.1 使用的是 lite 版本引擎，所以不能使用 AnySDK_Framework_JS(Android)/3.10及以上/ 目录下的文件。请下载 [anysdk-files-for-ccc](https://github.com/eddy2015/anysdk-files-for-ccc)，然后把 anysdk-files-for-ccc/build/jsb-default/frameworks/runtime-src/Classes/ 目录下的所有文件（除 AppDelegate.cpp 外），拷贝到 build/jsb-default/frameworks/runtime-src/Classes 目录下。

###修改 AppDelegate.cpp 文件
在 build/jsb-default/frameworks/runtime-src/Classes/AppDelegate.cpp 文件中添加：

```
#if (CC_TARGET_PLATFORM == CC_PLATFORM_ANDROID || CC_TARGET_PLATFORM == CC_PLATFORM_IOS)
#include "jsb_anysdk_basic_conversions.h"
#include "jsb_anysdk_protocols_auto.hpp"
#include "manualanysdkbindings.hpp"
#endif
```

```
#if (CC_TARGET_PLATFORM == CC_PLATFORM_ANDROID || CC_TARGET_PLATFORM == CC_PLATFORM_IOS)
	sc->addRegisterCallback(register_all_anysdk_framework);
	sc->addRegisterCallback(register_all_anysdk_manual);
#endif
```

如下图
![a34d1eef630b77357fd7b0da8a0eee8d.png](http://moefq.com/images/2016/06/22/a34d1eef630b77357fd7b0da8a0eee8d.png)

###修改 Android.mk 文件
在 build/jsb-default/frameworks/runtime-src/proj.android/jni/Android.mk 文件中添加：

```
$(call import-add-path,$(LOCAL_PATH)/../)
```

```
../../Classes/jsb/jsb_creator_auto.cpp \
../../Classes/jsb_anysdk_basic_conversions.cpp \
../../Classes/jsb_anysdk_protocols_auto.cpp \
../../Classes/manualanysdkbindings.cpp
```

```
LOCAL_WHOLE_STATIC_LIBRARIES := PluginProtocolStatic
```

```
$(call import-module, protocols/android)
```

如下图，**注意：记得在 ``../../Classes/AppDelegate.cpp
`` 后添加 “\”。**
![dbd9228816f437d2545a5ae9a4888fe0.png](http://moefq.com/images/2016/06/22/dbd9228816f437d2545a5ae9a4888fe0.png)

###修改 main.cpp 文件
在 build/jsb-default/frameworks/runtime-src/proj.android/jni/hellojavascript/main.cpp 文件中添加：

```
#include "PluginJniHelper.h"
```

```
using namespace anysdk::framework;
```

```
JavaVM* vm;
env->GetJavaVM(&vm);
PluginJniHelper::setJavaVM(vm);
```

如下图
![8cabdde9cc04010d4e2c4e298503dc38.png](http://moefq.com/images/2016/06/22/8cabdde9cc04010d4e2c4e298503dc38.png)

###修改 AndroidManifest.xml 文件
在 build/jsb-default/frameworks/runtime-src/proj.android/AndroidManifest.xml 文件中添加 AnySDK 所需要的权限：

```
	<!-- for anysdk start -->
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	<uses-permission android:name="android.permission.RESTART_PACKAGES" />
	<uses-permission android:name="android.permission.KILL_BACKGROUND_PROCESSES" />
	<!-- for anysdk end -->
```

如下图
![ff536e7787793ba702c09ae8cab9e0d8.png](http://moefq.com/images/2016/06/22/ff536e7787793ba702c09ae8cab9e0d8.png)

###修改 AppActivity.java 文件
在 build/jsb-default/frameworks/runtime-src/proj.android/src/org/cocos2dx/javascript/AppActivity.java 添加：

```
import com.anysdk.framework.PluginWrapper;
import android.content.Intent; 
import android.os.Bundle;
```

```
PluginWrapper.init(this); // for plugins
```

```
@Override
protected void onDestroy() {
	PluginWrapper.onDestroy();
	super.onDestroy();
}

@Override
protected void onPause() {
	PluginWrapper.onPause();
	super.onPause();
}

@Override
protected void onResume() {
	PluginWrapper.onResume();
	super.onResume();
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	PluginWrapper.onActivityResult(requestCode, resultCode, data);
	super.onActivityResult(requestCode, resultCode, data);
}

@Override
protected void onNewIntent(Intent intent) {
	PluginWrapper.onNewIntent(intent);
	super.onNewIntent(intent);
}

@Override
protected void onStop() {
	PluginWrapper.onStop();
	super.onStop();
}

@Override
protected void onRestart() {
	PluginWrapper.onRestart();
	super.onRestart();
}
```

如下图
![751912d2799f9251349eacd49e049c4c.png](http://moefq.com/images/2016/06/22/751912d2799f9251349eacd49e049c4c.png)

##在项目中使用AnySDK
---
###获取 AnySDK 参数
打开 AnySDK 客户端，点击 【打包工具】【游戏列表】【添加游戏】来在 AnySDK 客户端添加游戏项目。添加成功后，就可以获取到使用 AnySDK 需要的参数：appKey、appSecret、privateKey。

###在项目中添加 AnySDK 功能代码

下面给出了我在使用 AnySDK 用过的相关功能代码供大家参考。AnySDK 具体用法请参考[官方的客户端接入（JS）文档](http://docs.anysdk.com/JsAPI)。

```
// 初始化AnySDk
initAnySDK: function () {
    cc.log("WelcomeScreen.initAnySDK()");
    if (cc.sys.isMobile) {
        //注意：这里appKey, appSecret, privateKey，要替换成自己打包工具里面的值(登录打包工具，游戏管理界面上显示的那三个参数)。
        var appKey = "23BEE66E-48A1-9BF0-CF4A-4DA25FF54082";
        var appSecret = "b3d0c6f406c1f7d097f2036f445589b4";
        var privateKey = "FC69F26761B6C3BEDFCDA8A2D248CC45";
        var oauthLoginServer = "http://oauth.anysdk.com/api/OauthLoginDemo/Login.php";
        var agent = anysdk.agentManager;
        //init
        agent.init(appKey, appSecret, privateKey, oauthLoginServer);
        //load
        agent.loadAllPlugins();
        
        // 开启统计
        cc.log("analytics_plugin.startSession();");
        var analytics_plugin = anysdk.agentManager.getAnalyticsPlugin();
        if (analytics_plugin) {
            analytics_plugin.startSession();
        }
        
        // 开启推送服务
        cc.log("push_plugin.startPush();");
        var push_plugin = anysdk.agentManager.getPushPlugin();
        if (push_plugin) {
            push_plugin.startPush();
        }
    }
},

// 显示横幅广告
showBannerAd: function () {
    cc.log("WelcomeScreen.showBannerAd()");
    if (cc.sys.isMobile) {
        var ads_plugin = anysdk.agentManager.getAdsPlugin(); 
        if (ads_plugin.isAdTypeSupported(anysdk.AdsType.AD_TYPE_BANNER)) {
            ads_plugin.showAds(anysdk.AdsType.AD_TYPE_BANNER);
        } 
    }
},

// 显示插屏广告
showFullScreenAd: function () {
    cc.log("showFullScreenAd()");
    if (cc.sys.isMobile) {
        var ads_plugin = anysdk.agentManager.getAdsPlugin(); 
        if (ads_plugin.isAdTypeSupported(anysdk.AdsType.AD_TYPE_FULLSCREEN)) {
            ads_plugin.showAds(anysdk.AdsType.AD_TYPE_FULLSCREEN);
        } 
    }
},

// 统计事件
logEvent: function () {
    cc.log("logEvent()");
    if (cc.sys.isMobile) {
        var analytics_plugin = anysdk.agentManager.getAnalyticsPlugin();
        if (analytics_plugin) {
            analytics_plugin.logEvent("click_logevent_btn")
        }
    }
},

// 退出游戏
onExitBtnClicked: function () {
    cc.log("onExitBtnClicked()");
    
    // 关闭统计
    cc.log("analytics_plugin.stopSession();");
    if (cc.sys.isMobile) {
        var analytics_plugin = anysdk.agentManager.getAnalyticsPlugin();
        if (analytics_plugin) {
            analytics_plugin.stopSession();
        }
        // 在游戏结束或者适当的时候，调用unloadAllPlugins来卸载SDK插件
        anysdk.agentManager.unloadAllPlugins();
    } 
    
    cc.director.end();// 退出游戏
}
```

在项目中添加了 AnySDK 相关功能代码后，使用 CocosCreator 编译出 Android APK 母包。

###生成渠道包
在 AnySDK 客户端配置好游戏的渠道、SDK等参数，然后使用上一步骤编译出来的 Android APK 母包生成集成了 SDK 的渠道包。

在这里就不详细说明 AnySDK 客户端的使用了，详情请参考 [AnySDK 的官方文档](http://docs.anysdk.com/PackageTool)。

##如果快速集成 AnySDK
---
我在 GitHub 上面分享了一个便于在 CocosCreator 项目的 Android 平台快速集成 AnySDK 的项目：[anysdk-files-for-ccc](https://github.com/eddy2015/anysdk-files-for-ccc) 。具体使用方法请参考该项目的 README.md 文件，这里就不详细说明了。

##总结
---
本文主要讲解了如何在 CocosCreator 项目的 Android 平台集成 AnySDK ，并提供了一个快速集成方法。ios 平台请参考 AnySDK 的官方文档自行尝试。

本文出自 [Eddy's Blog](http://eddy2015.github.io/) 转载请注明出处：http://eddy2015.github.io/blog/2016/06/22/ccc-plus-anysdk/
