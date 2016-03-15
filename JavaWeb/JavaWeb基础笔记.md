# 1.eclipse使用和程序的断点调试
### Eclipse的使用
- 在eclipse下Java程序的编写和运行，及java运行环境的配置。 新建java工程day01，在弹出窗口中可配置jre 工程右键属性可配置编辑器的版本

### 调试程序 Debug窗口
- Resume(F8)到下一个断点
- Step into(F5)进入到函数等里面 Step over(F6)到下一行代码
- Step return(F7)返回到调用的下一行
- Drop to Frame返回到当前方法的第一行， Terminate (F12)终止虚拟机，程序就结束了。（调试完后用） 右键watch观察变量的值

### Breakpoints窗口
- 移除所以断点

### 断点注意问题
- 调完后，移除所以断点
- 调完后，一定要结束断点的JVM

---

# 2.eclipse常用快捷键
- MyEclipse设置工作空间默认编码utf-8等，使新建工程使用默认编码 菜单栏——Window / Preferences / General / Workspace 。 内容提示：Alt + /           Content Assist
- 选中多行代码，按Tab键是整块向右推进，按Shift+Tab是整块向左缩进 快速修复：Ctrl + 1 导包：Ctrl + shift + O
- 格式化代码块：ctrl + shift + F
- 向前向后：Alt + 方向键（left  right arrow）查看源代码时 添加注释 Ctrl+Shift+/ 除去注释 Ctrl+Shift+\
- 查看源代码：Ctrl+单击     ctrl+shift+t 查看方法说明：F2
- 重置透视图：Window menu下
- 更改为大写：Ctrl+Shift+X
- 更改为小写：Ctrl+Shift+Y
- 复制行：Ctrl+Alt+向下键(有些不能用)
- 查看类的继承关系：Ctrl+T
- 查看快捷键：Ctrl+shift+L

---

# 3.junit测试框架
在Outline窗口方法上右键`Run As /JUnit Test `测试某一个方法，类上右键`run as /JUnit Test` 测试这个类的所有方法
1.用`junit`进行单元测试的时候，在每个被测试的方法中必须加上`@Test`注解
2.用`@Before`注解是在每个被测试的方法前执行。
3.用`@After`注解是在每个被测试的方法后执行。

---

# 4.java5的静态导入和自动装箱拆箱
> JDK5中新增了很多新的java特性，利用这些新语法可以帮助开发人员编写出更加高效、清晰，安全的代码。 静态导入   自动装箱/拆箱   增强for循环  可变参数  枚举反射内省  泛型  元数据

### 静态导入
`JDK 1.5` 增加的静态导入语法用于导入类的某个静态属性或方法。使用静态导入可以简化程序对类静态属性和方法的调用。 语法：
`Import static` 包名.类名.静态属性|静态方法|*

### 自动装箱/拆箱
`JDK5.0`的语法允许开发人员把一个基本数据类型直接赋给对应的包装类变量, 或者赋给 `Object` 类型的变量，这个
过程称之为自动装箱。
自动拆箱与自动装箱与之相反，即把包装类对象直接赋给一个对应的基本类型变量。

---

# 5.增强for循环
### 增强for循环
引入增强for循环的原因：在JDK5以前的版本中，遍历数组或集合中的元素，需先获得数组的长度或集合的迭代器，比较麻烦！
因此`JDK5`中定义了一种新的语法——增强for循环，以简化此类操作。增强for循环只能用在数组、或实现`Iterator`接口的集合类上 语法格式：


for(变量类型变量 ：需迭代的数组或集合){}

---

# 6.可变参数(相当于动态的数组)
测试JDK中具有可变参数的类Arrays.asList()方法。分别传多个参数、传数组，传数组又传参的情况。
**注意：传入基本数据类型数组的问题。**
从JDK 5开始, Java 允许为方法定义长度可变的参数。语法：

```java
   public void foo(int … args){   }
```
注意事项：
调用可变参数的方法时, 编译器将自动创建一个数组保存传递给方法的可变参数，因此，程序员可以在方法体中以数组的形式访问可变参数
可变参数只能处于参数列表的最后,所以一个方法最多只能有一个长度可变的参数

---

# 7.枚举类
为什么需要枚举？
一些方法在运行时，它需要的数据不能是任意的，而必须是一定范围内的值，此类问题在JDK5以前采用自定义带有枚举功能的类解决，Java5以后可以直接使用枚举予以解决。 JDK 5新增的 enum 关键字用于定义一个枚举类。

