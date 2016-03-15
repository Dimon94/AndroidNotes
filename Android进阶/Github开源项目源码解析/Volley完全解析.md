# Volley简介
>Volley可是说是把AsyncHttpClient和Universal-Image-Loader的优点集于了一身，既可以像AsyncHttpClient一样非常简单地进行HTTP通信，也可以像Universal-Image-Loader一样轻松加载网络上的图片。除了简单易用之外，Volley在性能方面也进行了大幅度的调整，它的设计目标就是非常适合去进行数据量不大，但通信频繁的网络操作，而对于大数据量的网络操作，比如说下载文件等，Volley的表现就会非常糟糕。

###下载Volley
-使用Git clone 下来

```
  git clone https://android.googlesource.com/platform/frameworks/volley
```
- 下载地址是：http://download.csdn.net/detail/sinyu890807/7152015

###使用别人的volley镜像

```
  compile 'com.mcxiaoke.volley:library:1.0.19'
```
###StringReq的用法
- 首先需要获取到一个RequestQueue对象，可以调用如下方法获取到：

```java
RequestQueue mQueue = Volley.newRequestQueue(context);
```
- 注意这里拿到的RequestQueue是一个请求队列对象，它可以缓存所有的HTTP请求，然后按照一定的算法并发地发出这些请求。
RequestQueue内部的设计就是非常合适高并发的，因此我们不必为每一次HTTP请求都创建一个RequestQueue对象，这是非常浪费资源的，基本上在每一个需要和网络交互的Activity中创建一个RequestQueue对象就足够了。

- 接下来为了要发出一条HTTP请求，我们还需要创建一个StringRequest对象，如下所示：

```java
StringRequest stringRequest = new StringRequest("http://www.baidu.com",
                        new Response.Listener<String>() {
                            @Override
                            public void onResponse(String response) {
                                Log.d("TAG", response);
                            }
                        }, new Response.ErrorListener() {
                            @Override
                            public void onErrorResponse(VolleyError error) {
                                Log.e("TAG", error.getMessage(), error);
                            }
                        });
```
可以看到，这里new出了一个StringRequest对象，StringRequest的构造函数需要传入三个参数。
- 第一个参数就是目标服务器的URL地址，目标服务器地址我们填写的是百度的首页。
- 第二个参数是服务器响应成功的回调，在响应成功的回调里打印出服务器返回的内容。
- 第三个参数是服务器响应失败的回调，在响应失败的回调里打印出失败的详细信息。
最后，将这个StringRequest对象添加到RequestQueue里面就可以了，如下所示：

```java
  mQueue.add(stringRequest);
```
- 由于Volley是要访问网络的，因此不要忘记在你的AndroidManifest.xml中添加如下权限：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```
这样的话，一个最基本的HTTP发送与响应的功能就完成了。你会发现根本还没写几行代码就轻易实现了这个功能，主要就是进行了以下三步操作：
1.创建一个RequestQueue对象。
2.创建一个StringRequest对象。
3. 将StringRequest对象添加到RequestQueue里面。

HTTP的请求类型通常有两种，GET和POST，刚才我们使用的明显是一个GET请求，那么如果想要发出一条POST请求应该怎么做呢？StringRequest中还提供了另外一种四个参数的构造函数，其中第一个参数就是指定请求类型的，我们可以使用如下方式进行指定：

```java
StringRequest stringRequest = new StringRequest(Method.POST, url,  listener, errorListener);
```
当发出POST请求的时候，Volley会尝试调用StringRequest的父类——Request中的getParams()方法来获取POST参数，那么解决方法自然也就有了，我们只需要在StringRequest的匿名类中重写getParams()方法，在这里设置POST参数就可以了，代码如下所示：

```java
StringRequest stringRequest = new StringRequest(Method.POST, url,  listener, errorListener) {
    @Override
    protected Map<String, String> getParams() throws AuthFailureError {
        Map<String, String> map = new HashMap<String, String>();
        map.put("params1", "value1");
        map.put("params2", "value2");
        return map;
    }
};
```
###JsonRequest的用法

类似于StringRequest，JsonRequest也是继承自Request类的，不过由于JsonRequest是一个抽象类，因此我们无法直接创建它的实例，那么只能从它的子类入手了。
JsonRequest有两个直接的子类.
- JsonObjectRequest,是用于请求一段**JSON数据**的
- JsonArrayRequest，是用于请求一段**JSON数组**的

```java
JsonObjectRequest jsonObjectRequest = new JsonObjectRequest("http://m.weather.com.cn/data/101010100.html", null,
        new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                Log.d("TAG", response.toString());
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                Log.e("TAG", error.getMessage(), error);
            }
        });
