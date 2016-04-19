title: monkey 测试命令，保存到SD，遇到崩溃继续
date: 2015-09-19 19:16:05
categories: android
keywords: android,monkey,adb
tags: android
---


### 开始测试：
```
monkey -p com.itings.myradio -c android.intent.category.LAUNCHER -s 500 --hprof --ignore-crashes --ignore-timeouts --ignore-security-exceptions --monitor-native-crashes --throttle 50 -v -v 600000>/mnt/sdcard/monkey1.txt & 
```

### 保存monkey 和 logcat 的日志
```
monkey -p com.itings.myradio -c android.intent.category.LAUNCHER -s 500 --hprof --ignore-crashes --ignore-timeouts --ignore-security-exceptions --monitor-native-crashes --throttle 50 -v -v -v 15000000>>/mnt/sdcard/monkey_kaola.txt & logcat  -v time >>/mnt/sdcard/logcat.txt
```

### 终止：

```
adb shell
ps|grep monkey
kill id
```

###### adb logcat -s com.xx.xx