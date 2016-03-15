#HttpClient
###发送get请求
* 创建一个客户端对象

```java
	HttpClient client = new DefaultHttpClient();
```
* 创建一个get请求对象

```java
	HttpGet hg = new HttpGet(path);
```
* 发送get请求，建立连接，返回响应头对象

```java
	HttpResponse hr = hc.execute(hg);
```
* 获取状态行对象，获取状态码，如果为200则说明请求成功

```java
		if(hr.getStatusLine().getStatusCode() == 200){
			//拿到服务器返回的输入流
			InputStream is = hr.getEntity().getContent();
			String text = Utils.getTextFromStream(is);
		}
```
###发送post请求

```java
		//创建一个客户端对象
		HttpClient client = new DefaultHttpClient();
		//创建一个post请求对象
		HttpPost hp = new HttpPost(path);
```
* 往post对象里放入要提交给服务器的数据

```java
		//要提交的数据以键值对的形式存在BasicNameValuePair对象中
		List<NameValuePair> parameters = new ArrayList<NameValuePair>();
		BasicNameValuePair bnvp = new BasicNameValuePair("name", name);
		BasicNameValuePair bnvp2 = new BasicNameValuePair("pass", pass);
		parameters.add(bnvp);
		parameters.add(bnvp2);
		//创建实体对象，指定进行URL编码的码表
		UrlEncodedFormEntity entity = new UrlEncodedFormEntity(parameters, "utf-8");
		//为post请求设置实体
		hp.setEntity(entity);
```
---
#异步HttpClient框架
###发送get请求

```java
		//创建异步的httpclient对象
		AsyncHttpClient ahc = new AsyncHttpClient();
		//发送get请求
		ahc.get(path, new MyHandler());
```
* 注意AsyncHttpResponseHandler两个方法的调用时机

```java
		class MyHandler extends AsyncHttpResponseHandler{

			//http请求成功，返回码为200，系统回调此方法
			@Override
			public void onSuccess(int statusCode, Header[] headers,
					//responseBody的内容就是服务器返回的数据
					byte[] responseBody) {
				Toast.makeText(MainActivity.this, new String(responseBody), 0).show();

			}

			//http请求失败，返回码不为200，系统回调此方法
			@Override
			public void onFailure(int statusCode, Header[] headers,
					byte[] responseBody, Throwable error) {
				Toast.makeText(MainActivity.this, "返回码不为200", 0).show();

			}

		}
```
###发送post请求

* 使用RequestParams对象封装要携带的数据

```java
		//创建异步httpclient对象
		AsyncHttpClient ahc = new AsyncHttpClient();
		//创建RequestParams封装要携带的数据
		RequestParams rp = new RequestParams();
		rp.add("name", name);
		rp.add("pass", pass);
		//发送post请求
		ahc.post(path, rp, new MyHandler());
```
---
#多线程下载
>原理：服务器CPU分配给每条线程的时间片相同，服务器带宽平均分配给每条线程，所以客户端开启的线程越多，就能抢占到更多的服务器资源

###确定每条线程下载多少数据
* 发送http请求至下载地址

```java
		String path = "http://192.168.1.102:8080/editplus.exe";
		URL url = new URL(path);
		HttpURLConnection conn = (HttpURLConnection) url.openConnection();
		conn.setReadTimeout(5000);
		conn.setConnectTimeout(5000);
		conn.setRequestMethod("GET");
```
* 获取文件总长度，然后创建长度一致的临时文件

```java
		if(conn.getResponseCode() == 200){
			//获得服务器流中数据的长度
			int length = conn.getContentLength();
			//创建一个临时文件存储下载的数据
			RandomAccessFile raf = new RandomAccessFile(getFileName(path), "rwd");
			//设置临时文件的大小
			raf.setLength(length);
			raf.close();
		}
```

* 确定线程下载多少数据

```java
			//计算每个线程下载多少数据
			int blockSize = length / THREAD_COUNT;
```
###计算每条线程下载数据的开始位置和结束位置

```java
		for(int id = 1; id <= 3; id++){
			//计算每个线程下载数据的开始位置和结束位置
			int startIndex = (id - 1) * blockSize;
			int endIndex = id * blockSize - 1;
			if(id == THREAD_COUNT){
				endIndex = length;
			}

			//开启线程，按照计算出来的开始结束位置开始下载数据
			new DownLoadThread(startIndex, endIndex, id).start();
		}
```
###再次发送请求至下载地址，请求开始位置至结束位置的数据

```java
		String path = "http://192.168.1.102:8080/editplus.exe";

		URL url = new URL(path);
		HttpURLConnection conn = (HttpURLConnection) url.openConnection();
		conn.setReadTimeout(5000);
		conn.setConnectTimeout(5000);
		conn.setRequestMethod("GET");

		//向服务器请求部分数据
		conn.setRequestProperty("Range", "bytes=" + startIndex + "-" + endIndex);
		conn.connect();
```
* 下载请求到的数据，存放至临时文件中

