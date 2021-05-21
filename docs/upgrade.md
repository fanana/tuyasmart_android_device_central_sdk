## OTA 升级

### 设置升级回调

**接口说明**

通过 TuyaGatewaySdk 单例设置回调实现

```java
void setUpgradeCallback(UpgradeEventCallback upgradeCallback);
```

**参数说明**

| 参数 |说明  |
| --- | --- |
| upgradeCallback | OTA 事件回调 |

**回调说明**

```java
public interface UpgradeEventCallback {

/**
    * sdk 接收到后端的升级推送的时候，会触发此接口 附带升级信息
    * @param version
    */
void onUpgradeInfo(String version);

/**
    * 升级文件开始下载
    */
void onUpgradeDownloadStart();

/**
    * 升级文件下载进度
    */
void onUpgradeDownloadUpdate(int progress);

/**
    * sdk 下载升级文件下载完成触发此接口
    */
void upgradeFileDownloadFinished(boolean success);

/**
    * 升级失败
    * @param msg 错误信息
    */
void onUpgradeFail(String msg);
}
```

### 上传升级固件
1. 准备 ROM 升级文件 update.zip（非必须）
2. 打升级包 app.apk（必须）
3. 准备版本文件 version.json，内容为：

	```
		{
			"apkVersion": "1.2.0",  // apk版本号
    		"romVersion": "1565694425",   //  系统升级文件版本，升级压缩包的/META-INF/com/android/metadata `post-timestamp`
		}
	```

4. 将上面三个(或两个)文件压缩为 `标识名_OTA_版本号.bin`，例：`test_acs_rk3399-all_OTA_1.0.2.bin`
5. 在固件及版本管理后台上传
6. 在固件升级管理后台添加升级


