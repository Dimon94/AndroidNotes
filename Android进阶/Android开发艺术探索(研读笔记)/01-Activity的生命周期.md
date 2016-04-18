# Android开发艺术探索(研读笔记)

>作者：[Dimon](https://dimon94.github.io/)

>- 微博：[@Dimon-喰](http://weibo.com/dscott/profile?rightmod=1&wvr=6&mod=personinfo&is_all=1)
- LOFTER:[@Dimon、](http://dimon.lofter.com/)

## 01-Activity的生命周期
> 生命周期和启动模式以及IntentFilter的匹配规则分析。

Activity的生命周期分为两个部分

1. 典型情况下的生命周期
2. 异常情况下的生命周期

### 典型情况下的生命周期分析

-  **onCreate** :表示`Activity`正在被创建。在这里可以做一些初始化的工作。
-  **onRestart** :表示`Activity`正在重新启动。当当前`Activity`从不可见重新变成可见状态。
-  **onStart** :表示`Activity`正在被启动。已经可见，但不在前台，无法交互。
-  **onResume** :表示`Activity`已经可见，并且出现在前台可以交互。
-  **onPause** :表示`Activity`正在停止。在这里可以做一些储存数据，停止动画等工作，**但不能太耗时**，因为必须`onPause`执行完成之后新的`Activity`才能`Resume`。
-  **onStop** :表示`Activity`即将停止。可以进行一些稍微重量级的回收工作，不能太耗时。
-  **onDestroy** :表示`Activity`即将被销毁。可以进行一些回收工作和最终的资源释放。


![Activity的生命周期](http://img.my.csdn.net/uploads/201007/28/0_12803210018q71.gif)

**注意：**
- **onStart和onStop是从Activity是否可见这个角度来回调的**
- **onResum和onPause是从Activity是否在前台这个角度来回调的**

---

### 异常情况下的生命周期分析

#### 情况 1：资源相关的系统配置发生改变导致Activity被杀死并重新创建

> 比如说横屏手机和竖屏手机会拿到两张不同的图片（设定了landscape或者portrait状态下的图片）。本来手机在竖屏状态，突然旋转屏幕，由于系统配置发生了变化，在默认情况下，Activity会被销毁并且重新创建，当然我们也可以阻止系统重新创建我们的Activity。

当系统配置发生改变后，Activity会调用 onPause -> onStop -> onDestroy。

由于是异常情况终止，系统会在`onStop`之前调用`onSaveInstanceState`来保存当前`Activity`的状态。(与`onPause`没有时序关系)

当`Activity`被系统重新创建后，系统会调用`onRestoreInstanceState`，把之前`onSaveInstanceState`方法所保存的**`Bundle`对象**作为参数同时传给`onRestoreInstanceState`和`onCreate`方法。(从时序来说，`onRestoreInstanceState`的调用时机在`onStart`之后)

![异常情况下Activity的重建过程](http://7xs99u.com1.z0.glb.clouddn.com/image/jpg/%E5%BC%82%E5%B8%B8%E6%83%85%E5%86%B5%E4%B8%8BActivity%E7%9A%84%E9%87%8D%E5%BB%BA%E8%BF%87%E7%A8%8B.png)

而在视图方面，当Activity在异常情况下需要重新创建时，系统会默认为我们保存当前Activity的视图结构，并且在Activity重启后为我们恢复这些数据。

其实每个View都有`onSaveInstanceState`和`onRestoreInstanceState`,关于保存和恢复View层级结构，系统的工作流程如下：

![关于保存与恢复View层级结构](http://7xs99u.com1.z0.glb.clouddn.com/image/jpg/%E5%85%B3%E4%BA%8E%E4%BF%9D%E5%AD%98%E5%92%8C%E6%81%A2%E5%A4%8DView%E5%B1%82%E7%BA%A7%E7%BB%93%E6%9E%84.png)

> onSaveInstanceState方法，系统只会在Activity即将被销毁**并且有机会重新显示**的情况下才会去调用它。

#### 情况 2：资源内存不足导致低优先级的Activity被杀死

其实这种情况的数据存储与恢复过程与情况 1完全一致。

Activity的优先级情况：

- 前台的`Activity` —— 正在和用户交互的`Activity`，优先级最高
- 可见但非前台的`Activity` —— 比如`Activity`中弹出了一个对话框，导致`Activity`可见但是位于后台，无法和用户进行直接交互
- 后台的`Activity` —— 已经被暂停的`Activity`，比如执行了`onStop`，优先级最低

当系统内存不足时，系统就会按照上述优先级去杀死目标`Activity`所在的进程，并在后续通过`onSaveInstanceState`和`onRestoreInstanceState`来存储和恢复数据。而将后台工作放入`Service`中是一个比较好的方法。

#### 当系统配置改变后，Activity如何不被重新创建

由于系统配置中有很多内容，如果当某项内容发生改变后，不想系统重新创建`Activity`，**可以给`Activity`指定`configChanges`属性**。

```
android:configChanges="orientation|keyboardHidden"
```

| 项目  | 含义  |
| :------------ | :------------ |
| mcc  | SIM卡唯一标识IMSI(国际移动用户识别码)中的国家代码，由3位数组成，中国为460.此项 标识mcc代码发生了改变  |
| mnc  | SIM卡唯一标识IMSI(国际移动用户识别码)中的运营商代码，由两位数字组成，中国移动TD系统为00，中国联通为01，中国电信为03。此项标识mnc发生改变  |
| **locale**  | 设备的本地位置发生了改变们一般指切换了系统语言  |
| touchscreen  | 触摸屏发生了改变，正常情况下无法发生，可以忽略它  |
| keyboard  | 键盘类型发生了改变，比如用户使用了外插键盘  |
| **keyboardHidden**  | 键盘的可访问性发生了改变，比如用户调出了键盘  |
| navigation  | 系统导航方式发生了改变，比如采用了轨迹球导航，很难发生，可以忽略  |
| screenLayout  | 屏幕布局发生了改变，很可能是用户激活了另一个显示设备  |
| fontScale  | 系统字体缩放比如发生了改变，比如用户选择了一个新字号  |
| uiMode  | 用户界面模式发生了改变，比如是否开启了夜间模式（API8新添加）  |
| **orientation**  | 屏幕方向发生了改变，这个是最常用的，比如旋转了手机屏幕  |
| screenSize  | 当屏幕的尺寸信息发生了改变，当旋转设备屏幕时，屏幕尺寸会发生变化，这个选项比较特殊，它和编译选项有关，当编译选项中的minSdkVersion和targetSdkVersion 均低于13时，此选项不会导致Activity重启，否则会导致Activity重启（API13新添加）  |
| smallestScreenSize  | 设备的物理屏幕尺寸发生了改变，这个项目和屏幕的方向没有关系，仅仅表示在实际的物理屏幕的尺寸改变的时候发生，比如用户切换到了外部的显示设备，这个选项和screenSize一样，当编译选项中的minSdkVersion和targetSdkVersion均低于13时，此选项不会导致Activity重启，否则会导致Activity重启（API13新添加）  |
| layoutDirection  | 当布局方向发生变化，这个属性用的比较少，正常情况下无须修改布局的layoutDirection属性（API17新添加）  |

如果我们没有在`Activity`的`configChanges`属性中指定该选项的话，当配置发生改变后就会导致`Activity`重新创建。

最常用的只有`locale`、`orientation`和`keyboardHidden`。

需要修改的代码很简单，只需要在AndroidMenifest.xml中加入Activity的声明即可：

```xml
<activity
    android:name="com.dimon.MainActivity"
    android:configChanges="orientation|screenSize"
    android:label="@string/app_name">
    <intent-filter>
      <action android:name="android.intent.action.MAIN"/>
      <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
</activity>
```
```java
@Override
public void onConfigurationChanged(Configuration newConfig){
  super.onConfigurationChanged(newConfig);
  Log.d(TAG,"onConfigurationChanged,newOrientation:" + newConfig.orientation);
}
```

`Activity`没有重新创建，并且没有调用`onSaveInstanceState`和`onRestoreInstanceState`来存储和恢复数据，而是系统调用了`Activity`的`onConfigurationChanged`方法，这个时候我们可以加入一些自己的特殊处理了。

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!
