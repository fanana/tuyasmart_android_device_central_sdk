## SDK 集成

!!! warning
    在集成 **Android 设备 SDK** 前，请确保已经完成了 **[Tuya Home SDK 的集成](https://tuyainc.github.io/tuyasmart_home_android_sdk_doc/zh-hans/resource/Integrated.html)** 以及基础功能的开发
    
    如：扫码登录、家庭管理等基础部分

### 配置 build.gradle 文件

在安卓项目的 `build.gradle` 文件里，添加集成准备中下载的 dependencies 依赖库。

```groovy
dependencies {
   //SDK 最新稳版本：
	implementation 'com.tuya.smart:tuyasmart-lego_sdk:0.0.8-central-rc.7'
   
   //使用到的三方依赖
   implementation 'org.apache.commons:commons-io:1.3.2'
   implementation 'org.jetbrains.kotlin:kotlin-android-extensions-runtime:1.3.61'
   implementation 'com.google.code.gson:gson:2.8.5'
   implementation 'com.alibaba:fastjson:1.1.67.android'
   implementation 'io.reactivex.rxjava2:rxjava:2.2.8'
   implementation 'com.squareup.okhttp3:okhttp:3.9.0'
   implementation 'pub.devrel:easypermissions:2.0.1'
}
```
   
在根目录的 `build.gradle` 文件，中增加涂鸦 IoT Maven 仓库地址，进行仓库配置。
  
```groovy
repositories {
	jcenter()
	google()
	// 涂鸦 IoT 仓库地址
	maven {
		url "https://maven-other.tuya.com/repository/maven-releases/"
	}
}
```

### 权限要求

在 app 的应用清单文件 `AndroidManifest.xml` 中添加如下的权限

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE"
      android:required="false" /> <!-- 网络 -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.BLUETOOTH"/>
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
```
