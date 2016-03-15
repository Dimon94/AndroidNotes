Fragment中获取Context对象的最佳方案
===

###Context类型

>引用郭神的一段话："Android程序不像Java程序一样，随便创建一个类，写个main()方法就能跑了，而是要有一个完整的Android工程环境，在这个环境下，我们有像Activity、Service、BroadcastReceiver等系统组件，而这些组件并不是像一个普通的Java对象new一下就能创建实例的了，而是要有它们各自的上下文环境，也就是我们这里讨论的Context。可以这样讲，Context是维持Android程序中各组件能够正常工作的一个核心功能类。"

- Context一共有三种类型，分别是Application、Activity和Service。

> 有几种场景比较特殊，比如启动Activity，还有弹出Dialog。出于安全原因的考虑，Android是不允许Activity或Dialog凭空出现的，一个Activity的启动必须要建立在另一个Activity的基础之上，也就是以此形成的返回栈。而Dialog则必须在一个Activity上面弹出（除非是System Alert类型的Dialog），因此在这种场景下，我们只能使用Activity类型的Context，否则将会出错。

-getApplication()方法的语义性非常强，一看就知道是用来获取Application实例的，但是这个方法只有在Activity和Service中才能调用的到。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法了，如下所示：

```java
public class MyReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        MyApplication myApp = (MyApplication) context.getApplicationContext();
        Log.d("TAG", "myApp is " + myApp);
    }

}
```
- `MyApplication`在构造方法中调用Context的方法就会崩溃，在onCreate()方法中调用Context的方法就一切正常

```java
//正确使用方法
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        String packageName = getPackageName();
        Log.d("TAG", "package name is " + packageName);
    }

}
```
- 其实这里我们只需谨记一点，Application全局只有一个，它本身就已经是单例了，无需再用单例模式去为它做多重实例保护了，代码如下所示：

```java
public class MyApplication extends Application {

    private static MyApplication app;

    public static MyApplication getInstance() {
        return app;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        app = this;
    }

}
```
###Fragment中获取Context对象的最佳方案
1.MyApplication是一定要写的
2.写一个如下的BaseFragment类：所有的Fragment都继承这个BaseFragment,直接通过getContext()方法即可得到Context对象，当然实例化Dialog等需要依附于Activity的对象时，还是老老实实的getActivity()吧

```java
public class BaseFragment extends Fragment
{
    private Activity activity;

    public Context getContext(){
        if(activity == null){
            return MyApplication.getInstance();
        }
        return activity;
    }

    @Override
    public void onAttach(Activity activity) {
        super.onAttach(activity);
        activity = getActivity();
    }
}
```
3.运用getContext()获取Context对象

```java
public class FriendsFragment extends BaseFragment {

    private Context mContext;

    public FriendsFragment() {
        // Required empty public constructor
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mContext = this.getContext() ;

    }
  ......
}
```
---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!




