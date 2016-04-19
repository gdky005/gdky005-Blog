title: Android录音使用 byte 类型获取分贝或声音振幅
date: 2015-10-08 18:10:20
categories:
keywords: 获取分贝值
tags: android录音
---

以下是获取声音振幅的代码：


	try {
            while (isRecording) {
                read = audioRecord.read(data, 0, recBufSize);
	//                L.i(context, "开始获取音频TTT：" + data.length);
                if (AudioRecord.ERROR_INVALID_OPERATION != read && retry <= 3) {
	//                    L.i(context, "发出的音频TTT：" + data.length);
                    //录音成功，重置录音失败的次数
                    retry = 0;
                    int up = kaoLaRecordCore.upload(data, data.length); //TODO  算长度

                    long v = 0;
                    long tv = 0;
                    // 将 data 内容取出，进行平方和运算
                    for (int i = 0; i < data.length; i+=2) {
                        tv = data[i+1] * 128 + data[i];
                        tv *= tv;
                        v += tv;
                    }
                    // 平方和除以数据总长度，得到音量大小。
                    double mean = v / (double) read;
                    double volume = 10 * Math.log10(mean * 2);
                    KL.d(AudioThread.class, "分贝值:" + volume);

                    EventBus.getDefault().post((int)volume, ChatManager.TAG_VOICE_DB);

                    KL.d(AudioThread.class, "分贝值: {}，v = {}， read  = {}， mean = {}  ", volume, v,
                            read, mean);

	//                    L.i(context, "上传录音状态TTT：" + up);
	//                    if (isTest) {
	//                        try {
	//                            os.write(data);
	//                        } catch (Exception e) {
	//                            e.printStackTrace();
	//                        }
	//                    }
                } else {
                    L.i(AudioThread.class, "TTT录音权限可能有问题，暂时不能录音: read={}, retry:{}", read, retry);
                    if (retry <= 3) {
                        retry++;
                    } else {
                        isRecording = false;
                        EventBus.getDefault().post(context.getString(R.string.podcast_record_permission), TAG_MIC_FORBID_STATE);
                        break;
                    }

                }
	//                L.i(context, "上次音频TTT：" + data.length);

                Thread.sleep(10);

                if (isRecording())
                    pauseThread();
            }

	//            if (isTest) {
	//                try {
	//                    os.close();
	//                } catch (IOException e) {
	//                    e.printStackTrace();
	//                }
	//            }

        } catch (Exception e) {
            e.printStackTrace();
            L.i(AudioThread.class, "上传出现异常");

        }
        
        
效果是：

```
当前接受到的分贝值: %s，v =15
当前接受到的分贝值: %s，v =41
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =42
当前接受到的分贝值: %s，v =43
当前接受到的分贝值: %s，v =44
当前接受到的分贝值: %s，v =48
当前接受到的分贝值: %s，v =47
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =47
当前接受到的分贝值: %s，v =50
当前接受到的分贝值: %s，v =50
当前接受到的分贝值: %s，v =49
当前接受到的分贝值: %s，v =48
当前接受到的分贝值: %s，v =50
当前接受到的分贝值: %s，v =50
当前接受到的分贝值: %s，v =48
当前接受到的分贝值: %s，v =47
当前接受到的分贝值: %s，v =48
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =44
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =46
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =44
当前接受到的分贝值: %s，v =44
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =44
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =45
当前接受到的分贝值: %s，v =64
当前接受到的分贝值: %s，v =65
当前接受到的分贝值: %s，v =65
当前接受到的分贝值: %s，v =57
当前接受到的分贝值: %s，v =60
当前接受到的分贝值: %s，v =58
当前接受到的分贝值: %s，v =55
当前接受到的分贝值: %s，v =55
```
声音在0-100以内，基本声音维持在 40-60之间