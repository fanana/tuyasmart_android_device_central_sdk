# 语音控制

涂鸦语音 SDK 提供了 TTS 播报、ASR 文本、IOT 技能（设备控制、设备接入）、音乐（音乐、天气、相声、电台等）、提醒、语音媒体控制（麦克风开启/关闭、播放/暂停/上一首/下一首、蓝牙开启/关闭、闹钟、音量设置）、扩展 ATOP 接口（自定义云端交互业务）等能力。

该 SDK 适合已经具备语音前端处理能力的使用者。

## 获取语音 SDK 实例

???+ example "示例代码"

    === "Java"

        ```java
        TuyaVoiceSdk mVoice = TuyaVoiceSdk.getInstance();
        ```

    === "Kotlin"

        ```kotlin
        var mVoice = TuyaVoiceSdk.getInstance()
        ```

## 语音 SDK 初始化

**接口说明**

```java
init(ISpeechHelper callback)
```

**参数说明**

| 参数     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| ISpeechHelper      | [语音数据处理协议](#语音数据处理协议)              |

??? example "示例代码"

    === "Java"

        ```java
        mVoice.init(new ISpeechHelper() {
                    @Override
                    public boolean onSetVolume(int vol) {
                        return false;
                    }

                    @Override
                    public boolean onControlCommand(int ctl) {
                        return false;
                    }

                    @Override
                    public void onSyncAudio() {

                    }

                    @Override
                    public void onSyncDialogText(String speaker, int id, String text) {

                    }

                    @Override
                    public void onAlarmClock(String alarm) {

                    }

                    @Override
                    public void onTaskAlert(int type, String msg) {

                    }

                    @Override
                    public boolean onSetNickName(int mode, String nickname, String pinyin) {
                        return false;
                    }

                    @Override
                    public void onThingConfig(int mode, String token, int timeout) {

                    }

                    @Override
                    public void onUploadMediaStatus(int status) {

                    }

                    @Override
                    public void onCloudMedia(MediaAttribute[] mediaAttributes) {

                    }

                    @Override
                    public void onCloudExtMsg(String type, String msg) {

                    }
                });
        ```

    === "Kotlin"

        ```kotlin
        mVoice.init(object : ISpeechHelper {
            override fun onSetVolume(vol: Int): Boolean {
                return false
            }

            override fun onControlCommand(ctl: Int): Boolean {
                return false
            }

            override fun onSyncAudio() {}
            override fun onSyncDialogText(speaker: String, id: Int, text: String) {}
            override fun onAlarmClock(alarm: String) {}
            override fun onTaskAlert(type: Int, msg: String) {}
            override fun onSetNickName(mode: Int, nickname: String, pinyin: String): Boolean {
                return false
            }

            override fun onThingConfig(mode: Int, token: String, timeout: Int) {}
            override fun onUploadMediaStatus(status: Int) {}
            override fun onCloudMedia(mediaAttributes: Array<MediaAttribute>) {}
            override fun onCloudExtMsg(type: String, msg: String) {}
        })
        ```


## 语音数据上报
**接口说明** 

设备端上报采集到的语音数据，依次调用开启、上传、结束接口。
云端在识别到意图后，会触发 [语音数据处理协议](#语音数据处理协议) 回调执行对应的动作。

支持的语音 PCM 数据格式：

 参数 | 值
 ----- | --- 
 通道数 | 单通道 
 采样率 | 16000 
 采样精度 | 16bit 


??? example "示例代码"

    === "Java"

        ```java
        private static final int SAMPLING_RATE_IN_HZ = 16000;
        private static final int CHANNEL_CONFIG = AudioFormat.CHANNEL_IN_MONO;
        private static final int AUDIO_FORMAT = AudioFormat.ENCODING_PCM_16BIT;
        private static final int BUFFER_SIZE_FACTOR = 4;
        private static final int BUFFER_SIZE = AudioRecord.getMinBufferSize(SAMPLING_RATE_IN_HZ,
                CHANNEL_CONFIG, AUDIO_FORMAT) * BUFFER_SIZE_FACTOR;
        private final AtomicBoolean recordingInProgress = new AtomicBoolean(false);
        private AudioRecord recorder = null;
        private Thread recordingThread = null;

        private class RecordingRunnable implements Runnable {

            @Override
            public void run() {
                final byte[] buffer = new byte[BUFFER_SIZE];

                while (recordingInProgress.get()) {
                    int result = recorder.read(buffer, 0, BUFFER_SIZE);
                    if (result < 0) {
                        throw new RuntimeException("Reading of audio buffer failed: " +
                                getBufferReadFailureReason(result));
                    }
                    mVoice.voiceUploadMediaUpload(buffer);
                }

                mVoice.voiceUploadMediaStop();
            }

            private String getBufferReadFailureReason(int errorCode) {
                switch (errorCode) {
                    case AudioRecord.ERROR_INVALID_OPERATION:
                        return "ERROR_INVALID_OPERATION";
                    case AudioRecord.ERROR_BAD_VALUE:
                        return "ERROR_BAD_VALUE";
                    case AudioRecord.ERROR_DEAD_OBJECT:
                        return "ERROR_DEAD_OBJECT";
                    case AudioRecord.ERROR:
                        return "ERROR";
                    default:
                        return "Unknown (" + errorCode + ")";
                }
            }
        }
        
        private void startRecording() {
            recorder = new AudioRecord(MediaRecorder.AudioSource.VOICE_RECOGNITION, SAMPLING_RATE_IN_HZ,
                    CHANNEL_CONFIG, AUDIO_FORMAT, BUFFER_SIZE);

            mVoice.voiceUploadMediaStart();
            recorder.startRecording();

            recordingInProgress.set(true);

            recordingThread = new Thread(new RecordingRunnable(), "Recording Thread");
            recordingThread.start();
        }

        private void stopRecording() {
            if (null == recorder) {
                return;
            }

            recordingInProgress.set(false);

            recorder.stop();

            recorder.release();

            recorder = null;

            recordingThread = null;
        }
        ```

    === "Kotlin"

        ```kotlin
        private val SAMPLING_RATE_IN_HZ = 16000
        private val CHANNEL_CONFIG = AudioFormat.CHANNEL_IN_MONO
        private val AUDIO_FORMAT = AudioFormat.ENCODING_PCM_16BIT
        private val BUFFER_SIZE_FACTOR = 4
        private val BUFFER_SIZE = AudioRecord.getMinBufferSize(SAMPLING_RATE_IN_HZ,
                CHANNEL_CONFIG, AUDIO_FORMAT) * BUFFER_SIZE_FACTOR
        private val recordingInProgress = AtomicBoolean(false)
        private var recorder: AudioRecord? = null
        private var recordingThread: Thread? = null

        private class RecordingRunnable : Runnable {
            override fun run() {
                val buffer = ByteArray(BUFFER_SIZE)
                while (recordingInProgress.get()) {
                    val result: Int = recorder.read(buffer, 0, BUFFER_SIZE)
                    if (result < 0) {
                        throw RuntimeException("Reading of audio buffer failed: " +
                                getBufferReadFailureReason(result))
                    }
                    mVoice.voiceUploadMediaUpload(buffer)
                }
                mVoice.voiceUploadMediaStop()
            }

            private fun getBufferReadFailureReason(errorCode: Int): String {
                return when (errorCode) {
                    AudioRecord.ERROR_INVALID_OPERATION -> "ERROR_INVALID_OPERATION"
                    AudioRecord.ERROR_BAD_VALUE -> "ERROR_BAD_VALUE"
                    AudioRecord.ERROR_DEAD_OBJECT -> "ERROR_DEAD_OBJECT"
                    AudioRecord.ERROR -> "ERROR"
                    else -> "Unknown ($errorCode)"
                }
            }
        }

        private fun startRecording() {
            recorder = AudioRecord(MediaRecorder.AudioSource.VOICE_RECOGNITION, SAMPLING_RATE_IN_HZ,
                    CHANNEL_CONFIG, AUDIO_FORMAT, BUFFER_SIZE)
            mVoice.voiceUploadMediaStart()
            recorder!!.startRecording()
            recordingInProgress.set(true)
            recordingThread = Thread(RecordingRunnable(), "Recording Thread")
            recordingThread!!.start()
        }

        private fun stopRecording() {
            if (null == recorder) {
                return
            }
            recordingInProgress.set(false)
            recorder!!.stop()
            recorder!!.release()
            recorder = null
            recordingThread = null
        }
        ```

## 语音数据处理协议

### 音量设置

App 需要根据下发的值去设置系统的音量。

**接口说明**

```java
boolean onSetVolume(int vol);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| vol | 音量值，取值 0 ~ 100 |

**返回值**

将设置的结果返回。

??? example "示例代码"

    === "Java"

        ```java
        @Override
        public boolean onSetVolume(int vol) {
            Log.d(TAG, "onSetVolume:" + vol);
            int max = mAudioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC);

            try {
                mAudioManager.setStreamVolume(AudioManager.STREAM_MUSIC, (vol * max) / 100, AudioManager.FLAG_SHOW_UI);
            } catch (SecurityException e) {
                e.printStackTrace();
            }
            return true;
        }
        ```

    === "Kotlin"

        ```kotlin
        override fun onSetVolume(vol: Int): Boolean {
            Log.i(TAG, "onSetVolume:$vol")
            val max = mAudioManager!!.getStreamMaxVolume(AudioManager.STREAM_MUSIC)
            try {
                mAudioManager!!.setStreamVolume(AudioManager.STREAM_MUSIC, vol * max / 100, AudioManager.FLAG_SHOW_UI)
            } catch (e: SecurityException) {
                e.printStackTrace()
            }
            return true
        }
        ```

### 媒体控制

网关下发的媒体控制指令，App 需要根据下发的指令执行对于的操作。

**接口说明**

```java
boolean onControlCommand(@ControlCommandType int ctl);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| ctl     | 媒体控制指令，值为 CTRL_CMD_XXX 之一：<br/>CTRL_CMD_MIC_OPEN<br/>CTRL_CMD_MIC_CLOSE<br/>CTRL_CMD_PLAY<br/>CTRL_CMD_PAUSE<br/>CTRL_CMD_BT_PLAY_OPEN<br/>CTRL_CMD_BT_PLAY_CLOSE<br/>CTRL_CMD_PLAY_NEXT<br/>CTRL_CMD_PLAY_PREV|

**返回值**

将设置的结果返回。

### 音频播放进度同步

云端下发同步音频播放进度的通知。

**接口说明**

```java
void onSyncAudio();
```

### TTS 文本同步

语音对话的文本同步回调，APP 或设备端接收该消息后可以实时在界面上展示。

**接口说明**

```java
void onSyncDialogText(String speaker, int id, String text);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| speaker | 说话对象， 值为 "human" 或 "robot" |
| id | 该记录的id号 |
| text | 语音对应的文本 |


### 本地闹钟

本地闹钟触发回调

**接口说明**

```java
void onAlarmClock(String alarm);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
|   alarm   | 本地闹钟触发的事件，JSON格式的字符串 |


### 任务提醒

云端下发的任务提醒通知。

**接口说明**

```java
void onTaskAlert(@TaskAlertType int type, String msg);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
|   type   | 任务类型，值为 TASK_TYPE_XXX 之一：<br/>TASK_TYPE_NORMAL<br/>TASK_TYPE_CLOCK<br/>TASK_TYPE_ALERT<br/>TASK_TYPE_RING_TONE<br/>TASK_TYPE_CALL<br/>TASK_TYPE_CALL_TTS<br/>TASK_TYPE_INVALD |
|   msg  | 下发的提醒事件，JSON格式的字符串 |

### 昵称设置

App 需根据状态调用前端语音接口设置或删除昵称。

**接口说明**

```java
boolean onSetNickName(@SetNickNameType int mode, String nickname, String pinyin);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
|  mode   |  昵称设状态，值为 NICKNAME_XXX 之一：<br/>NICKNAME_SET<br/>NICNAME_DEL<br/>NICKNAME_INVALD |
| nickname | 昵称中文名 |
| pinyin | 昵称拼音 |

**返回值**

将设置的结果返回。

### 子设备语音配网

云端识别到配网意图后，触发开启/关闭子设备配网请求回调。

**接口说明**

```java
void onThingConfig(@ThingConfigType int mode, String token, int timeout);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| mode   | 配网状态，值为 THING_CONFIG_XXX 之一：<br/>THING_CONFIG_START<br/>THING_CONFIG_STOP<br/>THING_CONFIG_INVALD |
| token | 给wifi设备配网时需要token |
| timeout | 配网超时时间 |

### 语音上传状态

语音上传云端，出现异常将触发该回调。

**接口说明**

```java
void onUploadMediaStatus(@UploadMediaStatusType int status);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| status  |  语音上传状态， 值为 UPLOAD_XXX 之一:<br/>UPLOAD_NONE<br/>UPLOAD_NETWORK_ERR |


### 云端媒体数据下发

收到云端媒体数据时触发该回调。

**接口说明**

```java
void onCloudMedia(MediaAttribute[] mediaAttributes);
```

**MediaAttribute**

| 成员         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| mDecodeType  | 音频编码类型，值为 DECODER_TYPE_XXX 之一:<br/>DECODER_TYPE_WAV<br/>DECODER_TYPE_MP3<br/>DECODER_TYPE_SPEEX<br/>DECODER_TYPE_AAC<br>DECODER_TYPE_AMR<br/>DECODER_TYPE_M4A<br/>DECODER_TYPE_PCM<br/>DECODER_TYPE_INVALD|
| mLength | 数据大小长度 |
| mDuration | 音频的时长 |
| mMediaType | 媒体类型，值为 MEDIA_TYPE_XXX 之一:<br/>MEDIA_TYPE_MEDIA<br/>MEDIA_TYPE_TTS<br/>MEDIA_TYPE_INVALD|
| mUrl | 音频文件的 URL |
| mFollowAction | 是否继续对话，值为FOLLOW_ACTION_KEEP_SESSION或FOLLOW_ACTION_NO_KEEP_SESSION|
| mSessionId | 对话的 ID |

??? example "示例代码"

    === "Java"

        ```java
        @Override
        public void onCloudMedia(MediaAttribute[] mediaAttributes) {
            requestSpeakerMedia(mediaAttributes);
        }
        ```

        ```java
        private void requestSpeakerMedia(final MediaAttribute[] mediaAttributes) {
            CountDownLatch latch = new CountDownLatch(mediaAttributes.length);
            ArrayList<String> list = new ArrayList<>();
            boolean isDialog = false;
            for (MediaAttribute mediaAttribute : mediaAttributes) {
                if (!TextUtils.isEmpty(mediaAttribute.mUrl)) {
                    String name = String.format(Locale.getDefault(),
                            "/sdcard/tuya_speech_files/%s_%d_speaker_tmp.mp3",
                            mediaAttribute.mMediaType == MediaAttribute.MEDIA_TYPE_TTS ? "tts" : "audio",
                            System.currentTimeMillis());
                    if (mediaAttribute.mMediaType == MediaAttribute.MEDIA_TYPE_TTS) {
                        list.add(0, name);
                        if (mediaAttribute.mFollowAction == 1) {
                            isDialog = true;
                        }
                    } else {
                        list.add(name);
                    }
                    requestSpeakerMedia(mediaAttribute.mUrl, mediaAttribute.mRequestBody, name, latch);
                }
            }
            try {
                latch.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            if (mSpeechHandler != null) {
                mSpeechHandler.removeMessages(SpeechCallback.TIMEOUT_MSG_WHAT);

                //过滤空文件
                if (list.size() > 0) {
                    for (int i = list.size() - 1; i >= 0; i--) {
                        File file = new File(list.get(i));
                        if (!file.exists()) {
                            list.remove(i);
                        }
                    }
                }

                if (list.size() > 0) {
                    Message msg = Message.obtain();
                    msg.obj = list;
                    if (isDialog) {
                        msg.what = SpeechCallback.DIALOG_MSG_WHAT;
                    } else {
                        msg.what = SpeechCallback.SUCCESS_MSG_WHAT;
                    }
                    mSpeechHandler.sendMessage(msg);
                }
            }
        }

        private void requestSpeakerMedia(final String url, String requestStr, final String name, final CountDownLatch latch) {

            Request request;
            if (TextUtils.isEmpty(requestStr)) {
                request = new Request.Builder()
                        .url(url)
                        .get()
                        .build();
            } else {
                MediaType JSON = MediaType.parse("application/json; charset=utf-8");
                RequestBody requestBody = RequestBody.create(JSON, requestStr);
                request = new Request.Builder()
                        .url(url)
                        .post(requestBody)
                        .build();
            }

            mHttpClient.newCall(request).enqueue(new Callback() {
                @Override
                public void onFailure(Call call, IOException e) {
                    latch.countDown();
                    Log.w(TAG, "requestSpeakerMedia onFailure");
                }

                @Override
                public void onResponse(Call call, Response response) {
                    Log.w(TAG, "requestSpeakerMedia onResponse");

                    if (response.body() == null) {
                        latch.countDown();
                        return;
                    }

                    InputStream inputStream = response.body().byteStream();
                    FileOutputStream fileOutputStream;

                    try {
                        File file = new File(name);

                        if (file.exists()) {
                            file.delete();
                        }

                        fileOutputStream = new FileOutputStream(file);
                        byte[] buffer = new byte[1024];
                        int length = 0;
                        while ((length = inputStream.read(buffer)) != -1) {
                            fileOutputStream.write(buffer, 0, length);
                        }
                        fileOutputStream.flush();

                        if (file.length() <= 0) {
                            file.delete();
                            Log.w(TAG, name + " empty");
                        } else {
                            Log.w(TAG, name + " saved");
                        }
                    } catch (Exception e) {
                        Log.e(TAG, "" + e.getMessage());
                        e.printStackTrace();
                    } finally {
                        latch.countDown();
                    }
                }
            });
        }
        ```

    === "Kotlin"

        ```kotlin
        ```

### OEM 定制数据透传

透传OEM定制的 MQTT 501 接口，具体协议内容由云端与OEM客户制定，并提供说明。

**接口说明**

```java
void onCloudExtMsg(String type, String msg);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| type | 接口类型 |
| msg | Json格式消息 |


## 音乐/音频控制

### 音频切换

#### 播放上一首

**接口说明**

```java
int playPrev();
```

#### 播放下一首

**接口说明**

```java
int playNext();
```

### 获取播放模式

**接口说明**

```java
int getPlayerMode();
```

**返回值说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| int(PlayerMode) | 云端播放器播放模式，值为MODE_XXX之一：<br/>MODE_NORMAL:顺序播放<br/>MODE_SHUFFLE:随机播放<br/>MODE_REPEAT_ONE:单曲循环播放<br/>MODE_REPEAT_ALL:列表循环播放|

### 设置播放模式

**接口说明**

```java
boolean setPlayerMode(@PlayerMode int mode);
```

### 获取当前播放的歌曲信息

**接口说明**

```java
AudioBean getCurrentAudio()；
```

### 获取当前的播放列表

**接口说明**

```java
List<AudioBean> getPlayList(int start, int end)
```

**返回值说明**
返回 [AudioBean](#AudioBean)

**参数说明**

| 参数 | 含义 |
| ---- | ----- |
| start | 列表起始下标  |
| end  |  列表结束下标 |

**返回值说明**

返回 [AudioBean](#AudioBean) 的列表


### AudioBean

| 成员         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| indexId  | 在播放列表中的下标 |
| audioId  | 音频 ID |
| albumId | 专辑 ID |
| albumName | 专辑名字 |
| audioName | 音频名字 |
| artist | 艺术家 |
| imgUrl | 封面图片的 URL |
| source | 音频提供商 ID |
| audioUrl | 音频文件的 URL |
| duration | 音频的时长 |