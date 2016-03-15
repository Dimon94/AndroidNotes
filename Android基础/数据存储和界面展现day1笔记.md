#常见布局
###相对布局
#####RelativeLayout
* 组件默认左对齐、顶部对齐
* 设置组件在指定组件的右边

		 android:layout_toRightOf="@id/tv1"

* 设置在指定组件的下边

		android:layout_below="@id/tv1"

* 设置右对齐父元素

		android:layout_alignParentRight="true"

* 设置与指定组件右对齐

		 android:layout_alignRight="@id/tv1"

###线性布局
#####LinearLayout
* 指定各个节点的排列方向

		android:orientation="horizontal"

* 设置右对齐

		android:layout_gravity="right"

* 当竖直布局时，只能左右对齐和水平居中，顶部底部对齐竖直居中无效
* 当水平布局时，只能顶部底部对齐和竖直居中
* 使用match_parent时注意不要把其他组件顶出去
* 线性布局非常重要的一个属性：权重

		android:layout_weight="1"

* 权重设置的是按比例分配剩余的空间

###帧布局
#####FrameLayout
* 默认组件都是左对齐和顶部对齐，每个组件相当于一个div
* 可以更改对齐方式

		android:layout_gravity="bottom"

* 不能相对于其他组件布局

###表格布局
#####TableLayout
* 每个<TableRow/>节点是一行，它的每个子节点是一列
* 表格布局中的节点可以不设置宽高，因为设置了也无效
	* 根节点<TableLayout/>的子节点宽为匹配父元素，高为包裹内容
	* <TableRow/>节点的子节点宽为包裹内容，高为包裹内容
	* 以上默认属性无法修改

* 根节点中可以设置以下属性，表示让第1列拉伸填满屏幕宽度的剩余空间

		android:stretchColumns="1"

###绝对布局
#####AbsoluteLayout
* 直接指定组件的x、y坐标

		android:layout_x="144dp"
        android:layout_y="154dp"

---
#logcat
* 日志信息总共分为5个等级
	* verbose
	* debug
	* info
	* warn
	* error
* 定义过滤器方便查看
* System.out.print输出的日志级别是info，tag是System.out
* Android提供的日志输出api

		Log.v(TAG, "加油吧，童鞋们");
		Log.d(TAG, "加油吧，童鞋们");
		Log.i(TAG, "加油吧，童鞋们");
		Log.w(TAG, "加油吧，童鞋们");
		Log.e(TAG, "加油吧，童鞋们");

----
#文件读写操作
* Ram内存：运行内存，相当于电脑的内存
* Rom内存：内部存储空间，相当于电脑的硬盘
* sd卡：外部存储空间，相当于电脑的移动硬盘

###在内部存储空间中读写文件
>小案例：用户输入账号密码，勾选“记住账号密码”，点击登录按钮，登录的同时持久化保存账号和密码

#####1. 定义布局

#####2. 完成按钮的点击事件
* 弹土司提示用户登录成功

```java
		Toast.makeText(this, "登录成功", Toast.LENGTH_SHORT).show();
```
#####3. 拿到用户输入的数据
* 判断用户是否勾选保存账号密码

```java
		CheckBox cb = (CheckBox) findViewById(R.id.cb);
		if(cb.isChecked()){

		}
```
#####4. 开启io流把文件写入内部存储
* 直接开启文件输出流写数据

```java
		//持久化保存数据
			File file = new File("data/data/com.itheima.rwinrom/info.txt");
			FileOutputStream fos = new FileOutputStream(file);
			fos.write((name + "##" + pass).getBytes());
			fos.close();
```
* 读取数据前先检测文件是否存在

```java
		if(file.exists())
```
* 读取保存的数据，也是直接开文件输入流读取

```java
		File file = new File("data/data/com.itheima.rwinrom/info.txt");
		FileInputStream fis = new FileInputStream(file);
		//把字节流转换成字符流
		BufferedReader br = new BufferedReader(new InputStreamReader(fis));
		String text = br.readLine();
		String[] s = text.split("##");
```
* 读取到数据之后，回显至输入框