```java
			//请求部分数据，相应码是206
		if(conn.getResponseCode() == 206){
			InputStream is = conn.getInputStream();
			RandomAccessFile raf = new RandomAccessFile(getFileName(path), "rwd");
			//指定从哪个位置开始存放数据
			raf.seek(startIndex);
			byte[] b = new byte[1024];
			int len;
			while((len = is.read(b)) != -1){
				raf.write(b, 0, len);
			}
			raf.close();
		}
```
---
#带断点续传的多线程下载
* 定义一个int变量记录每条线程下载的数据总长度，然后加上该线程的下载开始位置，得到的结果就是下次下载时，该线程的开始位置，把得到的结果存入缓存文件

```java
		//用来记录当前线程总的下载长度
		int total = 0;
		while((len = is.read(b)) != -1){
			raf.write(b, 0, len);
			total += len;
			//每次下载都把新的下载位置写入缓存文本文件
			RandomAccessFile raf2 = new RandomAccessFile(threadId + ".txt", "rwd");
			raf2.write((startIndex + total + "").getBytes());
			raf2.close();
		}
```
* 下次下载开始时，先读取缓存文件中的值，得到的值就是该线程新的开始位置

```java
		FileInputStream fis = new FileInputStream(file);
		BufferedReader br = new BufferedReader(new InputStreamReader(fis));
		String text = br.readLine();
		int newStartIndex = Integer.parseInt(text);
		//把读到的值作为新的开始位置
		startIndex = newStartIndex;
		fis.close();
```
* 三条线程都下载完毕之后，删除缓存文件

```java
		RUNNING_THREAD--;
		synchronized (path){
			if(RUNNING_THREAD == 0){
			for(int i = 0; i <= ThreadCount; i++){
				File f = new File(i + ".txt");
				f.delete();
			}
			finishedThread = 0 ;
		}
}

```
---
#手机版的断点续传多线程下载器
* 把刚才的代码直接粘贴过来就能用，记得在访问文件时的路径要改成Android的目录，添加访问网络和外部存储的路径

###用进度条显示下载进度
- 设置style

```xml
<ProgressBar
	android:layout_width="match_paren"
	android:layout_height="wrap_content"
	style="@android:style/Widget.ProgressBar.Horizontal"/>
```

* 拿到下载文件总长度时，设置进度条的最大值

```java
		//设置进度条的最大值
		pb.setMax(length);
```
* 进度条需要显示三条线程的整体下载进度，所以三条线程每下载一次，就要把新下载的长度加入进度条
	* 定义一个int全局变量，记录三条线程的总下载长度

```java
			int progress;
```
	* 刷新进度条

```java
			while((len = is.read(b)) != -1){
				raf.write(b, 0, len);


				//把当前线程本次下载的长度加到进度条里
				progress += len;
				pb.setProgress(progress);
			}
```
* 每次断点下载时，从新的开始位置开始下载，进度条也要从新的位置开始显示，在读取缓存文件获取新的下载开始位置时，也要处理进度条进度

```java
		FileInputStream fis = new FileInputStream(file);
		BufferedReader br = new BufferedReader(new InputStreamReader(fis));
		String text = br.readLine();
		int newStartIndex = Integer.parseInt(text);

		//新开始位置减去原本的开始位置，得到已经下载的数据长度
		int alreadyDownload = newStartIndex - startIndex;
		//把已经下载的长度设置入进度条
		progress += alreadyDownload;
```
###添加文本框显示百分比进度

```java
		tv.setText((long)progress * 100 / pb.getMax() + "%");
```
---

#xUtils的HttpUtils的使用
>HttpUtils本身就支持多线程断点续传，使用起来非常的方便

* 创建HttpUtils对象

```java
		HttpUtils http = new HttpUtils();
```
* 下载文件

```java
		http.download(url, //下载请求的网址
				target, //下载的数据保存路径和文件名
				true, //是否开启断点续传
				true, //如果服务器响应头中包含了文件名，那么下载完毕后自动重命名
				new RequestCallBack<File>() {//侦听下载状态

			//下载成功此方法调用
			@Override
			public void onSuccess(ResponseInfo<File> arg0) {
				tv.setText("下载成功" + arg0.result.getPath());
			}

			//下载失败此方法调用，比如文件已经下载、没有网络权限、文件访问不到，方法传入一个字符串参数告知失败原因
			@Override
			public void onFailure(HttpException arg0, String arg1) {
				tv.setText("下载失败" + arg1);
			}

			//在下载过程中不断的调用，用于刷新进度条
			@Override
			public void onLoading(long total, long current, boolean isUploading) {
				super.onLoading(total, current, isUploading);
				//设置进度条总长度
				pb.setMax((int) total);
				//设置进度条当前进度
				pb.setProgress((int) current);
				tv_progress.setText(current * 100 / total + "%");
			}
		});
```

---
- 邮箱 ：elisabethzhen@163.com
- Good Luck!
