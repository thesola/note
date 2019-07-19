# Java基础知识梳理

![Java知识点](D:\资料\A工作\笔记\Java要求.png "知识点")

## 导论

* 计算机是头脑的自行车（便捷的工具）。
* **对象**具有状态、行为和标识。
* 任何程序都是你所设计的系统的一种仿真。
* 只有类名和公共方法被示于UML设计图中。
* Java还有一种默认的访问权限，**包访问权限**。
* **静态导入(import static)**用于导入类的静态成员，如: ```import static com.yf.util.Print.*;```
* 使用现有的类合成新的类称为**组合**，如果组合是动态发生的则称为**聚合**。
*　在建立新的类时，应该首先考虑组合。
*　**OOP**中，程序直到运行时才能够确定代码的地址。
*　在Java中，**动态绑定**(virtual)是默认行为。
*　将导出类看作基类称为**向上转型**，反之称为**向下转型**，来源于继承图的经典布局方式：父类在上、子类在下。
*　事实上所有C++以外的OOP语言都是**单根继承结构**，Java中这个终极基类叫Object。
*　持有其他对象的引用的对象，被称为**容器**。
*　容器知道自己保存的对象的类型被称为**参数化类型机制**，在Java中亦称为**范型**（在C++中就是模板）。
*　Java的**垃圾回收器**不包括清理对象的其他方面。
*　**异常处理**在面向对象语言出现之前就已经存在了。
*　**并发**的隐患：共享资源。
*　**插件**提供了一个“后门”，使得可以创建新的客户端编程语言。
*　解释**脚本语言**的插件在HTML页面被显示时自动激活。
*　**applet**是只在Web浏览器中运行的小程序，作为网页的一部分而自动下载。
*　**servlet**及其衍生物**JSP**是许多开发网站的公司迁移到Java上的两个主要原因。
*　Java中**没有goto**，但是可以使用**标签+break/continue**实现循环中的跳转:

```java
boolean res = true;
outer: // 标签
while(true){
    inner: // 标签
    while(true){ // continue inner; 后到这里
        if(res){
            res = !res;
            continue inner; // 继续指定标签的循环
        }
        break outer; // 跳出指定标签的循环
    }
}
// break outer;后到这里
```



## 运算符

* =、==、!=这三个运算符适用于所有对象。

* +、+=适用于String。

* 其他运算符只能操作基本数据类型。

* **对象赋值**实际是对引用的复制，亦称为**别名**。

* ==、!=比较的只是对象的引用是否相同，判断对象值是否相同必须得使用```Object.equals()```方法。

* ```Object.equals()```方法默认比较引用，因此自定义的类应根据需要覆写该方法。

* > 与C及C++不同的是：不可将一个非布尔值当作布尔值在逻辑表达式中使用。

* 使用```Integer.toBinaryString()```方法可以将整型转为二进制显示。

* 编译器默认将指数视为double，因此float常数需指明后缀```float f4 = 1e-43f; // 10的幂```。

* 可以对布尔类型进行*移位*和*~*以外的位运算。

* > Java中增加了一种”无符号“右移位操作符（>>>），它使用“零扩展”：无论正负，都在高位插入0。

* char、byte、short在进行移位时将被转为int，并且所得结果也是int，并且只有数值右端的低五位可用，以防止移位超过int所具有的位数(32)。```a>>>b; // b的右端低五位有效，其他无效```。

* 基于上一条，使用>>=、<<=时，由于运算的结果首先为int然后截取为char、byte、short，因此得到的可能不是正确的结果。

* 条件表达式依旧如故：```boolean-exp ? value1 : value2```

* **Java没有sizeof**



## 初始化

> 由于构造器的名称必须与类名完全相同，所以“每个方法首字母小写”的编码风格并不适用于构造器。

> 甚至参数顺序的不同也足以区分两个方法。

* 构造器是特殊的方法，因此无法使用```ClassName.class.get[Declared]Methods()```方法获得。

* 在构造器中调用构造器时，必须**将构造器调用置于最起始处**，否则编译器会报错。
* **static方法就是没有this的方法**，因此不能在static方法中调用成员方法。
* 可以在定义类成员变量的地方为其赋初值， 初值可以是常量、new对象、函数返回值。

```java
public class MethodInit{
    int a = 1; // 常量
    List<String> strList = new ArrayList<>(); // new对象
    int i = f(); // 函数返回值
    int j = g(i); // 带参函数返回值，必须先有i
    int f() { return 1; }
    int g(int arg) { return arg*arg; }
}
```

- 自动初始化发生在构造器调用之前。
- 变量定义的先后顺序决定了初始化的顺序。
- 静态对象只有在类的第一个对象创建时才会被创建并初始化，并且此后不再初始化。
- 静态对象的初始化发生在非静态对象之前。
- 构造器实际上是静态方法。
- 静态块->实例块->定义值->构造器

```java
public class MethodInit{
    static int v0;
    int v1;
    static { // 只在首次生成类对象或访问静态成员时执行
        v0 = 3; // 首先是静态块
    }
    { // 每次生成类对象时执行
        v1 = v0 + 1; // 然后是实例块
    }
    int v2 = v1 + 1; // 再然后是定义值
    int v3;
    MethodInit(){
        v3 = v2 + 1; // 最后是构造器
    }
}
```

- 可以将枚举视为一个类，也可以作为switch的选项值。

* ```EnumName.Values()```以获取枚举值数组，```enumVar.ordinal()```以获取枚举值的声明顺序。

