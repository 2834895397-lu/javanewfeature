# java8新特性

## 一:lambda表达式 



# ![image-20210112155221566](img/image-20210112155221566.png)

![image-20210112155200563](img/image-20210112155200563.png)







## 二: stream流

![image-20210112204251501](img/image-20210112204251501.png)

![image-20210114113625638](img/image-20210114113625638.png)

![image-20210114113844001](img/image-20210114113844001.png)

![image-20210114163137613](img/image-20210114163137613.png)

![image-20210119093439921](img/image-20210119093439921.png)





## 时间和日期



![image-20210124112643938](img/image-20210124112643938.png)

![image-20210124121204221](img/image-20210124121204221.png)

![image-20210124123641371](img/image-20210124123641371.png)

```java
/**
 * 重复注解与类型注解
 *
 * 总结: 如何声明一个可重复注解(可重复标注在同一个类/方法/属性上)
 *      1. 声明一个可重复注解
 *          public @interface MyAnnotaion {...}
 *          
 *      2. 再声明一个注解容器, 在这个容器中声明一下这个可重复注解(以数组的形式声明)
 *          public @interface MyAnnotationCollector {
 *              MyAnnotaion[] value();
 *          }
 *
 *      3. 可重复注解上标注@Repeatable(注解容器.class)
 *          @Repeatable(MyAnnotationCollector.class)
 *          public @interface MyAnnotaion {...}

 *      4. 然后就可以使用这个可重复的注解了
 */
```



# java9新特性

 

## 模块化(提供导入导出的实现)

一个工程下面可以建立好多个module, 每一个module的scr下面建立一个module-info.java的文件, 里面包含了可以导入/导出哪个module的哪个包

1. 导出

```java
module moduleName{
    //导出的包名
    exports: com.atguigu.bean
}
```

2. 导入

```java
module moduleName{
    //导入的包名
    requires: com.atguigu.bean
}
```



## 钻石操作符

![image-20210124173844676](img/image-20210124173844676.png)



## 异常处理

```java
/**
 * java9的异常处理
 */
public class TestTyt {
    /**
     * 正常的异常处理
     */
    @Test
    public void test01() {
        InputStreamReader reader = new InputStreamReader(System.in);
        try {
            reader.read();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //要自己手动的关闭资源, 非常的麻烦
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * java8中的异常处理:
     * 要求资源对象的实例化必须在try的一对()内完成
     * 在try中的资源已经暗含了是final了, 不能再给这个资源赋值了
     * 多个资源用;隔开
     */
    @Test
    public void test02() {
        //这样就不用自己手动关闭资源了
        try (InputStreamReader reader = new InputStreamReader(System.in);
             OutputStreamWriter writer = new OutputStreamWriter(System.out)) {
            //Cannot assign a value to final variable 'reader'
            //reader = null;
            reader.read();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * java9中的异常处理:
     * 资源对象的实例可以不用在try的一对()内完成, 在try的()中传入资源的实例即可
     * 在try中的资源已经暗含了是final了, 不能再给这个资源赋值了
     * 多个资源用;隔开
     */
    @Test
    public void test03() {
        InputStreamReader reader = new InputStreamReader(System.in);
        OutputStreamWriter writer = new OutputStreamWriter(System.out);
        //这样就不用自己手动关闭资源了
        try (reader;writer) {
            reader.read();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```



## 有关String类的改动

String, StringBuffer, StringBuilder在java8及以前底层是用char[]来存储的, 在java9开始就换成了用byte[] (encoding flag来标注存储的是字符还是英文)来存储了, 这样就可以节省了大量的空间

> String: 不可变的字符串
>
> StringBuffer: 可变的字符串序列, 线程安全的, 效率低
>
> StringBuilder: 可变的字符串序列, 线程不安全的, 效率高(java5的时候新增)



## 只读集合

```java
  //创建一个只读集合
        List<String> readList = Collections.unmodifiableList(list);
        //试图在修改只读集合会出错
        readList.add("jianhong");
        readList.forEach(System.out::println);

```



## 创建只读集合类的几种方式