```java
		et_name.setText(s[0]);
		et_pass.setText(s[1]);
```
* 应用只能在自己的包名目录下创建文件，不能到别人家去创建

###直接复制项目
* 需要改动的地方：
	* 项目名字
	* 应用包名
	* R文件重新导包

###使用路径api读写文件
* getFilesDir()得到的file对象的路径是data/data/com.itheima.rwinrom2/files
	* 存放在这个路径下的文件，只要你不删，它就一直在
* getCacheDir()得到的file对象的路径是data/data/com.itheima.rwinrom2/cache
	* 存放在这个路径下的文件，当内存不足时，有可能被删除

* 系统管理应用界面的清除缓存，会清除cache文件夹下的东西，清除数据，会清除整个包名目录下的东西

-----
#在外部存储读写数据
###sd卡的路径
* sdcard：2.3之前的sd卡路径
* mnt/sdcard：4.3之前的sd卡路径
* storage/sdcard：4.3之后的sd卡路径

* 最简单的打开sd卡的方式

```java
		File file = new File("sdcard/info.txt");
```
* 写sd卡需要权限

		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

* 读sd卡，在4.0之前不需要权限，4.0之后可以设置为需要

		<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

* 使用api获得sd卡的真实路径，部分手机品牌会更改sd卡的路径

		Environment.getExternalStorageDirectory()

* 判断sd卡是否准备就绪

```java
		if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED))
```
-----
#查看源代码查找获取sd卡剩余容量的代码
* 导入Settings项目
* 查找“可用空间”得到

		 <string name="memory_available" msgid="418542433817289474">"可用空间"</string>

* 查找"memory_available"，得到

		<Preference android:key="memory_sd_avail"
            style="?android:attr/preferenceInformationStyle"
            android:title="@string/memory_available"
            android:summary="00"/>

* 查找"memory_sd_avail"，得到

```java
		//这个字符串就是sd卡剩余容量
		formatSize(availableBlocks * blockSize) + readOnly
		//这两个参数相乘，得到sd卡以字节为单位的剩余容量
		availableBlocks * blockSize
```
* 存储设备会被分为若干个区块，每个区块有固定的大小
* **区块大小** * **区块数量** 等于 **存储设备的总大小**

###获取sd卡剩余容量
```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    File path = Environment.getExternalStorageDirectory();
        StatFs stat = new StatFs(path.getPath());
        long blockSize;
        long totalBlocks;
        long availableBlocks;

        //获取当前系统版本的等级
        if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR2){
           blockSize = stat.getBlockSizeLong();
             totalBlocks = stat.getBlockCountLong();
             availableBlocks = stat.getAvailableBlocksLong();
        }
        else{
          blockSize = stat.getBlockSize();
            totalBlocks = stat.getBlockCount();
            availableBlocks = stat.getAvailableBlocks();
        }

        TextView tv = (TextView) findViewById(R.id.tv);
        tv.setText(formatSize(availableBlocks * blockSize));//剩余容量
  }

  private String formatSize(long size) {
        return Formatter.formatFileSize(this, size);
    }
```
-------
#Linux文件的访问权限
* 在Android中，每一个应用是一个独立的用户
* 使用10个字母表示
* drwxrwxrwx
* 第1位：d表示文件夹，-表示文件
* 第2-4位：rwx，表示这个文件的拥有者用户（owner）对该文件的权限
	* r：读
	* w：写
	* x：执行
* 第5-7位：rwx，表示跟文件拥有者用户同组的用户（grouper）对该文件的权限
* 第8-10位：rwx，表示其他用户组的用户（other）对该文件的权限

----
#openFileOutput的四种模式
* MODE_PRIVATE：-rw-rw----
* MODE_APPEND:-rw-rw----
* MODE_WORLD_WRITEABLE:-rw-rw--w-
* MODE_WORLD_READABLE:-rw-rw-r--

