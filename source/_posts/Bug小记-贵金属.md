title: 'Bug小记(**贵金属)'
date: 2015-11-20 18:24:09
categories:
keywords:
tags: ios
---
# Bug 小记（\*\*贵金属）

**作者：球儿**


最近在修复 APP 的 Bug，遇到了几个因对 SDK 不熟 造成的 Bug。如下：
####Bug1：点击获取验证码后，没有进行倒计时，且不能再次点击


使用 GCD 写的倒计时，源代码：
 
    _isCountDown = YES;
    __block int timeout=kCountdownTime; //倒计时时间
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_source_t _timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0,queue);
    dispatch_source_set_timer(_timer,dispatch_walltime(NULL, 0),1.0*NSEC_PER_SEC, 0); //每秒执行
    dispatch_source_set_event_handler(_timer, ^{
        if(timeout<=0){ //倒计时结束，关闭
            dispatch_source_cancel(_timer);
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.getSmsCodeBtn setTitle:@"重新获取" forState:UIControlStateNormal];
                [self.getSmsCodeBtn setTitle:@"重新获取" forState:UIControlStateDisabled];

                [self.getSmsCodeBtn setTitleColor:ColorWithHexString(GJS_COLOR_LOGINBTN_AVAILABLE_NORMAL) forState:UIControlStateNormal];
                
                [self.getSmsCodeBtn setEnabled:YES];
                _isCountDown = NO;
            });
        }else{
            NSString *strTime = [NSString stringWithFormat:@"%ds后重发",timeout];
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.getSmsCodeBtn setTitle:strTime forState:UIControlStateNormal];
                [self.getSmsCodeBtn setTitle:strTime forState:UIControlStateDisabled];

                [self.getSmsCodeBtn setTitleColor:ColorWithHexString(GJS_COLOR_GETCODEBTN_UNAVAILABLE) forState:UIControlStateNormal];
            });
            timeout--;
            
        }
    });
    dispatch_resume(_timer);



如上所示的源码，在 iOS7 上倒计时按钮上的文字不会变化，在 iOS 8,iOS9 上都是没问题的，我也郁闷了很久。各种百度未果后转向 Google,也有人遇到这样的问题，但是只搜索到一篇真正能解决这个问题的文章 [http://blog.csdn.net/zhangyanshen/article/details/46910515][1]



<font color=green>**解决方案：**</font>

`[sendAuthCodeBtn setTitle:@"发送验证码" forState:UIControlStateDisabled];` 


关键在于这行代码。设置了禁用状态下的文字。顺利解决了 这个 Bug。

</br>
#### Bug1：倒计时 UIButton 上的文字变更会有闪烁效果
</br>
UIButton 设置 title时会闪烁。

<font color=brown>**原因：**</font>UIButton 的 buttonType 是 System 类型时会出现该种问题

<font color=green>**解决方案：**</font>UIButton 的 buttonType 设置为 Custom 类型时不会出现闪烁。

</br>
#### Bug2：在工程中添加plist 文件，用代码对其进行写入操作，在模拟器中按代码执行，但在真机上plist中内容未改变
</br>
plist 文件中是一个数组，元素是多个字典，在模拟器上运行一切正常，但测试人员用真机测试时发现问题，无法写入到 plist 文件中。

<font color=brown>**原因：**</font> 打包在 ipa 的文件是无法更改的。一句话：无权限修改（知道真相的我眼泪掉下来~)，只可进行读取操作。

<font color=green>**解决方案：**</font>在 app 启动的时候判断是否在 Document 文件夹下存在相同的 plsit 文件。 不存在，获取沙盒下 plist 文件中的内容，并写入Document 文件夹下的 plsit 文件。存在则不做任何处理。（之所以选择这种方式而不选择直接将内容用代码写入 Document 文件夹下来解决这个问题，是因为个人认为在开发时方便对工程中plsit 文件内容的更改）


[1]:	http://blog.csdn.net/zhangyanshen/article/details/46910515 "http://blog.csdn.net/zhangyanshen/article/details/46910515"