```java
public enum OrderStatusEnum { // 定义枚举类型
    NEW(0, "新订单"),	// 定义枚举常量
    FINISHED(1, "完结"),
    CANCEL(2, "已取消")
    ;

    private Integer code; // 像类一样使用枚举
    private String message;

    OrderStatusEnum(Integer code, String message) { // 构造方法不公有
        this.code = code;
        this.message = message;
    }

    public static void main(String[] args) {
        OrderStatusEnum orderStatusEnum = OrderStatusEnum.FINISHED; // 获取枚举值
        System.out.println(orderStatusEnum.ordinal()); // 获取枚举值的顺序
        System.out.println(OrderStatusEnum.values().length); // 获取枚举值数组
        switch (OrderStatusEnum.FINISHED){ // 在switch中使用枚举值
            case NEW:
                System.out.println("new");
                break;
            case CANCEL:
                System.out.println("Cancel");
                break;
            case FINISHED:
                System.out.println("Finished");
                break;
        }
    }
}
```



## 资源清除

* Java允许在类中定义**finalize()**方法，并在**垃圾回收**发生时调用。因此不能依赖finalize()方法，而应该在不需要时手动释放资源——Java的finalize()方法不如C++的析构方法便利。

> 之所以要有finalize()方法，是由于在分配内存时可能采用了类似C语言中的做法，这种情况主要发生在使用“本地方法”的情况下，本地方法是一种在Java中调用非Java代码的方式（主要用于调用C/C++代码）。

>  由于垃圾回收器的存在，Java从堆分配空间的速度可以和其他语言从堆栈上分配空间的速度相媲美。

* 垃圾回收器工作时，一面回收空间，一面将堆中的对象紧凑排列。
* 引用计数将导致循环引用问题，因此常用来说明垃圾回收器的工作方式，但并未被实际的JVM实现所采用。
* **停止-复制（stop-and-copy）**技术将暂停程序，从堆栈和静态存储区出发遍历所有的引用，将所有存活对象从当前堆复制到另一个堆，没有被复制的全部是垃圾。
* **标记-清扫（mark-and-sweep）**技术将暂停程序，从堆栈和静态存储区出发遍历所有的引用，标记所有存活对象，未被标记都是垃圾。
* **即时（Just-In-Time，JIT）编译器**技术将程序全部或部分翻译为本地机器码以提升程序的运行速度——代码执行的次数越多所获得的优化越多，执行得越快。



## 数组

- C风格的数组初始化只能出现在数组定义处：```int[] arr = {1,2,3,}```，末尾的逗号是可选的。
- 常规的数组赋值形式为```int[] arr = new int[20]```。
- 可变参数列表实际上被包装成数组传递给函数。

```java
public class Clazz{
    public static void f(int a, int... args){ // 可变参数列表
        System.out.println(args.length); // 当作数组使用
        for (int arg: args) {
            System.out.println(arg);
        }
    }
    public static void main(String[] args) {
        f(1, 2, 3, 4); // 2,3,4将被包装成数组传递给函数
    }
}
```



## 继承

* Java中Object为根类，因此即使类定义时未显式继承，其实际上也隐式继承了Object类。
* Java中只有单继承，但可以实现任意数量的接口。
* Java中**成员的默认访问权限为包访问权限**，C++中class默认访问为private、struct默认为public。
* 可以为每个类编写一个main方法，即使类仅有包访问权限，其main方法也是可以访问的。
* 使用关键字**super**以访问父类成员。
* 父类构造->子类构造，与C++中顺序无异。
* 使用**@Override**可以避免想要覆写但又不小心重载了的情况。



## final

* final修饰的基础类型数据为常量。
* final修饰的引用不可改变，但引用所指向的对象可以修改。
* final在第一次使用前有初始化即可。

```java
public class myClazz {
    final myClazz classVar; // 固定引用
    private final int i = 10; // 常量
    private int j = 10;
    public myClazz() {
        classVar = new myClazz();
        classVar.j = 11; // 可以修改final引用指向的对象的值
    }
    public myClazz(String str){
        classVar = new myClazz(str);       
    }
}
```

* final修饰的方法不可被覆写，所有private隐式地指定为final。
* final修饰的类不可被继承，所有final类成员隐式地指定为了final。
* 使用final可以关闭默认的动态绑定，使得编译器生成更有效的代码，但大多情况下这种方式并不能带来显著的效益，因此在编写程序时还是该更多地基于设计考虑而非性能考虑。



## 抽象类

* 含有抽象方法的类必须限定为抽象类。
* 抽象类中可以有普通方法和字段。

```java
public abstract class AbstractClassDemo { // 抽象类
    abstract void fun1(); // 抽象方法

    String fun2(){ return "string"; } // 普通方法
    int member = 1; // 普通字段
}
```



## 接口

* 接口本身默认为包访问权限。
* 接口内的字段为隐式```static final```。
* 接口内的方法为隐式```public```，因此在实现类中须将方法指明为```public```，否则由于类的默认访问权限为包访问权限而产生降级问题。
* 可以嵌套定义接口，但不需要实现嵌套的接口。

```java
public class InterfaceDemo  implements A {
    @Override
    public void method() {
        // 不需要实现嵌套的接口
    }
}

interface A {
    interface InnerA{
        void innerMethod();
    }

    void method();
}
```



## 内部类





## 容器

* Vector
* Stack
* Hashtable
* Collection
* List
* Set
* Map

## 异常

## I/O

## 反射

## 克隆

## 线程

## 锁

## 网络编程

## JDBC

## 排序



