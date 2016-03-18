###静态导入
  - JDK 1.5 增加的静态导入语法用于导入类的某个静态属性或方法。使用静态导入可以简化程序对类静态属性和方法的调用。
  - Import static 包名.类名.静态属性|静态方法|*

###自动装箱/拆箱
  - 自动装箱：指开发人员可以把一个基本数据类型直接赋给对应的包装类。
  - 自动拆箱：指开发人员可以把一个包装类对象直接赋给对应的基本数据类型。
  Integer i = 1; //装箱
  int j = i;//拆箱

###增强for循环
  - 引入增强for循环的原因：在JDK5以前的版本中，遍历数组或集合中的元素，需先获得数组的长度或集合的迭代器，比较麻烦！
  - 因此JDK5中定义了一种新的语法——增强for循环，以简化此类操作。增强for循环只能用在数组、或实现Iterator接口的集合类上
  - 语法格式：  for(变量类型变量 ：需迭代的数组或集合){}


```java
/*
   * 增强for循环
   */

  @Test
  public void test1() {
    int arr[] = { 1, 2, 3 };

    for (int num : arr) {
      System.err.println(num);
    }
  }

  @Test
  public void test2() {
    List list = new ArrayList();
    list.add(1);
    list.add(2);
    list.add(3);

    for (Object obj : list) {
      int i = (int) obj;
      System.err.println(i);
    }
  }

  @Test
  public void test3() {
    Map map = new LinkedHashMap();
    map.put("1", "aaa");
    map.put("2", "bbb");
    map.put("3", "ccc");

    // 传统方法1
    Set set = map.keySet();
    Iterator it = set.iterator();
    while (it.hasNext()) {

      String key = (String) it.next();
      String value = (String) map.get(key);
      System.err.println(key + "=" + value);
    }

  }

  @Test
  public void test4() {
    Map map = new LinkedHashMap();
    map.put("1", "aaa");
    map.put("2", "bbb");
    map.put("3", "ccc");

    // 传统方法2
    Set set = map.entrySet();
    Iterator it =set.iterator();
    while (it.hasNext()) {
      Map.Entry entry = (Map.Entry) it.next();
      String key = (String) entry.getKey();
      String value = (String) entry.getValue();
      System.err.println(key + "=" + value);
    }
  }
  @Test
  public void test5() {
    Map map = new LinkedHashMap();
    map.put("1", "aaa");
    map.put("2", "bbb");
    map.put("3", "ccc");

    // 增强for取map的第一种方式
    for(Object obj :map.keySet()){
      String key = (String) obj;
      String value = (String) map.get(key);
      System.err.println(key + "=" + value);
    }


  }

  @Test
  public void test6() {
    Map map = new LinkedHashMap();
    map.put("1", "aaa");
    map.put("2", "bbb");
    map.put("3", "ccc");

    // 增强for取map的第2种方式
    for(Object obj : map.entrySet()){
      Map.Entry entry = (Entry) obj;
      String key = (String) entry.getKey();
      String value = (String) entry.getValue();
      System.err.println(key + "=" + value);
    }
  }

  //使用增强for循环需要注意的几个问题：增强for只适合取数据
  //如果要修改数组或者集合中的数据，要用传统方式
```

###可变参数(相当于动态的数组)
测试JDK中具有可变参数的类Arrays.asList()方法。分别传多个参数、传数组，传数组又传参的情况。
注意：传入基本数据类型数组的问题。
从JDK 5开始, Java 允许为方法定义长度可变的参数。语法：   public void foo(int … args){   } 注意事项：
- 调用可变参数的方法时, 编译器将自动创建一个数组保存传递给方法的可变参数，因此，程序员可以在方法体中以数组的形式访问可变参数
- 可变参数只能处于参数列表的最后, 所以一个方法最多只能有一个长度可变的参数

###枚举类

1. 枚举类也是一种特殊形式的Java类。
2. 枚举类中声明的每一个枚举值代表枚举类的一个实例对象。A，B，C，D
3. 与java中的普通类一样，在声明枚举类时，也可以声明属性、方法和构造函数，但枚举类的构造函数必须为私有的（private这点不难理解）。为什么？防止其他类初始化对象
4. 枚举类也可以实现接口、或继承抽象类。
5. JDK5中扩展了swith语句，它除了可以接收int, byte, char, short外，还可以接收一个枚举类型。
6. 若枚举类只有一个枚举值，则可以当作单态设计模式使用。
7. Java中声明的枚举类，均是java.lang.Enum类的孩子，它继承了Enum类的所有方法。

常用方法：

- name()
- ordinal()//返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零）。
- valueof(Class enumClass, String name) //返回带指定名称的指定枚举类型的枚举常量。
- //String str=“B”;Grade g=Grade.valueOf(Grade.class,str);
- values() 此方法虽然在JDK文档中查找不到，但每个枚举类都具有该方法，它遍历枚举类的所有枚举值非常方便。

练习：请编写一个关于星期几的枚举WeekDay，要求： 枚举值：Mon、Tue 、Wed 、Thu 、Fri 、Sat 、Sun  该枚举要有一个方法，调用该方法返回中文格式的星期。
