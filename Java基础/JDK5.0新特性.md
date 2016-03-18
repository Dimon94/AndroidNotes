###��̬����
  - JDK 1.5 ���ӵľ�̬�����﷨���ڵ������ĳ����̬���Ի򷽷���ʹ�þ�̬������Լ򻯳�����ྲ̬���Ժͷ����ĵ��á�
  - Import static ����.����.��̬����|��̬����|*

###�Զ�װ��/����
  - �Զ�װ�䣺ָ������Ա���԰�һ��������������ֱ�Ӹ�����Ӧ�İ�װ�ࡣ
  - �Զ����䣺ָ������Ա���԰�һ����װ�����ֱ�Ӹ�����Ӧ�Ļ����������͡�
  Integer i = 1; //װ��
  int j = i;//����

###��ǿforѭ��
  - ������ǿforѭ����ԭ����JDK5��ǰ�İ汾�У���������򼯺��е�Ԫ�أ����Ȼ������ĳ��Ȼ򼯺ϵĵ��������Ƚ��鷳��
  - ���JDK5�ж�����һ���µ��﷨������ǿforѭ�����Լ򻯴����������ǿforѭ��ֻ���������顢��ʵ��Iterator�ӿڵļ�������
  - �﷨��ʽ��  for(�������ͱ��� �������������򼯺�){}


```java
/*
   * ��ǿforѭ��
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

    // ��ͳ����1
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

    // ��ͳ����2
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

    // ��ǿforȡmap�ĵ�һ�ַ�ʽ
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

    // ��ǿforȡmap�ĵ�2�ַ�ʽ
    for(Object obj : map.entrySet()){
      Map.Entry entry = (Entry) obj;
      String key = (String) entry.getKey();
      String value = (String) entry.getValue();
      System.err.println(key + "=" + value);
    }
  }

  //ʹ����ǿforѭ����Ҫע��ļ������⣺��ǿforֻ�ʺ�ȡ����
  //���Ҫ�޸�������߼����е����ݣ�Ҫ�ô�ͳ��ʽ
```

###�ɱ����(�൱�ڶ�̬������)
����JDK�о��пɱ��������Arrays.asList()�������ֱ𴫶�������������飬�������ִ��ε������
ע�⣺�����������������������⡣
��JDK 5��ʼ, Java ����Ϊ�������峤�ȿɱ�Ĳ������﷨��   public void foo(int �� args){   } ע�����
- ���ÿɱ�����ķ���ʱ, ���������Զ�����һ�����鱣�洫�ݸ������Ŀɱ��������ˣ�����Ա�����ڷ����������������ʽ���ʿɱ����
- �ɱ����ֻ�ܴ��ڲ����б�����, ����һ���������ֻ����һ�����ȿɱ�Ĳ���

###ö����

1. ö����Ҳ��һ��������ʽ��Java�ࡣ
2. ö������������ÿһ��ö��ֵ����ö�����һ��ʵ������A��B��C��D
3. ��java�е���ͨ��һ����������ö����ʱ��Ҳ�����������ԡ������͹��캯������ö����Ĺ��캯������Ϊ˽�еģ�private��㲻����⣩��Ϊʲô����ֹ�������ʼ������
4. ö����Ҳ����ʵ�ֽӿڡ���̳г����ࡣ
5. JDK5����չ��swith��䣬�����˿��Խ���int, byte, char, short�⣬�����Խ���һ��ö�����͡�
6. ��ö����ֻ��һ��ö��ֵ������Ե�����̬���ģʽʹ�á�
7. Java��������ö���࣬����java.lang.Enum��ĺ��ӣ����̳���Enum������з�����

���÷�����

- name()
- ordinal()//����ö�ٳ���������������ö�������е�λ�ã����г�ʼ��������Ϊ�㣩��
- valueof(Class enumClass, String name) //���ش�ָ�����Ƶ�ָ��ö�����͵�ö�ٳ�����
- //String str=��B��;Grade g=Grade.valueOf(Grade.class,str);
- values() �˷�����Ȼ��JDK�ĵ��в��Ҳ�������ÿ��ö���඼���и÷�����������ö���������ö��ֵ�ǳ����㡣

��ϰ�����дһ���������ڼ���ö��WeekDay��Ҫ�� ö��ֵ��Mon��Tue ��Wed ��Thu ��Fri ��Sat ��Sun  ��ö��Ҫ��һ�����������ø÷����������ĸ�ʽ�����ڡ�
