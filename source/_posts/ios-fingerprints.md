title: 关于指纹解锁那些事
date: 2015-09-17 14:18:22
tags: ios
---



### 关于指纹解锁那些事

**作者：秋儿**

<br>

iOS8 指纹解锁的API，[这篇文章](http://www.bubuko.com/infodetail-726115.html)解释的非常清楚。  

本文主要针对在实际使用中遇到的问题及解决方法，假定已经了解指纹解锁API，如不了解API,请先移步[指纹解锁的API说明](http://www.bubuko.com/infodetail-726115.html)

项目之前一直使用的是手势密码，近来要增加 iOS8 新出的指纹解锁功能。需求是在设置中添加指纹解锁开关  

**问题1：**在个人设置里面，添加指纹解锁开关项，此项仅在支持TouchID 的设备中出现  
很好，百度了下，得到了如下解决方案  

`解决方案: ` 


    LAContext *context = [LAContext new];
    NSError *error = [NSError new];
    BOOL isAvailable = [context canEvaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&error];


这是api给出的判断TouchID是否可用的方法，isAvailable == Yes 说明 TouchID 可用，反之，则不可用。
但是，当我满心欢喜的使用的时候，问题来了

**问题2：**在6 Plus，未设置手机解锁密码或没有可用的指纹时，用上面的方法判断 isAvailable == No,瞬间心都碎了。
这里依然有解决方案

`解决方案:`

	if (!isAvailable) {
		NSString *str = nil;
        switch (error.code) {
       		case LAErrorTouchIDNotEnrolled://无可用指纹
                
            case LAErrorPasscodeNotSet://设备未开启密码
            {
                isAvailable = YES;
                break;
            }
            case LAErrorTouchIDNotAvailable:
            default:
            {
                isAvailable = NO;
                break;
            }

        }
	}

虽然依然把这个问题解决了。but，又产生了新的问题。

**问题3：**使用上述方法，在 iPod Touch 等不支持 TouchID 的设备，未设置手机解锁密码情况下运行时，设置中的指纹解锁开关项居然出现了。

单步调试之，在 error.code 的 switch 中，进入的是 case LAErrorPasscodeNotSet://设备未开启密码，执行了isAvailable = YES;。然，大胆猜测之，api 居然先判断的是有没有开启密码而不是设备类型和或系统是否支持，这使我彻底无语~~~~

此时，我再也不相信API了，果断自己写判断吧。

`解决方案：`


	// 硬件设备不支持，或系统版本不支持 指纹解锁
    if (![Utils isSystemModelSupportTouchID] || ![Utils isSystemVersionMoreThanVersion:7.0]) {
        return NO;
    }
    
    LAContext *context = [LAContext new];
    NSError *error = [NSError new];
    BOOL isDeviceSupportTouchId = [context canEvaluatePolicy:LAPolicyDeviceOwnerAuthenticationWithBiometrics error:&error];
    
    if (!isDeviceSupportTouchId) {
        
        //不支持指纹识别
        
        switch (error.code) {
                
            case LAErrorTouchIDNotEnrolled:
                
            case LAErrorPasscodeNotSet:
            {
                isDeviceSupportTouchId = YES;
                break;
            }
            case LAErrorTouchIDNotAvailable:
            default:
            {
                isDeviceSupportTouchId = NO;
                break;
            }
        }
    }
    

    return isDeviceSupportTouchId;

硬件设备判断上花了一小点功夫，版本判断显然很简单。

硬件设备判断思路：

1. 获取设备类型字符串，如iPhone 5c,iPhone 6；
2. 判断设备类型字符串是包含iPhone ,iPod , iPad，是iPhone 则截取设备类型字符串中的第一位数字，iPad 有分mini 和Air, 截取设备类型字符串中的第一位数字，然后数字对比判断是否支持TouchID。


至此，TouchID 告一段落！在此，附上本文中的 Demo 地址：[LRFFingerPrintManager](https://github.com/mohuifen/LRFFingerPrintManager),欢迎各位读者朋友提出建议。。




