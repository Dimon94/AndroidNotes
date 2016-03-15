#eclipse的使用
###常用快捷键
- 内容提示：`Alt` + `/`
- 快速修复：`Ctrl` + `1`
- 导包：`Ctrl` + `Shift` + `O`
- 格式化代码：`Ctrl` + `Shift` + `F`
- 向前向后：`Alt` + 方向键
- 添加注释：`Ctrl` + `Shift` + `/`
- 除去注释：`Ctrl` + `Shift` + `\`
- 查看方法说明：`F2`
- 重置透视图：`Window` + `Reset perspective`
- 更改为大写：`Ctrl` + `Shift` + `X`
- 更改为小写：`Ctrl` + `Shift` + `Y`
- 复制行：`Ctrl` + `Alt` + 向下键（有些不能用）
- 移动行：`Alt` + 向上 向下键
- 查看类的继承关系 `Ctrl` + `T`
- 查看源代码：
  - `Ctrl` + 右键点击
  - `Ctrl` + `Shift` + `T`
- 查看所有的快捷键：`Ctrl` + `Shift` + `L`

## 常用快捷键 ##

- ctrl + shift + o 导包
- ctrl + shift + t 快速查找某个类
- 先按ctrl + 2 ,再点L, 创建变量并命名
- ctrl + o , 在当前类中,快速查找某个方法
- ctrl + k, 向下查找某个字符串
- ctrl + shift + k, 向上查找某个字符串
- alt + 左方向键 跳转上一个页面




###程序的调试与运行
- `F5`（跳入） `F6`（跳过） `F7`（跳出）
- drop to frame : 跳到当前方法的第一行

resume ：跳到下一个断点（如果没有下一个，则运行完整个程序）
watch：观察变量或表达式的值

断点注意的问题：
1.断点调试完成后，要在breakpoints视图中清除所有断点
2.断点调试完成后，一定要记得结束运行断点的jvm

###JUnit
@Test
@Before
@After
@BeforeClass
@AfterClass
Assert.assertEquals("2","1");

在Outline窗口方法上右键Run As /JUnit Test 测试某一个方法，类上右键run as /JUnit Test 测试这个类的所有方法
  1.用junit进行单元测试的时候，在每个被测试的方法中必须加上@Test注解
  2.用@Before注解是在每个被测试的方法前执行。
  3.用@After注解是在每个被测试的方法后执行。
  4.用@BeforeClass 注解的静态方法是在所有方法被测试之前执行的方法，就像类里面的构造方法一样。用来初始化一些要用到的变量等资源。
  5.用@AterClass注解的静态方法是在所有被测试的方法之后执行。相当于c++中析构函数。用来释放一些资源。

  ---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!