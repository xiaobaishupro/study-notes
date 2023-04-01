## string的理解

#### 1.字符串创建对象

String s = new String(“xyz”);创建了几个String Object?
两个。第一个对象是字符串常量"xyz" 第二个对象是new String()的时候产生的，在堆中分配内存给这个对象，只不过这个对象的内容是指向字符串常量"xyz" 另外还有一个引用s，指向第二个对象。这是一个变量，在栈中分配内存。

变形①：String s = "xyz"创建了几个String对象？
首先看常量池里有没有"xyz"，如果有直接引用，如果没有则创建再引用，这里"xyz"本身就是pool中的一个对象，而在运行时执行new String()时，将pool中的对象复制一份放到heap中，并且把heap中的这个对象的引用交给s持有。ok，这条语句就创建了2个String对象。

变形②：String str = “aaa” + new String(“bbb”)创建了几个String对象？
四个， "aa"一个对象 new Sring()一个对象 "bbb"一个对象 “aa” + new String(“bbb”);一个对象

String str = "abc";//在常量池中创建abc
String str1 = "abcd";//在常量池中创建abcd
String str2 = str+"d";//拼接字符串，此时会在堆中新建一个abcd的对象，因为str2编译之前是未知的
String str3 = "abc"+"d";//拼接之后str3还是abcd，所以还是会指向字符串常量池的内存地址
System.out.println(str1==str2);//false
System.out.println(str1==str3);//true