### 枚举类
枚举类具有如下特性：

1.枚举类也是一种特殊形式的Java类。

2.枚举类中声明的每一个枚举值代表枚举类的一个实例对象。A，B，C，D

3.与java中的普通类一样，在声明枚举类时，也可以声明属性、方法和构造函数，但枚举类的构造函数必须为私有的（private这点不难理解）。为什么？防止其他类初始化对象

4.枚举类也可以实现接口、或继承抽象类。
5.JDK5中扩展了swith语句，它除了可以接收int, byte, char, short外，还可以接收一个枚举类型。

6.若枚举类只有一个枚举值，则可以当作单态设计模式使用。

7.Java中声明的枚举类，均是java.lang.Enum类的孩子，它继承了Enum类的所有方法。

常用方法：
name()
ordinal()//返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零）。
valueof(Class enumClass, String name) //返回带指定名称的指定枚举类型的枚举常量。
//String str=”B”;Grade g=Grade.valueOf(Grade.class,str);
values() 此方法虽然在JDK文档中查找不到，但每个枚举类都具有该方法，它遍历枚举类的所有枚举值非常方便。
练习：请编写一个关于星期几的枚举WeekDay，要求： 枚举值：Mon、Tue 、Wed 、Thu 、Fri 、Sat 、Sun  该枚举要有一个方法，调用该方法返回中文格式的星期。

---

# 8.反射技术
### 反射什么-Class类

1. 反射就是把Java类中的各种成分映射成一个个的java对象。例如，一个类有：成员变量，方法，构造方法，包等等信息，利用反射技术可以对一个类进行解剖，把各个组成部分映射成一个个对象。
2. Class类用于表示.class文件，画图演示一个对象的创建过程。
3. 如何得到某个class文件对应的class对象。
类名.class，   对象.getClass()     Class.forName(“类名”)

以上三种方式，JVM都会把相应class文件装载到一个class对象中(只装载一次) 创建类的实例：Class.newInstance方法 数组类型的Class实例对象 Class.isArray()

总之，只要是在源程序中出现的类型，都有各自的Class实例对象，例如，int，void„

反射就是把Java类中的各种成分映射成一个个的java对象。例如，一个类有：成员变量，方法，构造方法，包等等信息，利用反射技术可以对一个类进行解剖，把各个组成部分映射成一个个对象。

掌握反射技术的要点在于：如何从一个class中反射出各个组成部分。反射出来的对象怎么用。

### Constructor类

1.Constructor类代表某个类中的一个构造方法 得到某个类所有的构造方法：
例子：

```java
Constructor [] constructors= Class.forName("java.lang.String").getConstructors();
```
得到某一个构造方法：
例子：

```java
//获得方法时要用到类型
Constructor constructor = Class.forName(“java.lang.String”).getConstructor(StringBuffer.class);
```
2.创建实例对象：
通常方式：

```java
String str = new String(new StringBuffer("abc"));
```
反射方式：

```java
String str = (String)constructor.newInstance(new StringBuffer("abc")); Class.newInstance()
```
方法： 例子：

```java
String obj = (String)Class.forName("java.lang.String").newInstance();
```
该方法内部先得到默认的构造方法，然后用该构造方法创建实例对象。

### Field类

`Field`类代表某个类中的一个成员变量
问题：得到的Field对象是对应到类上面的成员变量，还是对应到对象上的成员变量？类只有一个，而该类的实例对象有多个，如果是与对象关联，哪关联的是哪个对象呢？所以字段fieldX 代表的是x的定义，而不是具体的x变量。(注意访问权限的问题)
示例代码：

```java
ReflectPoint point = new ReflectPoint(1,7);
Field y = Class.forName("cn.itcast.corejava.ReflectPoint").getField("y");
System.out.println(y.get(point));  //
Field x = Class.forName("cn.itcast.corejava.ReflectPoint").getField("x");
Field x = Class.forName("cn.itcast.corejava.ReflectPoint").getDeclaredField("x");  x.setAccessible(true);
System.out.println(x.get(point));
```
### Method类
Method类代表某个类中的一个成员方法 得到类中的某一个方法：
例子：

```java
Method charAt = Class.forName("java.lang.String").getMethod("charAt", int.class);
```
 调用方法：
