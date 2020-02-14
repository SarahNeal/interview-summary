## Java面试题总结

**Java基础**

1. **JAVA中的几种基本数据类型是什么，各自占用多少字节。**

8种基本数据类型，4个整型，两个浮点类型，一个字符类型，一个布尔类型；

整型：

byte：一个字节，8位；

short： 2个字节，16位；

int： 4个字节，32位；

long： 8个字节，64位；

浮点类型：

float： 4个字节，32位；

double： 8个字节，64位；

char： 2个字节，16位；boolean；

2. **String类能被继承吗？为什么？**

不能，String类是final类型的不能被继承；String类被设计为Immutable，同样的还有Integer和Long等；

3. **String， StringBuffer ，StringBuilder的区别。**

String类是不可变的，StringBuffer提供可变的字符串拼接，StringBuilder和StringBuffer功能类似；

StringBuffer是线程安全的类，其append方法用synchronized修饰，并且维护toStringCache字段保存每次toString时char数组中的值；

StringBuilder是线程不安全的类；

字符串的拼接相当于StringBuffer的append；

4. **ArrayList和LinkedList有什么区别。**

ArrayList是基于数组实现的，基于下标访问，随机访问的复杂度是O(1);

LinkedList是基于双向链表实现的，每个节点都维护一个prev和next字段指向前后节点，随机访问复杂度是O(n)；

在插入数据时，ArrayList需要检查数组容量，必要时进行扩容（原来容积的1/2，最大为Integer.MAX_VALUE-8）;

另外插入时需要进行数组copy；LinkedList直接修改前后节点的指针即可；

（推荐使用Array List，James Gosling在Stack Overflow提到自己很少用LinkedList >_<）

5. **讲讲类的实例化顺序，比如父类静态数据，构造函数，字段，子类静态数据，构造函数，字
   段，当new的时候，他们的执行顺序。**

当new一个类时，初始化顺序为：静态变量>静态代码块>普通变量；

同时，在初始化一个类时，如果父类没有初始化，优先初始化父类；

静态代码块及静态变量只初始化一次；

6. **用过哪些Map类，都有什么区别，HashMap是线程安全的吗,并发下使用的Map是什么，他们
   内部原理分别是什么，比如存储方式，hashcode，扩容，默认容量等。**

HashMap, HashTable, LinkedHashMap, TreeMap, ConcurrentHashMap等；

区别：HashMap多线程下不保证线程不安全，并且key和value允许使用null；LinkedHashMap继承了HashMap，在HashMap基础上维护了一个双向链表保存HashMap的Node节点顺序。

ConcurrentHashMap和HashTable可以保证多线程环境下线程安全；区别是实现方式不同，HashTable是使用synchronized锁定整个hash表来实现读，写同步，ConcurrentHashMap是基于synchronized和cas实现同步（1.8），且锁粒度上要更细，只锁定头节点，并且读操作没有锁定；

7. **JAVA8的ConcurrentHashMap为什么放弃了分段锁，有什么问题吗，如果你来设计，你如何
   设计。**

使用了CAS+synchronized实现同步，由于使用了数组+链表/红黑树的数据结构，在锁定粒度上能够更细化，只要锁定链表头节点/红黑书根节点就可以；另外synchronized在JVM层面在不断优化，使用synchronized可以保持优化能够同步可控；

8. **有没有有顺序的Map实现类，如果有，他们是怎么保证有序的。**

TreeMap和LinkedHashMap是可以保证有序的Map实现类；区别是TreeMap实现了SortedMap，可以根据key值进行排序，默认是按key升序排列；

LinkedHashMap维护了一个双向链表，可以根据数据的插入和获取进行排序（根据accessOrder的值判断，默认为false），最近被访问/操作的数据放在链表尾部；

9. **抽象类和接口的区别，类可以继承多个类么，接口可以继承多个接口么,类可以实现多个接口
   么。**

1）抽象类和接口都不能直接实例化，必须通过实现了所有接口方法/抽象方法的实现类类进行实例化；

2）抽象类是继承，接口是实现；Java允许单继承，多实现；

3）抽象类可以有构造函数，抽象方法只声明不实现；接口不能有构造函数，可以借助default关键字默认实现方法；

10. **继承和聚合的区别在哪。**

继承通过extends关键字，继承另一个类的功能，可以添加相应扩展；类与类，接口与接口之间可以继承；

聚合是两个独立的类之间的整体与部分的关系，即has-a关系；

继承的特点是子类自动继承父类接口，缺点是子类与父类耦合性较强，破坏了封装性；

