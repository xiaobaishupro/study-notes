## Stream流式编程(学习)

#### 什么是Stream

- Stream 是 Java8 添加的一个新的抽象称为流，可以让你以一种声明的方式处理数据。
- 流式编程是一种处理数据的风格，这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。
- 元素流在管道中经过**中间操作**（intermediate operation）的处理，最后由**最终操作**(terminal operation)得到前面处理的结果。

#### Stream需要注意什么

1、Stream自己不会存储元素。

2、Stream不会改变源对象。相反，他们会返回一个持有结果的新Stream。

3、Stream操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

#### 操作Stream

**1、创建 Stream**

一个数据源（如：集合、数组），获取一个流

- 可以通过 Collection 系列集合提供的 stream() 【串型流】或 parallelStream() 【并行流】方法

```java
ArrayList<Object> list1 = new ArrayList<>();
Stream<Object> stream1 = list1.stream();
```

- 通过 Arrays 中的静态方法 stream() 获取数组流

  ```java
  int[] arr = new int[10];
  IntStream stream2 = Arrays.stream(arr);
  ```

- 通过 Stream 类中的静态方法 of()

  ```java
  Stream<Serializable> stream3 = Stream.of("aa", "bb");
  ```

- 静态的Stream.generate()方法生成无限流，接受一个不包含引元的函数

  ```java
  Stream<Double> stream5 = Stream.generate(() -> Math.random());
  ```

- 静态的Stream.iterate()方法生成无限流，接受一个种子值以及一个迭代函数

  ```java
  Stream<Integer> stream4 = Stream.iterate(0, (x) -> x - 2);
  ```

**2、中间操作**

一个中间操作链，对数据源的数据进行处理

- **filter(Predicate predicate)**：过滤Stream中所有不符合predicate的元素。
- **limit(long maxSize)**：该方法用于保证对该流的后续访问中最大允许访问的元素个数，这是一个有状态的，短路方法。
- **skip(n)**：跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。
- **distinct**：通过流所生成元素的 hashCode() 和 equals() 去除重复元素

**映射：**

- **map**：接收Lambda，将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。
- **flatMap**：接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流

**排序：**

- **sorted()**：自然排序（Comparable）
- **sorted(Comparator com)**：定制排序（Comparator）

**3、最终操作**

一个终止操作，执行中间操作链，并产生结果

**查找与匹配：**

- allMatch：检查是否匹配所有元素
- anyMatch：检查是否至少匹配一个元素
- noneMatch：检查是否没有匹配的元素
- findFirst：返回流中的第一个元素
- findAny：返回流中任意一个元素
- max：返回流中的最大值
- min：返回流中的最小值
- count() :返回流中元素的数量。
- **forEach(Consumer action)：**遍历流中所有元素，对每个元素执行action。

**归约：**

- reduce(T identity，BinaryOperator) / reduce(BinaryOperator)：可以将流中元素反复结合起来，得到一个值。

**收集：**

- collect—将流转换为其他形式。接收一个Collector接口的实现，用于给Stream中元素做汇总的方法

#### 代码示例：

```java
public class Test {
    public static void main(String[] args) {
        //创建6个用户
        User user1 = new User(1,"a",21);
        User user2 = new User(2,"b",22);
        User user3 = new User(3,"c",23);
        User user4 = new User(4,"d",24);
        User user5 = new User(5,"e",25);
        User user6 = new User(6,"f",26);

        List<User> list = Arrays.asList(user1,user2,user3,user4,user5,user6);
        /**
         * 要求：从上面用户中，筛选出符合下面五个条件的用户，用一行代码实现
         * 1、用户id为偶数
         * 2、年龄必须大于23岁
         * 3、用户名转换为大写
         * 4、用户名字母倒着排序
         * 5、只输出一个用户
         */
        //Collection接口的stream()方法生成流
        list.stream()
                //中间操作
                .filter((user) -> {return user.getId() % 2 == 0;})
                .filter((user) -> {return user.getAge() > 23;}) //将结果为false的元素过滤掉
                .map((user) -> {return user.getName().toUpperCase();})//转换元素的值
                .sorted((u1,u2)->{return u2.compareTo(u1);})//将流元素按传入的条件排序
                .limit(1)//保留第一个元素
                .forEach(System.out :: println);//最终操作
    }

}
```