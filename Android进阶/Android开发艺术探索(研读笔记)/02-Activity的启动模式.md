# Android开发艺术探索(研读笔记)

>作者：[Dimon](https://dimon94.github.io/)

>- 微博：[@Dimon-喰](http://weibo.com/dscott/profile?rightmod=1&wvr=6&mod=personinfo&is_all=1)
- GitHub：[@Dimon94](https://github.com/Dimon94)
- LOFTER：[@Dimon、](http://dimon.lofter.com/)

## 02-Activity的启动模式
### Activity 的 LaunchMode
> 复习一点：启动`Activity`时，系统会创造实例并把他们放入任务栈里，而任务栈是一种“后进先出”的栈结构。

`Activity`的四种启动模式：

- **standard**：标准模式、默认模式。每次启动一个`Activity`都会重新创建一个新的实例，不管这个实例是否已经存在。在这种模式下，某个`Activity`启动了一号`Activity`，那么一号`Activity`就运行在启动它的那个`Activity`所在的栈中。

- **singleTop**：栈顶复用模式。如果新的`Activity`已经位于任务栈的栈顶，那么此`Activity`就不会被重新创建，同时它的`onNewIntent`方法会被回调，并且可以根据此方法的参数获得当前请求的信息。

- **singleTask**：栈内复用模式。在这种单实例模式下，只要`Activity`在一个栈中存在，那么多次启动此`Activity`都不会重新创建实例，系统也会调用其`onNewIntent`。

![singleTask 的工作流程](http://7xs99u.com1.z0.glb.clouddn.com/image/png/singleTask%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png)

- **singleInstance**：单实例模式。这是一种加强的singleTask模式，除了具有singleTask模式的所有特性外，还加强了一点，那就是**具体此种模式的`Activity`只能单独地位于一个任务栈中。**

>注：在任何跳转的时候，首先调用本`Activity`的`onPause`，然后跳转。如果被跳转的`activity`由于启动方式而没创建新的实例，则会先调用`onNewIntent`，然后按照正常的生命周期调用。

如：

1. A→B，A：onPause；B：onCreate，onStart，onResume。
2. A(singleTop)→A，A：onPause；A：onSaveInstanceState；A：onResume。

### 一些具体问题与情况

- 一：

>首先要说明：任务栈分为前台任务栈和后台任务栈，后台任务栈中的`Activity`位于暂停状态。

当在前台任务栈AB启动Activity D，而D正好是后台任务栈CD的栈顶。现在假设后台任务栈里的Activity的启动模式是`singleTask`，请求启动D，那么整个后台任务栈都会被切换到前台，直接占据前台任务栈的栈顶，即ABCD。如果之前请求启动的是C，那么就会变成ABC。

**`singleTask`模式的`Activity`切换到栈顶会导致在它之上的栈内的`Activity`出栈。**

- 二：

`TaskAffinity`：任务相关性。标识一个`Activity`所需要的任务栈的名字。

```
adnroid:taskAffinity="com.dimon.task1"
```
默认情况下`Activity`所需要的任务栈的名字为应用的包名。

![TaskAddinity](http://7xs99u.com1.z0.glb.clouddn.com/image/png/TaskAffinity%E5%B1%9E%E6%80%A7.png)

### 如何给Activity指定启动模式呢？

#### 第一种方法：通过`AndroidMenifest`为`Activity`指定启动模式。

```xml
<activity
    android:name="com.dimon.SecondActivity"
    android:configChanges="screenLayout"
    adnroid:taskAffinity="com.dimon.task1"
    android:launchMode="singleTask"
    android:label="@string/app_name"/>
```

#### 第二种方法：通过在`Intent`中设置标志位来为`Activity`指定启动模式。

```java
Intent intent = new Intent();
intent.setClass(MainActivity.this, SecondActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent);
```
#### 区别

- 优先级：第二种方法比第一种优先级高，两种都存在时，以第二种为准。
- 限定范围：第一种无法设置`FLAG_ACTIVITY_NEW_TASK`标识，而第二种无法指定`singleTask`模式。

### Acticity 中的一些比较常用的 Flags

- **`FLAG_ACTIVITY_NEW_TASK`**

这个标记位的作用是为`Activity`指定“`singleTask`”启动模式，其效果和在XML中指定该启动模式相同。

- **`FLAG_ACTIVITY_SINGLE_TOP`**

这个标记位的作用是为`Activity`指定“`singleTop`”启动模式，其效果和在XML中指定该启动模式相同。

- **`FLAG_ACTIVITY_CLEAR_TOP`**

具有此标记位的`Activity`，当它启动时，在同一个任务栈中所有位于他上面的`Activity`都要出栈。`singleTask`默认就具有这个标记位的效果。如果被启动的`Activity`采用了`standard`启动模式，那么它连通它之上的`Activity`都要出栈，系统会创建新的`Activity`实例并放入栈顶。

- **`FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS`**

具有这个标记的`Activity`不会出现在历史`Activity`的列表中，当某种情况下我们不希望用户通过历史列表回到我们的`Activity`的时候这个标记比较有用。它等同于在XML中指定Activity的属性`android:excludeFromRecents="true"`。


### IntentFilter的匹配规则

#### action的匹配规则

`action`的匹配要求`Intent`中的`action`存在且必须和过滤规则中的一个`action`相同。区分大小写。

#### categoryd的匹配规则

如果`Intent`中含有`category`，那么所以的`category`都必须和过滤规则中的其中一个`category`相同。

如果`Intent`中不含有`category`，那么也能匹配，因为系统在调用`startActivity`或者`startActivityForResult`的时候会默认为`Intent`加上“`android.intent.category.DEFAULT`”这个`category`，所以为了我们的`Activity`能够接收隐式调用，就必须在`intent-filter`中指定“`android.intent.category.DEFAULT`”。

#### data的匹配规则

规则与`action`相似。

```xml
<intent-filter>
  <data android:mimeType="image/*"/>
  ...
</intent-filter>
```
以上规则说明`Intent`中的`mimeType`属性必须为“`image/*`”才能匹配。虽然没有定义`URI`，但是还是得有默认值，`URI`的默认值为`content`和`file`。也就是说没有`URI`也得在`Intent`中的`URI`部分的`schema`必须为`content`或者`file`才能匹配。

```java
intent.setDataAndType(uri.parse("file://abc"),"image/png");
```

如果要为Intent指定完整的data，必须调用setDataAndType方法，不能先调用setData再调用setType。因为这两个方法会彼此清除对方的值。源码如下：

```java
public Intent setData(Uri data){
  mData = data;
  mType = null;
  return this;
}

```

而`data`的结构略微复杂，语法如下：

```xml
<data android:scheme="string"
  android:host="string"
  android:port="string"
  android:path="string"
  android:pathPattern="string"
  android:pathPrefix="string"
  android:mimeType="string"/>
```
`data`由两部分组成，`mimeType`和`URI`。

`mimeType`指媒体类型，例如image/jpeg、audio/mpeg4-generic和video/*等。

`URI`的结构如下：

```
<scheme>://<host>:<port>/[<path>|<pathPrefix>|<pathPattern>]
```

- **Scheme**：URI的模式，比如http、file、content等。
- **Host**：URI的主机名，比如www.baidu.com
- **Port**：URI的端口号。
- **Path、pathPattern和pathPrefix**：这三个表示路径信息，path完整路径信息；pathPattern也表示完整的路径信息，但是它里面可以包括通配符“*”，注意正则表达式；pathPrefix表示路径的前缀信息。

---

#### IntentFilter整合例子

```xml
<intent-filter>
  <action android:name="com.dimon.1"/>
  <action android:name="com.dimon.2"/>
  <category android:name="com.dimon.1a"/>
  <category android:name="com.dimon.2b"/>
  <category android:name="android.intent.category.DEFAULF"/>\
  <data android:mimeType="text/plain"/>
</intent-filter>

```

```java
Intent intent = new Intent("com.dimon.1");
intent.addCategory("com.dimon.2b");
intent.setDataAndType(Uri.parse("file://abc"),"text/plain");
startActivity(intent);
```

#### Some Tips
使用隐式方式启动`Activity`时，最好做一下判断，看看是否有`Activity`能够匹配我们的隐式`Intent`。

方法有两个：采用`PackageManager`的`resolveActivity`方法或者`Intent`的`resolveActivity`方法，如果他们匹配不了就返回`null`。

另外`PackageManager`还提供了`queryIntentActivities`方法：它不是返回最佳匹配的`Activity`信息而是返回所有成功匹配的`Activity`信息。

```java
public abstract List<ResolveInfo> queryIntentActivities(Intent intent, int flags);
public abstract ResolveInfo resolveActivity(Intent intent, int flags);
```
第二个参数我们使用`MATCH_DEFAULT_ONLY`这个标记位，这个标记位仅仅匹配那些在`intent-filter`中声明了`<category android:name="android.intent.category.DEFAULT"/>`这个`category`的`Activity`。
只要上述两个方法不返回`null`，那么`startActivity`一定可以成功。因为不含有`DEFAULT`这个`category`的`Activity`是无法接收隐式`Intent`的。



---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!
