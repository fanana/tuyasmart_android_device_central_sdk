## 语音助手

> 该功能需要是没有语音前端处理能力的情况下接入的功能，如果已经有语音前端处理的能力，可以跳过该章节直接查看[语音SDK](voice_sdk.md)。如果需要对接该能力，需要协调涂鸦项目经理申请配置。

### 产品配置
将涂鸦提供的资源文件和配置文件放置到设备存储的同一目录下。

**配置示例**   
config.json
### 开启
通过 TuyaGatewaySdk 单例开启语音服务, 开启成功后可通过关键词唤醒，唤醒后可接收语音命令。

```json
{
	"PRODUCT_ID": "产品ID",
	"USER_ID": "用户ID",
	"PRODUCT_KEY": "Product Key",
	"PRODUCT_SECRET": "Product Secret",
	"API_KEY": "产品授权秘钥，服务端生成"
}
```

### 初始化
创建SpeechHelper对象

**接口说明** 

```java
SpeechHelper(Context context, String configPath, String audioPath, final SpeechCallback callback, int commandTimeout, String deviceId)
```
**参数说明**

| 参数 |说明  |
| --- | --- |
| context | 上下文|
| configPath | 资源及配置存放路径|
| audioPath | 音频文件存放路径|
| callback | 语音助手事件回调|
| commandTimeout | 语音命令超时时间|
| deviceId | 设备id |

**示例代码**

```kotlin
helper = SpeechHelper(this, "/sdcard/tuya_speech_config/", "/sdcard/tuya_speech_audio/",object : SpeechCallback {
	/**
     * 启动完成
     */
    fun onInitComplete()

    /**
     * 反初始化完成
     */
    fun onDeInitComplete()

    /**
     * 语音监听中
     */
    fun onStartListening()

    /**
     * 是否开启唤醒后监听
     */
    fun onWakeup(): Boolean

    /**
     * 检测到用户开始说话
     */
    fun onSpeechBeginning(errCode: Int)

    /**
     * 检测到用户结束说话
     */
    fun onSpeechEnd(errCode: Int)

    /**
     * 获取权限失败
     */
    fun onPermissionDenied()

    /**
     * 语音指令回复结果 (success: 是否成功, isDialog: 是否为对话, audioPath: 音频文件存放路径)
     */
    fun onResponse(success: Boolean, isDialog: Boolean, audioPath: ArrayList<String>?)

    /**
     * asr识别过程中发生错误
     */
    fun onASRError(errMsg: String)

    /**
     * 初始化错误
     */
    fun onInitError(errMsg: String)

    /**
     * 反初始化错误
     */
    fun onDeInitError(errMsg: String)

    /**
     * 接收command
     */
    fun onCommand(command: String, data: String)

    /**
     * 需要注册命令时，返回命令字符串数组
     */
    fun getCommands(): Array<String>
        }, 8000)
```

### 绑定
SpeechHelper 与语音通道SDK 通过接口绑定，打通语音上传云端逻辑。

**接口说明** 

```java
TuyaVoiceSdk.getInstance().init(SpeechHelper helper)
```

### 开启
开启语音服务, 开启成功后可通过关键词唤醒，唤醒后可接收语音命令。

```java
void start()
```

### 关闭
关闭语音服务， 进入不可唤醒状态。

```java
void stop()
```