title: 业务开发中自定义 EditText 光标不显示？
date: 2019-04-14 11:28:03
categories:
keywords: 光标,光标不显示，Edittext,
tags:
---


这里记录一个业务开发遇到的问题。最近996赶项目，UI 妹子说你这个输入框的光标颜色是红色的，不好看，我要换成橘黄的。好吧，那就修改下。
直接使用这个代码：

```
        <EditText
                    android:id="@+id/et_forget_password_verify_code"
                    style="@style/EditTextNoPaddingStyle"
                    android:inputType="textVisiblePassword"
                    android:hint="@string/register_verify_code_hint"
                    android:maxLength="6"/>

```

style.xml

```
    <style name="EditTextNoPaddingStyle" parent="LayoutMW">
        ...
        <item name="android:textCursorDrawable">@color/color_edit_text_cursor</item>
        ...
    </style>

```


结果在三星手机显示正常，但是测试同学说在 小米和华为手机，不显示光标，只有输入一个后，才会显示，但是光标也不闪动。

最后排查了下，具体原因是：textCursorDrawable 后面一定要跟 drawable ，否则某些手机厂商定制系统兼容不好，就显示不出来，或者不闪动。

解决办法是：
添加 edit_text_cursor.xml

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="rectangle">

    <size android:width="@dimen/dp_05"/>

    <span style="font-family: Arial, Helvetica, sans-serif;"><!-- 光标宽度可以自己定义 --></span>

    <solid android:color="@color/color_edit_text_cursor"/><!-- 光标颜色可以自己定义 -->
</shape>
```



style.xml

```
    <style name="EditTextNoPaddingStyle" parent="LayoutMW">
        ...
        <item name="android:textCursorDrawable">@drawable/edit_text_cursor</item>
        ...
    </style>

```

这样就显示正常了。

同此的理，很多我们在自己的手机或者测试机显示正常，但是不代表在所有手机显示正常，所有要多兼容，选择最安全的方式。