----
#SharedPreference
>用SharedPreference存储账号密码

* 往SharedPreference里写数据

```java
		//拿到一个SharedPreference对象
		SharedPreferences sp = getSharedPreferences("config", MODE_PRIVATE);
		//拿到编辑器
		Editor ed = sp.edit();
		//写数据
		ed.putBoolean("name", name);
		ed.commit();
```
* 从SharedPreference里取数据

```java
		SharedPreferences sp = getSharedPreferences("config", MODE_PRIVATE);
		//从SharedPreference里取数据
		String name = sp.getBoolean("name", "");
```
---
#生成XML文件备份短信

* 创建几个虚拟的短信对象，存在list中
* 备份数据通常都是备份至sd卡
###使用StringBuffer拼接字符串
* 把整个xml文件所有节点append到sb对象里

```java
		sb.append("<?xml version='1.0' encoding='utf-8' standalone='yes' ?>");
		//添加smss的开始节点
		sb.append("<smss>");
		.......
```
* 把sb写到输出流中

```java
		fos.write(sb.toString().getBytes());
```
###使用XMl序列化器生成xml文件
* 得到xml序列化器对象

```java
		XmlSerializer xs = Xml.newSerializer();
```
* 给序列化器设置输出流

```java
		File file = new File(Environment.getExternalStorageDirectory(), "backupsms.xml");
		FileOutputStream fos = new FileOutputStream(file);
		//给序列化器指定好输出流
		xs.setOutput(fos, "utf-8");//enconding:指定用什么编码生成xml文件
```
* 开始生成xml文件

```java
		//enconding:指定头结点中的enconding属性的值
		xs.startDocument("utf-8", true);
		xs.startTag(null, "smss");
		......
		xs.endTag(null, "smss");
		//告诉序列化器，文件生成完毕
		xs.endDocument();
```
---
#pull解析xml文件
* 先自己写一个xml文件，存一些天气信息

###拿到xml文件

```java
		InputStream is = getClassLoader().getResourceAsStream("weather.xml");
```
###拿到pull解析器

```java
		XmlPullParser xp = Xml.newPullParser();
```
###开始解析
* 拿到指针所在当前节点的事件类型

```java
		int type = xp.getEventType();
```
* 事件类型主要有五种
	* START_DOCUMENT：xml头的事件类型
	* END_DOCUMENT：xml尾的事件类型
	* START_TAG：开始节点的事件类型
	* END_TAG：结束节点的事件类型
	* TEXT：文本节点的事件类型

* 如果获取到的事件类型不是END_DOCUMENT，就说明解析还没有完成，如果是，解析完成，while循环结束

```java
		while(type != XmlPullParser.END_DOCUMENT)
```
* 当我们解析到不同节点时，需要进行不同的操作，所以判断一下当前节点的name
	* 当解析到weather的开始节点时，new出list
	* 当解析到city的开始节点时，创建city对象，创建对象是为了更方便的保存即将解析到的文本
	* 当解析到name开始节点时，获取下一个节点的文本内容，temp、pm也是一样

```java
			case XmlPullParser.START_TAG:
			//获取当前节点的名字
				if("weather".equals(xp.getName())){
					citys = new ArrayList<City>();
				}
				else if("city".equals(xp.getName())){
					city = new City();
				}
				else if("name".equals(xp.getName())){
					//获取当前节点的下一个节点的文本
					String name = xp.nextText();
					city.setName(name);
				}
				else if("temp".equals(xp.getName())){
					String temp = xp.nextText();
					city.setTemp(temp);
				}
				else if("pm".equals(xp.getName())){
					String pm = xp.nextText();
					city.setPm(pm);
				}
				break;
```
* 当解析到city的结束节点时，说明city的三个子节点已经全部解析完了，把city对象添加至list

```java
		case XmlPullParser.END_TAG:
			if("city".equals(xp.getName())){
					citys.add(city);
			}
```
>换行符也是文本节点

---
- 邮箱 ：elisabethzhen@163.com
- Good Luck!