聚合正好相反；

11. **IO模型有哪些，讲讲你理解的nio ，他和bio，aio的区别是啥，谈谈reactor模型。**

常见的IO模型有：

blocking(阻塞IO)；non-blocking(非阻塞IO)；asynchronous(异步IO)；multiplexing(多路复用IO)；

一般的IO请求会经过几个阶段：

- 第一阶段为准备数据，把数据copy到内核缓冲空间
- 第二阶段为把内核空间的数据copy到应用进程的缓冲空间；

**bio**是在数据准备的两个阶段都阻塞进程，直到内核完成copy数据到应用进程缓冲空间并返回结果后才唤起进程进行相应操作；

**多路复用io**又叫信号驱动io，是基于select，poll，epoll实现的。当用户进程调用了select函数，整个进程就会被阻塞，同时内核进程会轮询监视所有调用了select的进程socket，当任何一个socket准备好数据了，select就会返回，同时用户进程再调用read请求从内核copy数据到用户进程空间，之后继续阻塞，直到copy数据结束并返回，用户进程接下来执行相应的操作；

  多路复用io会阻塞两次，copy数据到内核缓冲区，内核copy数据到应用进程缓冲区都会阻塞，好处是一个进程可以同时处理多个网络连接的请求；

**nio** 是指用户进程调用read请求数据，如果数据还没有准备好，会马上返回一个error，用户轮询请求，直到得到数据准备完成的返回结果，和多路复用io不同的是由用户进程负责轮询；

**aio** 是指用户进程发起read请求数据后，会立即得到返回，之后用户进程继续执行其他操作不必等到数据完全准备好，当数据copy完成是，会收到一个signal信号，之后再进行数据相关的操作；

12. **反射的原理，反射创建类实例的三种方式是什么。**

反射简单来说就是在运行时获取类的属性和方法并进行赋值或调用；

一般来说想要使用一个类，需要先进行初始化，即

```
A a = new A();
a.methodA();
```

要使用一个类，就需要获取该类的Class对象；

通过反射有三种获取方式：

``` 
//1,通过Class.forName("类全限定名")获取
Class c1 = Class.forName("com.xxx.A");
//2.通过.class获取
Class c2 = A.clas;
//3.通过类对象.getClass()获取
A a = new A();
Class c3 = a.getClass();

//通过类对象操作类属性或方法有两种方式
//1.通过newInstance()方法直接实例化
Class clazz = Class.forName("com.xxx.A");
A a = (A)clazz.newInstance();
//2.通过构造函数实例化
Constructor cons = clazz.getConstructor();
A a = (A)cons.newInstance();

//获取类的属性及方法
Method[] declared = clazz.getDeclaredMethods();		//获取自定义方法

```

拿到了类对象，就可以调用类的方法或操作属性值；

13. **反射中，Class.forName和ClassLoader区别 。**

Class.forName()和ClassLoader都会将.class文件加载到jvm中，区别是前者加载后还会对类进行解释，执行类的static代码块，后者只有在调用了newInstance()后才会执行static代码块；

14. **动态代理的几种实现方式，分别说出相应优缺点；**

动态代理：是使用反射和字节码的技术，在运行期创建指定接口或类的子类，以及其实例对象的技术，通过这个技术可以无侵入的为代码进行增强;

动态代理有JDK动态代理和CGLIB动态代理；

JDK动态代理：是使用反射实现，不需要引用第三方库，在运行时生成代理类，效率较高；缺点是只能针对接口(生成的代理类继承了Proxy，由于Java是单继承，所以只能针对接口进行代理)；

CGLIB：是基于ASM字节码生成库，通过继承的方式对字节码进行修改和动态生成，无论目标对象有没有实现接口都可以；缺点是无法处理final修饰的类；

通常来说，基于接口的一般使用JDK动态代理，基于类的使用CGLIB(类不能用final修饰)；

JDK动态代理基于反射，所以生成效率较快；cglib基于字节码技术，所以执行效率较快；

15. **动态代理与cglib实现的区别。**

区别同上；

16. **为什么CGlib方式可以对接口实现代理。**

CGLIB使用ASM字节码技术生成新的子类，基于继承；

17. **final的用途。**

final修饰的类不能够被继承，final修饰的方法不能被重写，final修饰的变量引用不可变；

18. **写出三种单例模式实现 。**

单线程下使用，懒加载：

```

/**
 * @date 2019/12/23 21:29
 * 
 * 线程不安全，懒加载
 */
public class LazySingleton {
    private static LazySingleton instance;

    private LazySingleton(){
    }

    public static LazySingleton getInstance(){
        if(instance==null){
            instance=new LazySingleton();
        }
        return instance;
    }
}

```

