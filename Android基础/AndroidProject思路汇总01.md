---
title: AndroidProject思路汇总
---
#01.电话拨号器

###流程：

1. 绘制界面，一个**EditText**一个**Button**
2. 获取**ID**
3. 建立按键监听
4. 在监听中获取**EditText**的电话信息-
5. 创建Intent意图对象

###代码如下：

```java
String phone = editText.getText().toString();
                //创建意图对象
                Intent intent = new Intent();
                //把动作封装到意图对象当中
                intent.setAction(Intent.ACTION_CALL);
                //设置打给谁
                intent.setData(Uri.parse("tel:" + phone));
                //把动作告诉系统
                startActivity(intent);
```

---
#02.messenger

###需求：

- 可以输入信息对方
- 可以输入信息
- 可以发生短信

###流程：

1. 绘制基本界面，两个EditText一个Button
2. 获取ID
3. 设置按键监听，获取电话号码以及传输信息

```java
public void onClick(View v) {
                //拿到用户输入的号码和内容
                EditText mMsg = (EditText) findViewById(R.id.et_content);
                EditText mPhone = (EditText) findViewById(R.id.et_phone);

                String phone = mMsg.getText().toString();
                String content = mPhone.getText().toString();

                //1.获取短信管理器
                SmsManager sm = SmsManager.getDefault();
                //切割短信，把长短信分为若干个小短信
                ArrayList<String> smss = sm.divideMessage(content);
                //2.创建一个PendingIntent
                PendingIntent pi = PendingIntent.getActivity(MainActivity.this,0,new Intent(),0);

                //3.发送短信
                for (String string : smss){

                    sm.sendTextMessage(phone,null,string,pi, null);
                }
    }
```

---
#03.inputOutput

###需求

- 绘制登陆界面
- 可以保存用户密码

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


##在外部存储读写数据
###sd卡的路径
* sdcard：2.3之前的sd卡路径
* mnt/sdcard：4.3之前的sd卡路径
* storage/sdcard：4.3之后的sd卡路径

最简单的打开sd卡的方式

```java
    File file = new File("sdcard/info.txt");
```
* 写sd卡需要权限

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

* 读sd卡，在4.0之前不需要权限，4.0之后可以设置为需要

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

* 使用api获得sd卡的真实路径，部分手机品牌会更改sd卡的路径

```java
    Environment.getExternalStorageDirectory()
```
* 判断sd卡是否准备就绪

```java
    if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED))
```

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

###SharedPreference
>用SharedPreference存储账号密码

* 往SharedPreference里写数据

```java
    //拿到一个SharedPreference对象
    SharedPreferences sp = getSharedPreferences("config", MODE_PRIVATE);
    //拿到编辑器
    Editor ed = sp.edit();
    //写数据
    ed.putString("name", name);
    ed.commit();
```
* 从SharedPreference里取数据

```java
    SharedPreferences sp = getSharedPreferences("config", MODE_PRIVATE);
    //从SharedPreference里取数据
    String name = sp.getString("name", "");
```

---
- 邮箱 ：elisabethzhen@163.com
- Good Luck!