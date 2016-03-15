#广播
* 广播的概念
	* 现实：电台通过发送广播发布消息，买个收音机，就能收听
	* Android：系统在产生某个事件时发送广播，应用程序使用广播接收者接收这个广播，就知道系统产生了什么事件。
Android系统在运行的过程中，会产生很多事件，比如开机、电量改变、收发短信、拨打电话、屏幕解锁

---
#IP拨号器
> 原理：接收拨打电话的广播，修改广播内携带的电话号码
* 定义广播接收者接收打电话广播

	public class CallReceiver extends BroadcastReceiver {

		//当广播接收者接收到广播时，此方法会调用
		@Override
		public void onReceive(Context context, Intent intent) {
			//拿到用户拨打的号码
			String number = getResultData();
			//修改广播内的号码
			setResultData("17951" + number);
		}
	}
* 在清单文件中定义该广播接收者接收的广播类型

		<receiver android:name="com.itheima.ipdialer.CallReceiver">
            <intent-filter >
                <action android:name="android.intent.action.NEW_OUTGOING_CALL"/>
            </intent-filter>
        </receiver>
* 接收打电话广播需要权限

		<uses-permission android:name="android.permission.PROCESS_OUTGOING_CALLS"/>
* 即使广播接收者的进程没有启动，当系统发送的广播可以被该接收者接收时，系统会自动启动该接收者所在的进程

---
#短信拦截器
>系统收到短信时会产生一条广播，广播中包含了短信的号码和内容

* 定义广播接收者接收短信广播

```java
		public void onReceive(Context context, Intent intent) {
		//拿到广播里携带的短信内容
		Bundle bundle = intent.getExtras();
		Object[] objects = (Object[]) bundle.get("pdus");
		for(Object ob : objects ){
			//通过object对象创建一个短信对象
			SmsMessage sms = SmsMessage.createFromPdu((byte[])ob);
			System.out.println(sms.getMessageBody());
			System.out.println(sms.getOriginatingAddress());
		}
	}
```
* 系统创建广播时，把短信存放到一个数组，然后把数据以pdus为key存入bundle，再把bundle存入intent
* 清单文件中配置广播接收者接收的广播类型，注意要设置优先级属性，要保证优先级高于短信应用，才可以实现拦截

```xml
		<receiver android:name="com.itheima.smslistener.SmsReceiver">
            <intent-filter android:priority="1000">
                <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
            </intent-filter>
        </receiver>
```
* 添加权限

```xml
		<uses-permission android:name="android.permission.RECEIVE_SMS"/>
```
* 4.0以后广播接收者安装以后必须手动启动一次，否则不生效
* 4.0以后广播接收者如果被手动关闭，就不会再启动了

---
#监听SD卡状态
* 清单文件中定义广播接收者接收的类型，监听SD卡常见的三种状态，所以广播接收者需要接收三种广播

```xml
		 <receiver android:name="com.itheima.sdcradlistener.SDCardReceiver">
            <intent-filter >
                <action android:name="android.intent.action.MEDIA_MOUNTED"/>
                <action android:name="android.intent.action.MEDIA_UNMOUNTED"/>
                <action android:name="android.intent.action.MEDIA_REMOVED"/>
                <data android:scheme="file"/>
            </intent-filter>
        </receiver>
 ```
* 广播接收者的定义

```java
		public class SDCardReceiver extends BroadcastReceiver {
			@Override
			public void onReceive(Context context, Intent intent) {
				// 区分接收到的是哪个广播
				String action = intent.getAction();

				if(action.equals("android.intent.action.MEDIA_MOUNTED")){
					System.out.println("sd卡就绪");
				}
				else if(action.equals("android.intent.action.MEDIA_UNMOUNTED")){
					System.out.println("sd卡被移除");
				}
				else if(action.equals("android.intent.action.MEDIA_REMOVED")){
					System.out.println("sd卡被拔出");
				}
			}
		}
```
---
#勒索软件
* 接收开机广播，在广播接收者中启动勒索的Activity
* 清单文件中配置接收开机广播

```xml
		<receiver android:name="com.itheima.lesuo.BootReceiver">
            <intent-filter >
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
        </receiver>
```
* 权限

```xml
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
* 定义广播接收者

```java
		@Override
		public void onReceive(Context context, Intent intent) {
			//开机的时候就启动勒索软件
			Intent it = new Intent(context, MainActivity.class);
			context.startActivity(it);
		}