通常方式：

```java
System.out.println(str.charAt(1));
```
反射方式：

```java
System.out.println(charAt.invoke(str, 1));
```
如果传递给Method对象的invoke()方法的第一个参数为null，这有着什么样的意义呢？说明该Method对象对应的是一个静态方法！
jdk1.4和jdk1.5的invoke方法的区别：

Jdk1.5：public Object invoke(Object obj,Object... args)
Jdk1.4：public Object invoke(Object obj,Object[] args)，即按jdk1.4的语法，需要将一个数组作为参数传递给invoke方法时，数组中的每个元素分别对应被调用方法中的一个参数，所以，调用charAt方法的代码也可以用Jdk1.4改写为 charAt.invoke(“str”, new Object[]{1})形式。

### 用反射方式反射类中的main方法

目标：写一个程序，这个程序能够根据用户提供的类名，去执行该类中的main方法。用普通方式调完后，大家要明白为什么要用反射方式去调啊
问题：
启动Java程序的main方法的参数是一个字符串数组，即

```java
public static void main(String[] args)，
```
通过反射方式来调用这个main方法时，如何为invoke方法传递参数呢？按jdk1.5的语法，整个数组是一个参数，而按jdk1.4的语法，数组中的每个元素对应一个参数，当把一个字符串数组作为参数传递给invoke方法时，javac会到底按照哪种
语法进行处理呢？jdk1.5肯定要兼容jdk1.4的语法，会按jdk1.4的语法进行处理，即把数组打散成为若干个单独的参数。所以，在给main方法传递参数时，不能使用代码mainMethod.invoke(null,new String[]{“xxx”})，javac只把它当作jdk1.4的语法进行理解，而不把它当作jdk1.5的语法解释，因此会出现参数类型不对的问题。 解决办法：

```java
mainMethod.invoke(null,new Object[]{new String[]{"xxx"}});
mainMethod.invoke(null,(Object)new String[]{"xxx"});
```
编译器会作特殊处理，编译时不把参数当作数组看待，也就

---

# 9.内省技术

### 用内省技术反省javaBean
1.内省(Introspector) — JavaBean
Introspector 类为通过工具学习有关受目标 Java Bean 支持的属性、事件和方法的知识提供了一个标准方法。
2.什么是JavaBean和属性的读写方法?
有get或set方法就是一个属性，另外所有类继承了Object类的getClass（）方法，所以还有一个属性class。
3.访问JavaBean属性的两种方式：
  1.直接调用bean的setXXX或getXXX方法。
  2.通过内省技术访问(java.beans包提供了内省的API)，内省技术访问也提供了两种方式。
  A.通过PropertyDescriptor类操作Bean的属性
  B.通过Introspector类获得Bean对象的BeanInfo，然后通过 BeanInfo 来获取属性的描述器（ PropertyDescriptor ），通过这个属性描述器就可以获取某个属性对应的 getter/setter 方法，然后通过反射机制来调用这些方法。

### 内省—beanutils工具包

1.Apache组织开发了一套用于操作JavaBean的API，这套API考虑到了很多实际开发中的应用场景，因此在实际开发中很多程序员使用这套API操作JavaBean，以简化程序代码的编写。
在工程下新建lib目录，导入commons-beanutils-1.8.3.jar 和支持包commons-logging-1.1.1.jar 选中两个包，右键build path/add to build path
2.Beanutils工具包的常用类： BeanUtils PropertyUtils
ConvertUtils.regsiter(Converter convert, Class clazz)

---

# 泛型技术(Generic)

### 泛形的作用
1.JDK5以前，对象保存到集合中就会失去其特性，取出时通常要程序员手工进行类型的强制 转换，这样不可避免就会引发程序的一些安全性问题。例如：

```java
ArrayList list = new ArrayList(); list.add("abc");
Integer num = (Integer) list.get(0);  //运行时会出错，但编码时发现不了
list.add(new Random()); list.add(new ArrayList());
for(int i=0;i)
```
2.JDK5中的泛形允许程序员在编写集合代码时，就限制集合的处理类型，从而把原来程序运行时可能发生问题，转变为编译时的问题，以此提高程序的可读性和稳定性(尤其在大型程序中更为突出)。
注意：泛型是提供给javac编译器使用的，它用于限定集合的输入类型，让编译器在源代码级别上，即挡住向集合中插入非法数据。但编译器编译完带有泛形的java程序后，生成的class文件中将不再带有泛形信息，以此使程序运行效率不受到影响，这个过程称之为“擦除”。 泛形的基本术语，以ArrayList为例：<>念着typeof ArrayList中的E称为类型参数变量
ArrayList中的Integer称为实际类型参数 整个称为ArrayList泛型类型
整个ArrayList称为参数化的类型ParameterizedType