```java
public class OnlyReadColletion {
 
    @Test
    public void test01() {
        ArrayList<String> list = new ArrayList<>();
        list.add("jacklu");
        list.add("yangmin");
        list.add("zhouzhou");

        //创建一个只读集合
        List<String> readList = Collections.unmodifiableList(list);
        //试图在修改只读集合会出错
        //readList.add("jianhong");
        readList.forEach(System.out::println);
    }

    @Test
    public void test02() {
        //创建一个只读的set
        Set<Integer> set = Collections.unmodifiableSet(new HashSet<>(Arrays.asList(1, 2, 3, 4, 5)));
        //也是不能够修改只读集合的
        //set.add(520);
        set.forEach(System.out::println);

        //创建一个只读的map
        Map<String, Integer> map = Collections.unmodifiableMap(new HashMap<>() {
            //用代码块给实现类添加数据
            {
                put("yangmin", 21);
                put("jacklu", 23);
            }
        });
        //不可修改只读的map
        //map.put("jianhong", 22);
        map.forEach((k, v) -> {
            System.out.println(k + "--->" + v);
        });
    }

    /**
     * java9中创建一个只读的集合
     */
    @Test
    public void test03(){
        //通过of创建一个只读集合(通过接口中的静态方法来创建,Set和Map的创建也一样, 可以通过of方法来创建)
        List<Integer> integers = List.of(1, 2, 3, 4, 5);
        //integers.add(6);
        System.out.println(integers);

        Map<String, Integer> map = Map.of("jacklu", 22, "yangmin", 20);
        System.out.println(map);//{yangmin=20, jacklu=22}

        //创建只读的map的另一种方式
        Map<String, Integer> entries = Map.ofEntries(Map.entry("jacklu", 22), Map.entry("yangmin", 20));
        System.out.println(entries);
    }
}
```



# StreamApi

![image-20210124212741122](img/image-20210124212741122.png)

 ![image-20210124212942135](img/image-20210124212942135.png)



### streamApi演示

```java
public class StreamTest {
    /**
     * java9中新增了4个方法:
     * takeWhile(): 从前往后拿按顺序拿, 直到不符合条件就停止, 这点与filter不同, filter是不会停止, filter是全部遍历的
     */

    /**
     * takeWhile(): 不符合条件就停止遍历收集
     */
    @Test
    public void test01() {
        Arrays.asList(12, 342, 55, 63, 999, 2, 56, 75, 1, 23, 44).stream()
                .takeWhile(x -> x < 999).forEach(System.out::println);
    }

    /**
     * dropWhile(): 与takeWhile相反, 不符合条件就停止drop
     */
    @Test
    public void test02() {
        Arrays.asList(12, 342, 55, 63, 999, 2, 56, 75, 1, 23, 44).stream()
                //>=99就停止drop
                .dropWhile(x -> x < 999).forEach(System.out::println);
    }

    /**
     * ofNullable(): 允许我们创建一个空元素的stream, 可以包含一个非空元素, 也可以创建一个空stream
     */
    @Test
    public void test03() {
        //当流中只有一个元素时, 这个元素不能为null
        //Stream.of(null).forEach(System.out::println);

        //使用java9中的ofNullable方法, 当流中只有一个元素时, 这个元素可以为Null
        Stream.ofNullable(null).forEach(System.out::println);
    }

    /**
     * iterator(): 三个参数, 中间的参数是迭代条件
     */
    @Test
    public void test04() {
        /**
         * 复习: Stream的实例化
         *      1. 通过集合类的stream()
         *      2. 通过数组工具类Arrays.stream()
         *      3. Stream中的静态方法Stream.of()或者Stream.ofNullable()
         *      4. 通过迭代器Stream.iterate()
         */

        Stream.iterate(0, x -> x + 1).limit(10).forEach(System.out::println);

        //跟上面一样的功能, 中间的参数是迭代条件
        Stream.iterate(0, x -> x < 10, x -> x + 1).forEach(System.out::println);
    }
}
```



### optionalApi演示

```java
/**
 * Optional中提供了转换为Stream的方法stream()
 */
public class OptionalTest {
    @Test
    public void test01(){
        List<String> list = new ArrayList<>();
        list.add("jacklu");
        list.add("yangmin");
        list.add("zhouzhou");
        Stream<List<String>> stream = Optional.of(list).stream();
        System.out.println(stream.count());//1

        //合并流里面的元素
        List<String> list1 = new ArrayList<>();
        list1.add("jacklu");
        list1.add("yangmin");
        list1.add("zhouzhou");
        //合并流
        Stream<String> stringStream = Optional.of(list1).stream().flatMap(x -> x.stream());
        System.out.println(stringStream.count());//3
    }
}
```