```
* 以上代码还不能启动MainActivity，因为广播接收者的启动，并不会创建任务栈，那么没有任务栈，就无法启动activity
* 手动设置创建新任务栈的flag

```java
		it.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
```
---
#监听应用的安装、卸载、更新
> 原理：应用在安装卸载更新时，系统会发送广播，广播里会携带应用的包名
* 清单文件定义广播接收者接收的类型，因为要监听应用的三个动作，所以需要接收三种广播

```xml
		<receiver android:name="com.itheima.app.AppReceiver">
            <intent-filter >
                <action android:name="android.intent.action.PACKAGE_ADDED"/>
                <action android:name="android.intent.action.PACKAGE_REPLACED"/>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package"/>
            </intent-filter>
        </receiver>
 ```
* 广播接收者的定义

```java
		public void onReceive(Context context, Intent intent) {
			//区分接收到的是哪种广播
			String action = intent.getAction();
			//获取广播中包含的应用包名
			Uri uri = intent.getData();
			if(action.equals("android.intent.action.PACKAGE_ADDED")){
				System.out.println(uri + "被安装了");
			}
			else if(action.equals("android.intent.action.PACKAGE_REPLACED")){
				System.out.println(uri + "被更新了");
			}
			else if(action.equals("android.intent.action.PACKAGE_REMOVED")){
				System.out.println(uri + "被卸载了");
			}
		}
```
---
#广播的两种类型
* 无序广播：所有跟广播的intent匹配的广播接收者都可以收到该广播，并且是没有先后顺序（同时收到）
* 有序广播：所有跟广播的intent匹配的广播接收者都可以收到该广播，但是会按照广播接收者的优先级来决定接收的先后顺序
	* 优先级的定义：-1000~1000
	* 最终接收者：所有广播接收者都接收到广播之后，它才接收，并且一定会接收
	* abortBroadCast：阻止其他接收者接收这条广播，类似拦截，只有有序广播可以被拦截

---
#Service
* 就是默默运行在后台的组件，可以理解为是没有前台的activity，适合用来运行不需要前台界面的代码
* 服务可以被手动关闭，不会重启，但是如果被自动关闭，内存充足就会重启
* startService启动服务的生命周期
	* onCreate-onStartCommand-onDestroy
* 重复的调用startService会导致onStartCommand被重复调用

---
# 进程优先级
1. 前台进程：拥有前台activity（onResume方法被调用）
2. 可见进程：拥有可见activity（onPause方法被调用）
3. 服务进程：不到万不得已不会被回收，而且即便被回收，内存充足时也会被重启
4. 后台进程：拥有后台activity（activity的onStop方法被调用了），很容易被回收
5. 空进程：没有运行任何activity，很容易被回收

---
#电话窃听器
* 电话状态：空闲、响铃、接听
* 获取电话管理器，设置侦听

```java
		TelephonyManager tm = (TelephonyManager) getSystemService(TELEPHONY_SERVICE);
		tm.listen(new MyPhoneStateListener(), PhoneStateListener.LISTEN_CALL_STATE);//侦听电话状态改变
* 侦听对象的实现

		class MyPhoneStateListener extends PhoneStateListener{

			//当电话状态改变时，此方法调用
			@Override
			public void onCallStateCcchanged(int state, String incomingNumber) {
				// TODO Auto-generated method stub
				super.onCallStateChanged(state, incomingNumber);
				switch (state) {
				case TelephonyManager.CALL_STATE_IDLE://空闲
					if(recorder != null){
						recorder.stop();
						recorder.release();
						recorder  = null;
					}
					break;
				case TelephonyManager.CALL_STATE_OFFHOOK://摘机
					if(recorder != null){
						recorder.start();
					}
					break;
				case TelephonyManager.CALL_STATE_RINGING://响铃
					recorder = new MediaRecorder();
					//设置声音来源
					recorder.setAudioSource(MediaRecorder.AudioSource.MIC);
					//设置音频文件格式
					recorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
					recorder.setOutputFile("sdcard/haha.3gp");
					//设置音频文件编码
					recorder.setAudioEncoder(MediaRecorder.AudioEncoder.AMR_NB);
					try {
						recorder.prepare();
					} catch (IllegalStateException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					} catch (IOException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
					break;
				}
			}
		}
```
---
- 邮箱 ：elisabethzhen@163.com
- Good Luck!