### Stream流式编程(实用)

#### 1.generate()生成流数据

```java
Stream<Integer> stream = Stream.generate(() -> new Random().nextInt(10));
stream.forEach(e -> System.out.println(e)); 
```

2.