### 泛型典型应用

使用迭代器迭代泛形集合中的元素。
使用增强for循环迭代泛形集合中的元素。 存取HashMap中的元素。 使用泛形时的几个常见问题：
使用泛形时，泛形类型须为引用类型，不能是基本数据类型 //使用泛型时，如果两边都使用到泛型，两边必须一样

```java
ArrayList list = new ArrayList(); //bad
ArrayList list = new ArrayList(); //bad
ArrayList list = new ArrayList ();//ok
ArrayList list = new ArrayList();//ok
```
### 自定义泛形——泛型方法

Java程序中的普通方法、构造方法和静态方法中都可以使用泛型。方法使用泛形前，必须对泛形进行声明，语法：，T可以是任意字母，但通常必须要大写。通常需放在方法的返回值声明之前。例如：

```java
public static  void doxx(T t);
```
练习：
编写一个泛形方法，实现数组指定位置上元素的交换。
编写一个泛形方法，接收一个任意数组，并颠倒数组中的所有元素。 注意：
只有对象类型才能作为泛型方法的实际参数。 在泛型中可以同时有多个类型，例如：

```java
   public static  V getValue(K key) { return map.get(key);}
```
### 自定义泛形——泛型类

如果一个类多处都要用到同一个泛型，可以把泛形定义在类上（即类级别的泛型），语法格式如下：

```java
public class GenericDao {
  private T field1;
  public void save(T obj){}
  public T getId(int id){}
}
```
注意，静态方法不能使用类定义的泛形，而应单独定义泛形。

### 泛型的高级应用——通配符

定义一个方法，接收一个集合，并打印出集合中的所有元素，如下所示：

```java
void print (Collection c) {
  for (String e : c) {
    System.out.println(e);
  }
}
```
问题：该方法只能打印保存了Object对象的集合，不能打印其它集合。通配符用于解决此类问题，方法的定义可改写为如下形式：

```java
void print (Collection<?> c)  {   //Collection<?>(发音为:"collection of unknown")
for (Object e : c) {
  System.out.println(e);
  }
}
```

此种形式下需要注意的是：由于print方法c参数的类型为Collection<?>，即表示一种不确定的类型，因此在方法体内不能调用与类型相关的方法，例如add()方法。
总结：使用?通配符主要用于引用对象，使用了?通配符，就只能调对象与类型无关的方法，不能调用对象与类型有关的方法。

### 泛型的高级应用——有限制的通配符
限定通配符的上边界：
正确：

```java
Vector<?extends Number> x = new Vector();
```
 错误：
```java
 Vector<?extends Number> x = new Vector();
```
限定通配符的下边界：
正确：

```java
Vector<?super Integer> x = new Vector();
```
错误：

```java
Vector<?super Integer> x = new Vector();
```
问题：以下代码行不行？

```java
public void add(List<? extends String> list){  list.add("abc"); }
```
# 11.Annotation(注解) 概述

从 JDK 5.0 开始, Java 增加了对元数据(MetaData) 的支持, 也就是 Annotation(注解)。 什么是Annotation，以及注解的作用?三个基本的 Annotation: @Override: 限定重写父类方法, 该注解只能用于方法 @Deprecated: 用于表示某个程序元素(类, 方法等)已过时 @SuppressWarnings: 抑制编译器警告.
Annotation 其实就是代码里的特殊标记, 在Java技术里注解的典型应用是：可以通过反射技术去得到类里面的注解，以决定怎么去运行类。 掌握注解技术的要点： 如何定义注解
如何反射注解

### 自定义Annotation

