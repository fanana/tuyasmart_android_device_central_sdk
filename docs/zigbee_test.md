## Zigbee 模块产测

### 设置产测回调

**接口说明**

通过 TuyaGatewaySdk 单例设置回调实现

```java
void setZigbeeTestCallbacks(ZigbeeTestCallbacks callbacks)；
```

**参数说明**

| 参数 |说明  |
| --- | --- |
| ZigbeeTestCallbacks | Zigbee 产测回调 |

**回调说明**

```java
/** Zigbee 测试结果
* @param receiveCount 接收到的包的个数
*/
void onZigbeeTestResult(int receiveCount) {

}
```

### 启动产测

**接口说明**

Zigbee 产测需要在网关初始化完成后进行。

```java
int zigbeeTestStart(final int channel, final int packageCount, final int timeout);
```

**参数说明**

| 参数 |说明  |
| --- | --- |
| channel | zigbee dongle 的信道 |
| packageCount | 测试总共发送多少个包 |
| timeout | 测试时长，单位为秒，建议10秒以上 |


??? example "示例代码"

    === "Java"

        ```java
        mGateway.setZigbeeTestCallbacks(new ZigbeeTestCallbacks() {
            @Override
            public void onZigbeeTestResult(int receiveCount) {
                int result;
                Log.d(TAG, "Received packages:" + receiveCount);
                //判断规定时间内收到的包个数是否满足测试要求
                if (receiveCount < 12) {
                    Log.i(TAG, "Zigbee RF Test Failed");
                } else {
                    Log.i(TAG, "Zigbee RF Test Pass");
                }

            }
        });

        mGateway.zigbeeTestStart(12, 20, 10);
        ```

    === "Kotlin"
        ```kotlin
        mGateway.setZigbeeTestCallbacks( object : ZigbeeTestCallbacks() {
            fun onZigbeeTestResult(receiveCount: Int) {
                var result: Int
                Log.d(GatewayDemoActivity.TAG, "Received packages:$receiveCount")
                //判断规定时间内收到的包个数是否满足测试要求
                if (receiveCount < 12) {
                    Log.i(GatewayDemoActivity.TAG, "Zigbee RF Test Failed")
                } else {
                    Log.i(GatewayDemoActivity.TAG, "Zigbee RF Test Pass")
                }
            }
        })

        mGateway.zigbeeTestStart(12, 20, 10)
        ```