```
这里我们填写的URL地址是http://m.weather.com.cn/data/101010100.html，这是中国天气网提供的一个查询天气信息的接口，响应的数据就是以JSON格式返回的，然后我们在onResponse()方法中将返回的数据打印出来。
最后再将这个JsonObjectRequest对象添加到RequestQueue里就可以了，如下所示:

```java
mQueue.add(jsonObjectRequest);
```
###ImageRequest的用法
1. 创建一个RequestQueue对象。
2. 创建一个Request对象。
3. 将Request对象添加到RequestQueue里面。

- 首先需要获取到一个RequestQueue对象，可以调用如下方法获取到：

```java
RequestQueue mQueue = Volley.newRequestQueue(context);
```
- 接下来自然要去new出一个ImageRequest对象了，代码如下所示：

```java
ImageRequest imageRequest = new ImageRequest(
        "http://developer.android.com/images/home/aw_dac.png",
        new Response.Listener<Bitmap>() {
            @Override
            public void onResponse(Bitmap response) {
                imageView.setImageBitmap(response);
            }
        }, 0, 0, Config.RGB_565, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                imageView.setImageResource(R.drawable.default_image);
            }
        });
```
- 第一个参数就是图片的URL地址
- 第二个参数是图片请求成功的回调，这里我们把返回的Bitmap参数设置到ImageView中
- 第三第四个参数分别用于指定允许图片最大的宽度和高度，如果指定的网络图片的宽度或高度大于这里的最大值，则会对图片进行压缩，指定成0的话就表示不管图片有多大，都不会进行压缩。
- 第五个参数用于指定图片的颜色属性，Bitmap.Config下的几个常量都可以在这里使用，其中ARGB_8888可以展示最好的颜色属性，每个图片像素占据4个字节的大小，而RGB_565则表示每个图片像素占据2个字节大小。
- 第六个参数是图片请求失败的回调，这里我们当请求失败时在ImageView中显示一张默认图片。


最后将这个ImageRequest对象添加到RequestQueue里就可以了，如下所示：

```java
mQueue.add(imageRequest);
```
###ImageLoader的用法
1. 创建一个RequestQueue对象。
2. 创建一个ImageLoader对象。
3. 获取一个ImageListener对象。
4. 调用ImageLoader的get()方法加载网络上的图片。

- 创建RequestQueue对象

```java
RequestQueue mQueue = Volley.newRequestQueue(context);
```
- 新建一个ImageLoader对象

```java
ImageLoader imageLoader = new ImageLoader(mQueue, new ImageCache() {
    @Override
    public void putBitmap(String url, Bitmap bitmap) {
    }

    @Override
    public Bitmap getBitmap(String url) {
        return null;
    }
});
```
- 第一个参数就是RequestQueue对象
- 第二个参数是一个ImageCache对象，这里我们先new出一个空的ImageCache的实现即可。

- 接下来需要获取一个ImageListener对象

```java
ImageListener listener = ImageLoader.getImageListener(imageView,
        R.drawable.default_image, R.drawable.failed_image);
```
getImageListener()方法接收三个参数:
- 第一个参数指定用于显示图片的ImageView控件
- 第二个参数指定加载图片的过程中显示的图片
- 第三个参数指定加载图片失败的情况下显示的图片。

- 最后，调用ImageLoader的get()方法来加载图片

```java
imageLoader.get("http://192.168.1.105:8080/apple.png", listener);
```
- 第一个参数就是图片的URL地址
- 第二个参数则是刚刚获取到的ImageListener对象
- 如果你想对图片的大小进行限制，也可以使用get()方法的重载，指定图片允许的最大宽度和高度，如下所示：

```java
imageLoader.get("http://192.168.1.105:8080/apple.png",
                listener, 200, 200);