定义新的 Annotation 类型使用 @interface 关键字 声明注解的属性：
Annotation 的属性声明方式：String name(); Annotation 属性默认值声明方式： String name() default “xxx”;
特殊属性value：如果注解中有一个名称为value的属性，那么使用注解时，可以省略value=部分，例如：@MyAnnotation(“xxx")。

### JDK的Annotation

 Annotation指修饰Annotation的Annotation。JDK中定义了如下元Annotation：
@Retention: 只能用于修饰一个 Annotation 定义, 用于指定该 Annotation 可以保留的域, @Rentention 包含一个 RetentionPolicy 类型的成员变量, 通过这个变量指定域。
RetentionPolicy.CLASS: 编译器将把注释记录在 class 文件中. 当运行 Java 程序时, JVM 不会保留注释. 这是默认值
RetentionPolicy.RUNTIME:编译器将把注释记录在 class 文件中. 当运行 Java 程序时, JVM 会保留注释. 程序可以通过反射获取该注释
RetentionPolicy.SOURCE: 编译器直接丢弃这种策略的注释
@Target: 指定注解用于修饰类的哪个成员. @Target 包含了一个名为 value 的成员变量.
@Documented: 用于指定被该元 Annotation 修饰的 Annotation 类将被 javadoc 工具提取成文档.
@Inherited: 被它修饰的 Annotation 将具有继承性.如果某个类使用了被 @Inherited 修饰的 Annotation, 则其子类将自动具有该注解

### 提取Annotation信息

JDK 5.0 在 java.lang.reflect 包下新增了 AnnotationElement 接口, 该接口代表程序中可以接受注释的程序元素 当一个 Annotation 类型被定义为运行时 Annotation 后, 该注释才是运行时可见, 当 class 文件被载入时保存在 class 文件中的 Annotation 才会被虚拟机读取
程序可以调用 AnnotationElement 对象的如下方法来访问 Annotation 信息

### Tip：动态代理

在java里，每个对象都有一个类与之对应。
现在要生成某一个对象的代理对象，这个代理对象也要通过一个类来生成，所以首先要编写用于生成代理对象的类。
如何编写生成代理对象的类，两个要素： 代理谁
如何生成代理对象 代理谁？
设计一个类变量，以及一个构造函数，记住代理类代理哪个对象。 如何生成代理对象？
设计一个方法生成代理对象（在方法内编写代码生成代理对象是此处编程的难点） Java提供了一个Proxy类，调用它的newInstance方法可以生成某个对象的代理对象，使用该方法生成代理对象时，需要三个参数：

1.生成代理对象使用哪个类装载器
2.生成哪个对象的代理对象，通过接口指定
3.生成的代理对象的方法里干什么事，由开发人员编写handler接口的实现来指定。 初学者必须理解，或不理解必须记住的2件事情：
Proxy类负责创建代理对象时，如果指定了handler（处理器），那么不管用户调用代理对象的什么方法，该方法都是调用处理器的invoke方法。
由于invoke方法被调用需要三个参数：代理对象、方法、方法的参数，因此不管代理对象哪个方法调用处理器的invoke方法，都必须把自己所在的对象、自己（调用invoke方法的方法）、方法的参数传递进来。

### Tip：动态代理应用

在动态代理技术里，由于不管用户调用代理对象的什么方法，都是调用开发人员编写的处理器的invoke方法（这相当于invoke方法拦截到了代理对象的方法调用）。
并且，开发人员通过invoke方法的参数，还可以在拦截的同时，知道用户调用的是什么方法，因此利用这两个特性，就可以实现一些特殊需求，例如：拦截用户的访问请求，以检查用户是否有访问权限、动态为某个对象添加额外的功能。

### 类加载器

类加载器负责将 .class 文件(可能在磁盘上, 也可能在网络上) 加载到内存中, 并为之生成对应的 java.lang.Class 对象
当 JVM 启动时，会形成由三个类加载器组成的初始类加载器层次结构：

### bootstrap classloader
bootstrap classloader：引导（也称为原始）类加载器，它负责加载Java的核心类。这个加载器的是非常特殊的，它实际上不是 java.lang.ClassLoader的子类，而是由JVM自身实现的。可以通过执行以下代码来获得bootstrap classloader加载了那些核心类库：

```java
URL[] urls=sun.misc.Launcher.getBootstrapClassPath().getURLs();  for (int i = 0; i < urls.length; i++) {   System.out.println(urls[i].toExternalForm());  }
```
因为JVM在启动的时候就自动加载它们,所以不需要在系统属性CLASSPATH中指定这些类库

### extension classloader

extension classloader －扩展类加载器，它负责加载JRE的扩展目录（JAVA_HOME/jre/lib/ext或者由java.ext.dirs系统属性指定的）中的JAR包。这为引入除Java核心类以外的新功能提供了一个标准机制。因为默认的扩展目录对所有从同一个JRE中启动的JVM都是通用的，所以放入这个目录的 JAR类包对所有的JVM和system classloader都是可见的。

### system classloader

system classloader －系统（也称为应用）类加载器，它负责在JVM被启动时，加载来自在命令java中的-classpath或者java.class.path系统属性或者 CLASSPATH操作系统属性所指定的JAR类包和类路径。
可以通过静态方法ClassLoader.getSystemClassLoader()找到该类加载器。如果没有特别指定，则用户自定义的任何类加载器都将该类加载器作为它的父加载器。

### 全盘负责委托机制

classloader 加载类用的是全盘负责委托机制。
全盘负责：即是当一个classloader加载一个Class的时候，这个Class所依赖的和引用的其它Class通常也由这个classloader负责载入。
委托机制：先让parent（父）类加载器寻找，只有在parent找不到的时候才从自己的类路径中去寻找。
类加载还采用了cache机制：如果 cache中保存了这个Class就直接返回它，如果没有才从文件中读取和转换成Class，并存入cache，这就是为什么修改了Class但是必须重新启动JVM才能生效,并且类只加载一次的原因。 HttpUrlConnection
不用myeclipse怎么去开发web工程

### Tip：DTD 的语法细节：元素定义1

在DTD文档中使用ELEMENT声明一个XML元素，语法格式如下所示：     元素类型可以是元素内容、或类型
如为元素内容：则需要使用()括起来，如
如为元素类型，则直接书写，DTD规范定义了如下几种类型： EMPTY：用于定义空元素，例如

 ANY：表示元素内容为任意类型。
元素内容中可以使用如下方式，描述内容的组成关系
元素内容使用空白符分隔，表示出现顺序没有要求： ×  用逗号分隔，表示内容的出现顺序必须与声明时一致。  用|分隔，表示任选其一，即多个只能出现一个
在元素内容中也可以使用+、*、?等符号表示元素出现的次数：   +: 一次或多次 (书+)    ?: 0次或一次 (书?)    *: 0次或多次  (书*) 也可使用圆括号( )批量设置，例

### Tip：属性定义

xml文档中的标签属性需通过ATTLIST为其设置属性 语法格式：
属性声明举例：
对应XML文件：  „   „ 设置说明：
#REQUIRED：必须设置该属性
#IMPLIED：可以设置也可以不设置
#FIXED：说明该属性的取值固定为一个值，在 XML 文件中不能为该属性设置其它值。但需要为该属性提供这个值
直接使用默认值：在 XML 中可以设置该值也可以不设置该属性值。若没设置则使用默认值。   举例：

### Tip：常用属性值类型
CDATA：表示属性值为普通文本字符串。 ENUMERATED  ID
ENTITY(实体)

### Tip：属性值类型

属性的类型可以是一组取值的列表，在 XML 文件中设置的属性值只能是这个列表中的某个值(枚举)

```xml
<?xml version = "1.0" encoding="GB2312" standalone="yes"?>    ]>
```
### Tip：实体定义
实体用于为一段内容创建一个别名，以后在XML文档中就可以使用别名引用这段内容了。 在DTD定义中，一条语句用于定义一个实体。
实体可分为两种类型：引用实体和参数实体。

### Tip：实体定义引用实体
引用实体主要在 XML 文档中被应用 语法格式：
：直接转变成实体内容 引用方式： &实体名称; 举例：      ……   &copyright;

### Tip：实体定义参数实体
参数实体被 DTD 文件自身使用 语法格式：
引用方式： %实体名称; 举例1：
举例2：
...

### Tip：XML解析技术概述

XML解析方式分为两种：dom和sax
dom：(Document Object Model, 即文档对象模型) 是 W3C 组织推荐的处理 XML 的一种方式。
sax： (Simple API for XML) 不是官方标准，但它是 XML 社区事实上的标准，几乎所有的 XML 解析器都支持它。 XML解析器
Crimson、Xerces 、Aelfred2 XML解析开发包 Jaxp、Jdom、dom4j

### Tip：JAXP

JAXP 开发包是J2SE的一部分，它由javax.xml、org.w3c.dom 、org.xml.sax 包及其子包组成 在 javax.xml.parsers 包中，定义了几个工厂类，程序员调用这些工厂类，可以得到对xml文档进行解析的 DOM 或
SAX 的解析器对象。

### Tip：使用JAXP进行DOM解析

javax.xml.parsers 包中的DocumentBuilderFactory用于创建DOM模式的解析器对象 ， DocumentBuilderFactory是一个抽象工厂类，它不能直接实例化，但该类提供了一个newInstance方法 ，这个方法会根据本地平台默认安装的解析器，自动创建一个工厂的对象并返回。

### Tip：获得JAXP中的DOM解析器

调用 DocumentBuilderFactory.newInstance() 方法得到创建 DOM 解析器的工厂。
调用工厂对象的 newDocumentBuilder方法得到 DOM 解析器对象。
调用 DOM 解析器对象的 parse() 方法解析 XML 文档，得到代表整个文档的 Document 对象，进行可以利用DOM

### 调虚拟机内存大小

-Xmx83886080  C:\Program Files\Java\jdk1.7.0\docs\technotes\tools\windows\java.html        -Xmx81920k        -Xmx80m

### Tip：DOM编程
DOM模型(document object model)
DOM解析器在解析XML文档时，会把文档中的所有元素，按照其出现的层次关系，解析成一个个Node对象(节点)。
在dom中，节点之间关系如下：
位于一个节点之上的节点是该节点的父节点(parent) 一个节点之下的节点是该节点的子节点（children）
同一层次，具有相同父节点的节点是兄弟节点（sibling）  一个节点的下一个层次的节点集合是节点后代(descendant)
父、祖父节点及所有位于节点上面的，都是节点的祖先(ancestor)  节点类型（下页ppt）
Node对象提供了一系列常量来代表结点的类型，当开发人员获得某个Node类型后，就可以把Node节点转换成相应的节点对象(Node的子类对象)，以便于调用其特有的方法。（查看API文档）
Node对象提供了相应的方法去获得它的父结点或子结点。编程人员通过这些方法就可以读取整个XML文档的内容、或添加、修改、删除XML文档的内容了。

### Tip：DOM方式解析XML文件
DOM解析编程 遍历所有节点 查找某一个节点 删除结点 更新结点 添加节点

### Tip：更新XML文档
javax.xml.transform包中的Transformer类用于把代表XML文件的Document对象转换为某种格式后进行输出，例如把xml文件应用样式表后转成一个html文档。利用这个对象，当然也可以把Document对象又重新写入到一个XML文件中。
Transformer类通过transform方法完成转换操作，该方法接收一个源和一个目的地。我们可以通过： javax.xml.transform.dom.DOMSource类来关联要转换的document对象，  用javax.xml.transform.stream.StreamResult 对象来表示数据的目的地。  Transformer对象通过TransformerFactory获得。

### Tip：SAX解析
在使用 DOM 解析 XML 文档时，需要读取整个 XML 文档，在内存中构架代表整个 DOM 树的Doucment对象，从而再对XML文档进行操作。此种情况下，如果 XML 文档特别大，就会消耗计算机的大量内存，并且容易导致内存溢出。
SAX解析允许在读取文档的时候，即对文档进行处理，而不必等到整个文档装载完才会文档进行操作。
SAX采用事件处理的方式解析XML文件，利用 SAX 解析 XML 文档，涉及两个部分：解析器和事件处理器： 解析器可以使用JAXP的API创建，创建出SAX解析器后，就可以指定解析器去解析某个XML文档。
解析器采用SAX方式在解析某个XML文档时，它只要解析到XML文档的一个组成部分，都会去调用事件处理器的一个方法，解析器在调用事件处理器的方法时，会把当前解析到的xml文件内容作为方法的参数传递给事件处理器。
事件处理器由程序员编写，程序员通过事件处理器中方法的参数，就可以很轻松地得到sax解析器解析到的数据，从而可以决定如何对数据进行处理。
阅读ContentHandler API文档，常用方法：startElement、endElement、characters

###  Tip：SAX方式解析XML文档

使用SAXParserFactory创建SAX解析工厂
SAXParserFactory spf = SAXParserFactory.newInstance(); 通过SAX解析工厂得到解析器对象   SAXParser sp = spf.newSAXParser();
通过解析器对象得到一个XML的读取器 XMLReader xmlReader = sp.getXMLReader();
设置读取器的事件处理器
xmlReader.setContentHandler(new BookParserHandler()); 解析xml文件

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!