二、静态内部类形式：懒加载，线程安全

```
/**
 * @date 2019/12/23 21:37
 * 静态内部类模式，线程安全，懒加载（推荐）
 */
public class InnerClassSingleton {

    private InnerClassSingleton() {
    }

    public static InnerClassSingleton getInstance() {
        return SingletonHolder.INSTANCE;
    }

    private static class SingletonHolder {
        private static final InnerClassSingleton INSTANCE = new 	InnerClassSingleton();
    }
}

```

三、基于static关键字初始化，线程安全，非懒加载

```
/**
 * @date 2019/12/23 21:12
 * 基于static关键字初始化，线程安全，非懒加载
 */
public class Singleton {
    private static Singleton instance = new Singleton();

    private Singleton() {
        System.err.println("create a new instance.");
    }

    public static Singleton getInstance() {
        return instance;
    }
}
```

四、Double-Check-Locking 双重锁检查模式，线程安全，懒加载

```
/**
 * @author Created by neal.zhang
 * @date 2020/2/14 17:54
 * 双重锁检查，jdk1.5以上适用，基于synchronized和volatile
 * 线程安全，懒加载，有人认为anti-pattern
 */
public class DoubleCheckSingleton {

    private static volatile DoubleCheckSingleton instance;

    private DoubleCheckSingleton() {
    }

    public static DoubleCheckSingleton getInstance() {
        if (instance == null) {
            synchronized (DoubleCheckSingleton.class) {
                if (instance == null) {
                    instance = new DoubleCheckSingleton();
                }
            }

        }
        return instance;
    }
}
```

五、枚举，天然immutable，天然多线程安全；

其他的各种不推荐；

19. **如何在父类中为子类自动完成所有的hashcode和equals实现？这么做有何优劣。**

子类不实现，默认调用父类方法；

缺点是不精确，有需要比较子类的场景则不适用；

20. **请结合OO设计理念，谈谈访问修饰符public、private、protected、default在应用设
    计中的作用。**

* `public` 所有类都可以访问
* `protected` 子类可以访问
* `private` 只有自己可以访问
* `default` 默认访问模式，同一包下可以访问

21. **深拷贝和浅拷贝区别。**



10. 数组和链表数据结构描述，各自的时间复杂度。
11. error和exception的区别，CheckedException，RuntimeException的区别。
12. 请列出5个运行时异常。
13. 在自己的代码中，如果创建一个java.lang.String类，这个类是否可以被类加载器加
    载？为什么。
14. 说一说你对java.lang.Object对象中hashCode和equals方法的理解。在什么场景下需
    要重新实现这两个方法。
15. 在jdk1.5中，引入了泛型，泛型的存在是用来解决什么问题。
16. 这样的a.hashcode() 有什么用，与a.equals(b)有什么关系。
17. 有没有可能2个不相等的对象有相同的hashcode。
18. Java中的HashSet内部是如何工作的。
19. 什么是序列化，怎么序列化，为什么序列化，反序列化会遇到什么问题，如何解决。
20. java8的新特性。

**JVM知识**

1. 什么情况下会发生栈内存溢出。
2. JVM的内存结构，Eden和Survivor比例。
3. JVM内存为什么要分成新生代，老年代，持久代。新生代中为什么要分为Eden和Survivor。
4. JVM中一次完整的GC流程是怎样的，对象如何晋升到老年代，说说你知道的几种主要的JVM参
   数。
5. 你知道哪几种垃圾收集器，各自的优缺点，重点讲下cms和G1，包括原理，流程，优缺点。
6. 垃圾回收算法的实现原理。
7. 当出现了内存溢出，你怎么排错。
8. JVM内存模型的相关知识了解多少，比如重排序，内存屏障，happen-before，主内存，工作
   内存等。
9. 简单说说你了解的类加载器，可以打破双亲委派么，怎么打破。
10. 讲讲JAVA的反射机制。
11. 你们线上应用的JVM参数有哪些。
12. g1和cms区别,吞吐量优先和响应优先的垃圾收集器选择。
13. 怎么打出线程栈信息。
14. 请解释如下jvm参数的含义：
    -server -Xms512m -Xmx512m -Xss1024K
    -XX:PermSize=256m -XX:MaxPermSize=512m -
    XX:MaxTenuringThreshold=20XX:CMSInitiatingOccupancyFraction=80 -
    XX:+UseCMSInitiatingOccupancyOnly。