```
现在运行一下程序并开始加载图片，你将看到ImageView中会先显示一张默认的图片，等到网络上的图片加载完成后，ImageView则会自动显示该图，

虽然现在我们已经掌握了ImageLoader的用法，但是刚才介绍的ImageLoader的优点却还没有使用到。为什么呢？因为这里创建的ImageCache对象是一个空的实现，完全没能起到图片缓存的作用。其实写一个ImageCache也非常简单，但是如果想要写一个性能非常好的ImageCache，最好就要借助Android提供的LruCache功能了,欲了解可看：http://blog.csdn.net/guolin_blog/article/details/9316683

- 这里我们新建一个BitmapCache并实现了ImageCache接口，如下所示：

```java
public class BitmapCache implements ImageCache {

    private LruCache<String, Bitmap> mCache;

    public BitmapCache() {
        int maxSize = 10 * 1024 * 1024;
        mCache = new LruCache<String, Bitmap>(maxSize) {
            @Override
            protected int sizeOf(String key, Bitmap bitmap) {
                return bitmap.getRowBytes() * bitmap.getHeight();
            }
        };
    }

    @Override
    public Bitmap getBitmap(String url) {
        return mCache.get(url);
    }

    @Override
    public void putBitmap(String url, Bitmap bitmap) {
        mCache.put(url, bitmap);
    }

}
```
- 可以看到，这里我们将缓存图片的大小设置为10M。接着修改创建ImageLoader实例的代码，第二个参数传入BitmapCache的实例，如下所示：

```java
ImageLoader imageLoader = new ImageLoader(mQueue, new BitmapCache());

```
###NetworkImageView的用法
>NetworkImageView是一个自定义控制，它是继承自ImageView的，具备ImageView控件的所有功能，并且在原生的基础之上加入了加载网络图片的功能。

- NetworkImageView控件的用法要比前两种方式更加简单，大致可以分为以下五步：

1. 创建一个RequestQueue对象。
2. 创建一个ImageLoader对象。
3. 在布局文件中添加一个NetworkImageView控件。
4. 在代码中获取该控件的实例。
5. 设置要加载的图片地址。

其中，第一第二步和ImageLoader的用法是完全一样的，因此这里我们就从第三步开始学习了。首先修改布局文件中的代码，在里面加入NetworkImageView控件，如下所示：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send Request" />

    <com.android.volley.toolbox.NetworkImageView
        android:id="@+id/network_image_view"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:layout_gravity="center_horizontal"
        />

</LinearLayout>
```
- 接着在Activity获取到这个控件的实例，这就非常简单了，代码如下所示：

```java
networkImageView = (NetworkImageView) findViewById(R.id.network_image_view);
```

调用它的:
- setDefaultImageResId()方法:设置加载中显示的图片
- setErrorImageResId()方法:加载失败时显示的图片
- setImageUrl()方法:目标图片的URL地址

```java
networkImageView.setDefaultImageResId(R.drawable.default_image);
networkImageView.setErrorImageResId(R.drawable.failed_image);
networkImageView.setImageUrl("http://192.168.1.105:8080/apple.png",imageLoader);
```
其中，setImageUrl()方法接收两个参数
- 第一个参数用于指定图片的URL地址
- 第二个参数则是前面创建好的ImageLoader对象。
- 将看到和使用ImageLoader来加载图片一模一样的效果
- 使用ImageRequest和ImageLoader这两种方式来加载网络图片，都可以传入一个最大宽度和高度的参数来对图片进行压缩，而NetworkImageView中则完全没有提供设置最大宽度和高度的方法，那么是不是使用NetworkImageView来加载的图片都不会进行压缩呢？
- NetworkImageView并不需要提供任何设置最大宽高的方法也能够对加载的图片进行压缩。
- 这是由于NetworkImageView是一个控件，在加载图片的时候它会自动获取自身的宽高，然后对比网络图片的宽度，再决定是否需要对图片进行压缩。
- 也就是说，压缩过程是在内部完全自动化的，并不需要我们关心，NetworkImageView会始终呈现给我们一张大小刚刚好的网络图片，不会多占用任何一点内存，这也是NetworkImageView最简单好用的一点吧。
- 当然了，如果你不想对图片进行压缩的话，其实也很简单，只需要在布局文件中把NetworkImageView的layout_width和layout_height都设置成wrap_content就可以了，这样NetworkImageView就会将该图片的原始大小展示出来，不会进行任何压缩。


---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!
