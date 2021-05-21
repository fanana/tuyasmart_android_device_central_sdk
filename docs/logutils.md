# 日志系统

## 创建 LogDeamon 对象

**接口说明**

````java
public LogDaemon(String logDir, int maxfileCount, int fileSizeKB, String prefixName);
````

 **参数说明**

| 参数         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| logDir       | 日志路径，如果路径没有创建，日志系统会创建该目录。           |
| maxfileCount | 备份的日志文件个数，不包括当前写的文件。                     |
| fileSizeKB   | 文件大小，当日志文件达到 fileSizeKB 指定大小时，会备份当前文件，如果备份的文件个数达到 maxfileCount 指定的值，则删除最老的一个文件。 |
| prefixName   | 日志文件名，最终生成日志文件 logDir/prefixName.log，备份文件为logDir/prefixName.log.1, logDir/prefixName.log.2... |

**示例代码**

```java
String filePath = getFilesDir().getAbsolutePath();
String logPath = filePath + File.separator + "log";

//logPath/gateawy.log
mLogDaemon = new LogDaemon(logPath, 10,3*1024, "gateway");
```

## 设置日志保存方式
**接口说明**

```java
public void setExpectation(int pid, String tags, boolean seperate)
```

**参数说明**

| 参数     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| pid      | -1：不按照进程抓取日志；正值：要抓取的进程id。               |
| tags     | 设置要抓取的 tag，多个 tag 用英文逗号分开，例如 “TuyaGatewaySdk,LogDaemon”，抓取Android LOG TAG 为 TuyaGatewaySdk 和 LogDaemon 的日志。 |
| seperate | 如果要通过 pid 和 tag 抓取日志，seperate 指定是否分开抓取，如果分开抓取，pid 一份 log，tag 一份 log；否则抓取 pid 中指定的 tag 日志。只有 pid 和 tag 同时有效时，该参数才有意义。 |

**示例代码**

* 收集指定进程的全部日志

  抓取本进程的所有日志：

  ```java
  mLogDaemon.setExpectation(android.os.Process.myPid(),  null, false);
  mLogDaemon.start();
  ```

* 收集指定进程中符合 tag 的日志

  抓取本进程中 tag 为 TuyaGatewaySdk 和 LogDaemon 的日志：

  ```java
  mLogDaemon.setExpectation(android.os.Process.myPid(),  "TuyaGatewaySdk,LogDaemon", false);
  mLogDaemon.start();
  ```

* 收集指定 tag 的日志

  抓取 tag 为 TuyaGatewaySdk和 LogDaemon 的日志：

  ```java
  mLogDaemon.setExpectation(-1,  "TuyaGatewaySdk,LogDaemon", false);
  mLogDaemon.start();
  ```



* 收集指定 pid 的日志和指定 tag 的日志，输出两份日志

  分别抓取本进程所有日志以及 tag 为 TuyaGatewaySdk 和 LogDaemon 的日志的日志。

  ```java
  mLogDaemon.setExpectation(android.os.Process.myPid(),  "TuyaGatewaySdk,LogDaemon", true);
  mLogDaemon.start();
  ```



## 开始日志保存

**接口说明**

```java
public boolean start();
```

## 停止日志保存
**接口说明**

设备退出或者重启 app 进程时，请务必也退出日志系统，因为日志系统内部起了 logcat 进程。

```java
public void stop();
```

## 获取日志文件

**接口说明**

该接口会将日志目录下的所有日志文件打包为一个 zip 文件。

```java
public String getZippedLogFile()；
```
