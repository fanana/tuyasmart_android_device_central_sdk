# 网关控制

网关 SDK 在 [IoT SDK](iot.md) 的基础上扩展了 Zigbee 网关和蓝牙网关的能力。

## 网关启动

### 获取网关 SDK 实例

???+ example "示例代码"

    === "Java"

        ```java
        private TuyaGatewaySdk mGateway = TuyaGatewaySdk.getInstance();
        ```

    === "Kotlin"
    
        ```kotlin
        var mGateway = TuyaGatewaySdk.getInstance()
        ```

### 注册回调函数

创建 `GatewayCallbacks ` 对象， 调用 ` TuyaGatewaySdk` 的 `setGatewayCallbacks` 函数注册。回调函数的实现参考 [回调函数] (#回调函数) 中说明。

??? example "示例代码"

    === "Java"

        ```java
        mGateway.setGatewayCallbacks(new GatewayCallbacks() {
            @Override
            public String onGetIP() {
                return null;
            }

            @Override
            public String onGetMacAddress() {
                return null;
            }

            @Override
            public void onStatusChanged(int status) {

            }

            @Override
            public void onReset(int type) {

            }

            @Override
            public void onReboot() {

            }

            @Override
            public void onDataPointCommand(int type, int dttType, String cid, String groupid, DataPoint[] dataPoint) {

            }

            @Override
            public void onRawDataPointCommand(int type, int dttType, String cid, int dpid, String groupid, byte[] data) {

            }

            @Override
            public void onDataPointQuery(String cid, int[] dpArray) {

            }

            @Override
            public void onNetworkStatus(int status) {

            }

            @Override
            public void onStartSuccess() {

            }

            @Override
            public void onStartFailure(int err) {

            }

            @Override
            public String onGetLogFile() {
                return null;
            }
        });
        ```

    === "Kotlin"

        ```kotlin
        mGateway.setGatewayCallbacks(object : GatewayCallbacks {
            override fun onStatusChanged(status: Int) {
                TODO("Not yet implemented")
            }

            override fun onReset(type: Int) {
                TODO("Not yet implemented")
            }

            override fun onReboot() {
                TODO("Not yet implemented")
            }

            override fun onDataPointCommand(type: Int, dttType: Int, cid: String?, groupid: String?, dataPoint: Array<out DataPoint>?) {
                TODO("Not yet implemented")
            }

            override fun onRawDataPointCommand(type: Int, dttType: Int, cid: String?, dpid: Int, groupid: String?, data: ByteArray?) {
                TODO("Not yet implemented")
            }

            override fun onDataPointQuery(cid: String?, dpArray: IntArray?) {
                TODO("Not yet implemented")
            }

            override fun onNetworkStatus(status: Int) {
                TODO("Not yet implemented")
            }

            override fun onStartSuccess() {
                TODO("Not yet implemented")
            }

            override fun onStartFailure(err: Int) {
                TODO("Not yet implemented")
            }

            override fun onGetLogFile(): String {
                TODO("Not yet implemented")
            }

            override fun onGetIP(): String {
                TODO("Not yet implemented")
            }

            override fun onGetMacAddress(): String {
                TODO("Not yet implemented")
            }
        })
        ```

### 配置 Zigbee 模块

**接口说明**

如果设备需要启用 zigbee 网关功能，调用该方法使能。

```java
void zigbeeEnable(Context context, final ZigbeeConfig config);
```

**参数说明**

| 参数    | 说明                                 |
| ------- | ------------------------------------ |
| Context | 上下文                               |
| ZigbeeConfig  | Zigbee 配置 |

**ZigbeeConfig**

| 成员         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| mSerialPort  | 串口终端（zigbee模组连接的串口号，根据板子实际焊接情况填写。）  |
| mTempDir     | 临时文件目录，该目录要在app中创建                         |
| mBinDir      | bin文件目录，该文件夹下不要存放其他文件，该目录要在app中创建 |
| mIsCTS       | 是否带流控                                                 |

??? example "示例代码"

    === "Java"

        ```java
        File filedirs = getFilesDir();
        String tmpDir = filedirs + File.separator + "tmp" + File.separator;
        file = new File(tmpDir);
        if (!file.exists()) {
            file.mkdir();
        }
        String binDir = filedirs + File.separator + "bin" + File.separator;
        file = new File(binDir);
        if (!file.exists()) {
            file.mkdir();
        }

        ZigbeeConfig zigbeeConfig = new ZigbeeConfig();
        zigbeeConfig.mBinDir = binDir;
        zigbeeConfig.mTempDir = tmpDir;
        zigbeeConfig.mSerialPort = "/dev/ttyS3";
        zigbeeConfig.mIsCTS = true;
        mGateway.zigbeeEnable(ctx, zigbeeConfig);
        ```

    === "Kotlin"

        ```kotlin
        val filedirs: File = getFilesDir()
        val tmpDir = filedirs.toString() + File.separator + "tmp" + File.separator
        file = File(tmpDir)
        if (!file.exists()) {
            file.mkdir()
        }
        val binDir = filedirs.toString() + File.separator + "bin" + File.separator
        file = File(binDir)
        if (!file.exists()) {
            file.mkdir()
        }
        val zigbeeConfig = ZigbeeConfig()
        zigbeeConfig.mBinDir = binDir
        zigbeeConfig.mTempDir = tmpDir
        zigbeeConfig.mSerialPort = "/dev/ttyS3"
        zigbeeConfig.mIsCTS = true
        mGateway.zigbeeEnable(ctx, zigbeeConfig)
        ```

### 配置 Bluetooth 模块

**接口说明**

如果设备需要启用蓝牙网关功能，调用该方法使能。

```java
void bluetoothEnable(Context context, final BluetoothConfig config);
```

**参数说明**

| 参数    | 说明                                 |
| ------- | ------------------------------------ |
| Context | 上下文                               |
| BluetoothConfig  | Bluetooth 配置 |

**BluetoothConfig**

| 成员         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| mMode | 蓝牙模式 |

??? example "示例代码"

    === "Java"

        ```java
        BluetoothConfig btConfig = new BluetoothConfig();
        btConfig.mMode = (TY_BT_MODE_ADV_MESH | TY_BT_MODE_BLE_MASTER);
        mGateway.bluetoothEnable(ctx, btConfig);
        ```

    === "Kotlin"

        ```kotlin
        val btConfig = BluetoothConfig()
        btConfig.mMode = TY_BT_MODE_ADV_MESH or TY_BT_MODE_BLE_MASTER
        mGateway.bluetoothEnable(ctx, btConfig)
        ```

### 启动网关

**接口说明**

```java
void gatewayStart(Context context, GatewayConfig config);
```

**参数说明**

| 参数    | 说明                                 |
| ------- | ------------------------------------ |
| Context | 上下文                               |
| GatewayConfig  | 网关配置 |

**GatewayConfig**

| 成员         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| mPath        | 存储路径（要求可读写分区），该目录要在app中创建                                |
| mFirmwareKey | 固件key或者pid，向涂鸦申请，产品唯一。<br/>和mIsOEM配合使用:<br/>mIsOEM为true时，mFirmwareKey为固件key<br/>mIsOEM为false时，mFirmwareKey为pid |
| mUUID        | 涂鸦IOT平台获取到的UUID（授权信息），和mAuthKey成对使用 |
| mAuthKey     |  涂鸦IOT平台获取到的Authkey（授权信息），和mUUID成对使用 |
| mIsOEM       | 是否是oem产品，和mFirmwareKey配合使用。           |
| mPackageName | App的包名，用于App/固件的OTA升级。 |
| mVersion     | App/设备固件的版本号，由用户设置，格式为 “xx.xx.xx”。<br/> 用于固件升级，设备激活后，版本号会在手机APP上设备详情中进行展示。 |
| mNetworkInterface | 设备的网卡名称，比如: wlan0, eth0。为null时SDK会使用系统默认的网卡。|
| mLogLevel | SDK输出log信息的级别  |


**返回值说明**

tuyaGatewayStart 是异步调用，其结果通过回调函数返回，启动成功回调函数 onStartSuccess，失败调用回调函数onStartFailure，参考 [网关启动结果回调](#网关启动结果回调) 中的说明。

??? example "示例代码"

    === "Java"

        ```java
        File filedirs = getFilesDir();
        String storageDir = filedirs + File.separator + "storage" + File.separator;
        File file = new File(storageDir);
        if (!file.exists()) {
            file.mkdir();
        }

        GatewayConfig config = new GatewayConfig();
        config.mFirmwareKey = "xxxxxxxxxx";
        config.mUUID = "xxxxxxxxxx";
        config.mAuthKey = "xxxxxxxxxxxxxxx";

        config.mIsOEM = true;
        config.mVersion = "1.0.0";
        config.mPackageName = getPackageName();
        config.mPath = storageDir;
        config.mLogLevel = TY_LOG_LEVEL_DEBUG;

        mGateway.gatewayStart(context, config);
        ```

    === "Kotlin"

        ```kotlin
        val filedirs: File = getFilesDir()
        val storageDir = filedirs.toString() + File.separator + "storage" + File.separator
        val file = File(storageDir)
        if (!file.exists()) {
            file.mkdir()
        }

        val config = GatewayConfig()
        config.mFirmwareKey = "xxxxxxxxxx"
        config.mUUID = "xxxxxxxxxx"
        config.mAuthKey = "xxxxxxxxxxxxxxx"

        config.mIsOEM = true
        config.mVersion = "1.0.0"
        config.mPackageName = getPackageName()
        config.mPath = storageDir
        config.mLogLevel = LogLevel.TY_LOG_LEVEL_DEBUG

        mGateway.gatewayStart(context, config)
        ```

## 网关激活

启动网关成功之后，才可以入网。

**接口说明**

```java
public int tuyaIotBindToken(String token);
```

**参数说明**

| 参数  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| token | 入网 token ，<br>需要登录涂鸦账号之后调用 Tuya Smart Android Home SDK 中的接口获取。<br>详情请参考 Tuya Smart Android Home SDK - 设备配网 - **获取配网 token** 的接口说明。 |

**返回值说明**

| 返回值  | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| 0       | 调用成功，请注意，调用成功不代表已经入网成功，入网结果通过 onNetworkStatus 通知。 |
| 非 0 值 | 调用失败，没有入网。                                         |

??? example "示例代码"
    
    === "Java"

        ```java
        TuyaHomeSdk.getActivatorInstance().getActivatorToken(homeId, new ITuyaActivatorGetToken() {
            @Override
            public void onSuccess(final String token) {
                threadPoolExecutor.execute(new Runnable() {
                    @Override
                    public void run() {
                        Log.e(TAG, "get token suc , iot bind token");
                        TuyaGatewaySdk.getInstance().tuyaIotBindToken(token);
                    }
                });
            }

            @Override
            public void onFailure(String errorCode, String errorMsg) {
                Log.e(TAG, "get token failed " + errorCode + ", " + errorMsg);
            }
        });
        ```

## DP 点上报

### 异步上报

**接口说明**

同步上传透传型 DP 点数据。

```java
public int tuyaIotReportDataPointJsonAsync(String devId, DataPoint[] dataPoint);
```

**参数说明**

| 参数      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| devId     | 如果是主设备，devId 是自设备 id；如果是网关 /soc/mcu，则 devId 为 null |
| dataPoint | dp 点信息，参考 TuyaGatewaySdk.DataPoint                     |

TuyaGatewaySdk.DataPoint

| 成员       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| mId        | 在涂鸦 IoT 平台上定义的功能点编号                            |
| mType      | 功能点的数据类型，支持的数据类型请参见涂鸦文档中心的功能点定义，参考[自定义功能](https://docs.tuya.com/zh/iot/configure-in-platform/function-definition/custom-functions?id=K937y38137c64):<br>TYPE_BOOL：布尔型<br>TYPE_VALUE：数值型<br>TYPE_STRING：字符串型<br>TYPE_ENUM：枚举型<br>TYPE_BITMAP：故障型<br> |
| mData      | 功能点的值，数据类型由 mType 指定。                          |
| mTimeStamp | 时间戳，值为 0 时采用当前的时间。                            |

### 同步上报

**接口说明**

```java
public int tuyaIotReportDataPointRawSync(String devId, int dataPointId, byte[] data, int timeout);
```

**参数说明**

| 返回值      | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| devId       | 如果是主设备，devId 是自设备 id；如果是网关 /soc/mcu，则 devId 为 null |
| dataPointId | 在涂鸦 IoT 平台上定义的功能点编号                            |
| data        | 透传型 dp 点数据                                             |
| timeout     | 函数阻塞超时时间，以秒为单位                                 |

## ATOP透传接口
**接口说明** 

```java
ATopResponse tuyaIotAtopPost(String api, String version, String postData);
```

**参数说明**

| 参数 |说明  |
| --- | --- |
| api | 接口名|
| version | 接口版本|
| postData | 上报云端的数据json字符串|

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| ATopResponse      | 包含errCode（错误码）和result（请求结果）两个字段。请求正常返回时errCode为0 |

??? example "示例代码"

    === "Java"

        ```java
        ATopResponse response = mGateway.tuyaIotAtopPost("tuya.xx.xx", "1.0", "");
        if (response.errCode == 0) {
            Log.d(TAG, "result: " + response.result);
        }
        ```


## 获取虚拟 ID

网关设备激活后，可以通过该方法获取设备的虚拟 ID 。

**接口说明**

```java
String getId()；
```

## 获取 SDK 版本信息

**接口说明**

```java
String getVersion();
```

## 网关重置

网关本地恢复出厂设置，网关会从云端解绑，并将子设备等数据信息全部删除。

**接口说明**

```java
public int gatewayReset();
```

网关重置后会触发[网关重置回调](#网关重置回调)

## 销毁网关 SDK 实例

**接口说明**

```java
void tuyaGatewayRelease();
```

## 回调函数

在调用 gatewayStart 之前应该注册 GatewayCallbacks 回调函数，以接收处理结果。

### 网关状态回调

**接口说明**

网关状态更新时回调。

```java
void onStatusChanged(int status);
```

**参数说明**

| 返回值 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| status | 状态更改通知回调函数：<br>STATUS_RESET：网关被重置<br>STATUS_ACTIVATED：网关被激活<br>STATUS_FIRST_START：网关第一次启动<br>STATUS_NORMAL：网关激活而且已经启动 |

### 网关重置回调

**接口说明**

网关被重置后回调，app 需要根据 type 的值做相应的操作。

```java
void onReset(int type);
```

**参数说明**

| 返回值 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| type   | 重置类型：<br>RESET_TYPE_LOCAL_RESET_FACTORY：本地恢复工厂设置<br>RESET_TYPE_REMOTE_UNACTIVE：远端网关重置，即为通过 APP 取消与账号绑定<br>RESET_TYPE_LOCAL_UNACTIVE：本地网关重置，取消与账号绑定<br>RESET_TYPE_REMOTE_RESET_FACTORY：远端恢复工厂设置<br/>RESET_TYPE_RESET_DATA_FACTORY：恢复数据工厂设置 |



### 重启请求回调

**接口说明**

网关请求重启设备。

```java
void onReboot();
```

### 网关网络状态回调

**接口说明**

网关已经被重置，app 需要根据 type 的值做相应的操作。

```java
void onNetworkStatus(int status);
```

**参数说明**

| 返回值 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| type   | 网络状态：<br>CLOUD_UNCONNECTED：联网失败<br>CLOUD_CONNECTED：云端连接成功，设备已经激活 |



### 网关 DP 点下发回调

**接口说明**

DP 点下发。

```java
void onDataPointCommand(@DataPointCommandType int type, @DataPointTransportType int dttType, String cid, String groupid, DataPoint[] dataPoint);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| type      | 指令类型：<br>DATAPOINT_CMD_LAN：LAN 触发<br>DATAPOINT_CMD_MQ：MQTT 触发<br>DATAPOINT_CMD_TIMER：本地定时触发<br>DATAPOINT_CMD_SCENE_LINKAGE：场景联动触发<br/>DATAPOINT_CMD_RELIABLE_TRANSFER：重发 |
| dttType   | 传输方式：<br/>DATAPOINT_DTT_SCT_UNC：LAN 触发<br/>DATAPOINT_DTT_SCT_BNC：MQTT 触发<br/>DATAPOINT_DTT_SCT_MNC：本地定时触发<br/>DATAPOINT_DTT_SCT_SCENE：场景联动触发 |
| cid       | cid == NULL 表示控制的网关的功能点；cid != NULL 表示控制的网关子设备的功能点，其中 cid 是子设备的唯一 ID |
| groupid   | 群组 ID，只有当 dtt_tp = 2 时，该字段才有效。                |
| dataPoint | dp点数据，DataPoint类型参考[DP点异步上报](#DP点异步上报)中的说明。 |

### 网关 DP 点透传数据下发回调
用于透传用户自定义的 DP 点数据

**接口说明**

```java
void onRawDataPointCommand(@DataPointCommandType int type, @DataPointTransportType int dttType, String cid,
                               int dpid, String groupid, byte[] data);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| type      | 指令类型：<br>DATAPOINT_CMD_LAN：LAN 触发<br>DATAPOINT_CMD_MQ：MQTT 触发<br>DATAPOINT_CMD_TIMER：本地定时触发<br>DATAPOINT_CMD_SCENE_LINKAGE：场景联动触发<br/>DATAPOINT_CMD_RELIABLE_TRANSFER：重发 |
| dttType   | 传输方式：<br/>DATAPOINT_DTT_SCT_UNC：LAN 触发<br/>DATAPOINT_DTT_SCT_BNC：MQTT 触发<br/>DATAPOINT_DTT_SCT_MNC：本地定时触发<br/>DATAPOINT_DTT_SCT_SCENE：场景联动触发 |
| cid       | cid == NULL 表示控制的网关的功能点；cid != NULL 表示控制的网关子设备的功能点，其中 cid 是子设备的唯一 ID |
| dpid      |  DP 点的 ID |
| groupid   | 群组 ID，只有当 dtt_tp = 2 时，该字段才有效。                |
| data  | DP 点数据 |


### 网关 DP 点数据查询回调

用于设备特定数据查询

**接口说明**

```java
void onDataPointQuery(String cid, int[] dpArray);
```

**参数说明**

| 返回值    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| cid      | cid == NULL 表示控制的网关的功能点；cid != NULL 表示控制的网关子设备的功能点，其中 cid 是子设备的唯一 ID |
| dpArray | 用户定义功能点序号集 |

### ~~获取 IP 回调~~

**接口说明**

获取设备IP，已废弃。

```java
String onGetIP();
```

### ~~获取 MAC 地址回调~~

**接口说明**

获取设备 MAC 地址，已废弃。

```java
String onGetMacAddress();
```

### 网关启动结果回调

**接口说明**

tuyaIotStart 成功时调用 onStartSuccess 回调。

```java
void onStartSuccess();
```

**接口说明**

tuyaIotStart 失败时调用 onStartFailure 回调。

   ```java
void onStartFailure(int err);
   ```

**参数说明**

| 返回值 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| type   | 错误码，为 GatewayError 中定义的值之一，常见错误：<br>ERROR_COM_ERROR：网络连接错误<br>ERROR_INVALID_PARM：参数错误<br>ERROR_INVALID_STATUS：状态错误 |

### 获取日志文件回调

**接口说明**

```java
String onGetLogFile();
```

**返回值说明**

| 返回值 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| String | 需要保存的日志文件路径 |