# JavaSE

- library & framework
  - 开发者的代码遵循规定以调用library
  - framework按约定调用开发者的代码
  - Inversion of Control：your code calls a library but a framework calls your code
- 按自然语言的语义进行编程
## 数据类型
- 变量
  - 变量=基本数据类型变量+引用类型变量
  - 变量的生命周期由开发者决定
  - 变量类型决定存储变量的值需要多少内存单元
    - 无论引用的类型是类、数组、匿名类还是接口，引用所需要的内存单元数固定，由JVM决定
  - 基本数据类型变量标识的内存单元存储基本数据类型的值
  - 引用变量标识的内存单元存储引用类型的值
    - 引用的值标识对象实例、数组的位置
    - 更改引用的值将使引用指向不同的对象实例、数组
    - 引用的值只能通过赋值更改
    - 规定：引用用于计算时，编译器报错
- 声明与初始化
  - 声明变量的类型，将指定数量的内存单元标识为变量名
  - 初始化：在变量名标识的内存空间中存储二进制数
  - 基本数据类型变量所存储的值完全由开发者决定
  - 引用类型变量所存储的值，规定由垃圾回收器管理，供开发者使用
  - 规定：若开发者未在域变量声明时显示初始化，由JVM为其赋予初始值
    - number->0
    - boolean->false
    - reference->null
  - 规定：若开发者未显示初始化局部变量，编译器将不会将源代码编译为字节码；
- final
  - 规定：被final修饰的变量，一经初始化后就不可改变值
  - 约定：被final修饰的reference代表引用关系固定，该引用不应指向可变对象实例
  - 约定：被final修饰的基本数据类型变量的名字应全大写
- 变量的值
  - boolean：不能与其它类型发生类型转换；其值域为true、false，不能写入其它类型的值
  - 变量的值只能使用二进制序列表示，供计算机阅读；
    - 整型和浮点型的二进制序列表示方法不同
    - 整型采用二进制补码
    - 浮点数遵循<A HREF="https://zh.wikipedia.org/zh-hans/IEEE_754">IEEE-754规范</A>
    - java不支持无符号数
  - 变量的值可翻译为二进制0b、八进制0、十六进制0x、十进制供人阅读
    - java在此处采用的设计原则：数据的存储和显示分离
- 基本数据类型
  - ![](Photos/2023-11-28-12-36-23.png)
    - 一个小方块代表一个内存单元
    - 一个内存单元代表`1byte=8bit`，使用两个十六进制数表示
  - 约定开发者应总是使用int/double
    - 在整型变量的值可能截断时替换为long
  - 关于字面量的规定
    - 200->int，200L/l->long
    - 1.1F/f->单精度浮点数，1.1、1.1D/d->双精度浮点数
  - 类型转换
    - 规定不允许将内存单元更多的字面量、变量，赋值给使用内存单元更少的变量，该规定可以通过强制类型转换通过编译器检查
    - ![](Photos/2023-07-10-13-57-32.png)
    - 实线：允许隐式类型转换，无溢出风险
    - 虚线：只允许强制类型转换，有溢出/指数截断、尾数截断风险
      - int转float，float指数可偏移127位不存在指数截断的风险，但是尾数只有23位，而int最大数0x7FFF_FFFF，会发生尾数截断造成精度丢失
    - 浮点数转整型时，转科学计数法后直接抛弃小数部分，表现为向下取整；
      - ps：Math.round()工具类提供四舍五入

| 语句                               | 是否合法 | 是否类型转换 | 解释                                                                         |
| ---------------------------------- | -------- | ------------ | ------------------------------------------------------------------------------ |
| `long n = 0xF_FFFF_FFFF`            | 不合法   |            | 数字字面量超过int类型范围，编译器将报错                          |
| `long n = 0xF_FFFF_FFFFL`           | 合法     | 否           | 开发者初始化long变量时，应总是为字面量添加后缀`L/l`                                  |
| `short n = 0xFFFF_FFFF`             | 合法     | 是           | 发生溢出并被计算机截断，变量值为-1                                    |
|float f=3.14|非法||
int = (int)0xFl|合法|是

  
- IEEE-754
  - 浮点数二进制序列构成：符号位、指数、尾数
    - 双精度浮点数：符号位1位、指数8位、尾数23位
    - 单精度浮点数：符号位1位、指数11位、尾数52位
  - 浮点数二进制序列转浮点数科学计数法：
    - 假定采用小端法，尾数域的最左边权为$2^{-1}$，转为小数后记为$a$
    - 指数视为无符号二进制转整数记为$b$
    - $1+a$作为有效数字部分
    - 浮点数的科学计数法表示为$(1+a)E(b-127)$或$(1+a)E(b-1023)$
  - IEEE-754规范定义了特殊的变量值的二进制序列，及其翻译出来的记号

| 二进制序列                                   | 记号         | 中文意义        |
| -------------------------------------------- | ------------ | --------------- |
| `01111111100000000000000000000000`           | +∞           | 正无穷大         |
| `11111111100000000000000000000000`           | -∞           | 负无穷大         |
| `00000000000000000000000000000000`  | +0           | 正零             |
| `10000000000000000000000000000000`  | -0           | 负零             |
| `0111111111xxxxxxxxxxxxxxxxxxxxxx`           | NaN          | 非数值（Not a Number） |



- 运算
  - 关系运算：==、！=、>=等运算符返回boolean类型的字面量
  - 逻辑运算：&&、||采用短路方式从左往右运算
    - `x != 0 && 1/x > x+y`避免产生0除异常
    - && 的优先级高于 ||
  - 三元操作符`?:`
  - 位运算：以二进制位而不是内存单元作为原子单位进行运算
    - `&、|、^、~`
    - 位运算不采用短路
    - \>\>使用符号位填充高位，>>>使用0填充高位，<<使用0填充低位；
- char
  - char由两个字节存储，使用单引号标注char字面量
  - 编码方式决定存储文本文件的二进制序列
  - Unicode编码指UTF-16定长编码，英文字符的高字节总是00浪费空间，因此通常使用UTF-8变长编码
  - UTF-8共17个语言级别，将char分割为两个码点，通过判断码点的值范围，判断这个码点所属的编码属于哪个语言级别，然后读取1-4个字节转义为文本字符
  - UTF-8编码的容错能力强；传输时某些字符出错，不会影响后续字符
  - java内存使用UTF-8对字符串进行编码，将UTF-8编码的字符串转换为使用其它编码方式编码的二进制序列方法如下：

```JAVA
/**转换为系统默认编码后的二进制序列*/
byte[] b1 = "Hello".getBytes(); 

/**转换为UTF-8编码后的二进制序列*/
byte[] b2 = "Hello".getBytes("UTF-8"); 

/**转换为GBK编码后的二进制序列*/
byte[] b2 = "Hello".getBytes("GBK");

/**转换为UTF-8编码后的二进制序列*/
byte[] b3 = "Hello".getBytes(StandardCharsets.UTF_8); 

```
- 二进制序列按指定编码方法转文本，再转UTF-8编码的String
```JAVA
byte[] b = ...
String s1 = new String(b, "GBK");
String s2 = new String(b, StandardCharsets.UTF_8); 
```

## 面向对象编程

### 类

- 访问控制修饰Field、Method、Constructor
  - public：允许所有人访问
  - protected：仅允许同一个包中的类或子类访问
  - 无访问控制符：仅允许同一个包中的类访问
  - private：仅允许类内访问
  - 约定：职责决定范围
- 构造器
  - 当且仅当没有提供构造器时，编译器才会提供默认的无参构造器
  - 提供自定义构造器后，想调用无参构造器就必须由开发者自行声明
- 与访问控制符、static、final相关的约定：
  - 总是使用private修饰Field
    - 域的名字为$A$时，其更改器访问器方法名为`setA、getA`
  - 不被static修饰的Field标识对象状态，被static修饰的Field标识类状态
  - 当对象状态、类状态一经初始化就不再更改时，应使用final修饰
  - Method不直接或间接依赖对象状态时，应使用static修饰
  - 使用final修饰Method，表示该方法不能被子类重写
- 方法、构造器的参数
  - 参数传递总是进行值的复制
  - 方法、构造器的参数不为空时，代表该方法、构造器对外界具有依赖关系
  - 对非static的方法：传递隐式参数this和显式参数，隐式参数使用this表示，代表调用该方法的对象
  - 对static方法：只有显式参数
  - 对构造器
    - this(parameters)将调用本类的符合参数列表要求的其它构造器，与非static的隐式参数具有不同性质
    - super(parameters)将调用父类的符合参数列表要求的构造器
  - 可变参数列表
    - (int...args)
    - 在方法或构造器内部将args视为数组类型的引用变量进行调用
    - 若没有可变参数列表，就需要定义大量的重载方法，出现大量重复代码
- 局部变量
  - 局部变量的生效范围是整个代码块
  - 对该代码块中的子代码块，不允许定义同名局部变量
  - 局部变量更改值对局部变量代码块之外不可见
  - 局部引用变量更改对象状态后，局部变量代码块之外可见该对象状态的改变
- 方法重载
  - 方法使用方法签名=方法名+参数列表进行标识
    - 方法签名相同时，即使返回值类型不同，编译器也会报错
  - 约定：方法名相同的方法具有单一功能，处理不同的
- 初始化块
  - 对初始化块的数量和位置没有要求
  - 非static初始化块在对象实例化期间进行，每个对象实例化时都会执行一次初始化块
  - static初始化块只会执行一次，在类初次被加载时执行
- 执行顺序
  - 域声明语句，若域声明语句未初始化，则由JVM赋予默认值
  - 按从上到下的顺序依次执行初始化块
  - 按递归的形式执行构造器方法链
  - 约定：
    - 构造器的参数列表不应该依赖于初始化块的执行结果
    - 总是将域的初始化放置在构造器中，确保对象状态的一致性
- 访问器、更改器
  - 约定：即使是在类内部，也不应直接访问域，而是应该通过更改器、访问器方法间接访问，<A HREF="#动态代理">原因</A>
  - 域是布尔类型时，更改器和访问器不应该是get/set，应是is/set
  - 只有getter的域是只读域，只有setter的域是只写域
### 继承相关

- 规范
  - 引用类型是对象类型的父类或接口类型
- 引用、对象实例相关的约定
  - 判断是否为null$\ne$域是否为空
  - 通过引用调用方法前，必须判断引用是否为null
  - 通过引用访问域前，先判断引用是否为null，再判断对象实例的域是否为空
    - 例：String s，`if(s != null && !s.isEmpty())`
  - 设计时应明确分割类职责和对象职责
    - 类职责、对象职责决定类状态、对象状态
    - 类状态由更改器、static初始化块进行控制
    - 对象状态由更改器、构造器、工厂方法、非static初始化块进行控制
- 继承
  - ![](Photos/2023-11-28-20-30-15.png)
  - 引用指向子类对象，可调用子类对象及父类对象的public方法
    - 子类构造器的第一行必须调用父类构造器，子类构造器不能负责控制父类对象状态
    - 如果第一行不是super(parameters)，那么JVM插入代码调用无参构造器，如果父类没有无参构造器将报错；
    - 子类对象内部不仅可通过super.方法名调用父类的public方法，还可以调用父类的protected方法
  - 约定
    - 调用父类方法时只依赖于父类对象的状态
    - 调用子类方法时只依赖于子类对象的状态
  - java只支持公有继承，公有继承与组合的区别：
    - 公有继承时，引用所指的对象中最多只有一个对象，并且该引用既能调用子类对象也能调用父类对象
    - 组合时，引用所指的对象中可有多个对象，每个对象有明确的引用
    - 因此java不支持多重继承，继承链只多只有一条
- 抽象类使用abstract进行标识，表示该类不能实例化
  - 允许抽象类具有域、使用abstract修饰的方法、不被abstract修饰的方法
  - 如果一个类具有被abstract修饰的方法，则必须声明为abstract类
  - 当一个类实现接口或继承抽象类但是不能实现其方法时，必须使用abstract进行修饰
  - 抽象类可以不具有抽象方法
- 重写 & 重载
  - 约定：方法签名不变，职责就不应更改
    - 方法职责 = 参数状态到方法行为的映射集合
    - 方法职责不变 = 映射集合遵守父类定义的契约或映射集合是父类的真子集
    - 方法行为 = 异常、返回的变量、通过引用更改外界定义的对象
  - 重写不应违背里氏替换原则
    - 父类要求子类重构：父类定义契约，子类遵守契约
      - 重写equals()需要遵守五个原则（自反性、对称性、传递性、一致性、null参数总是映射到false）
    - 接口、抽象类要求子类实现抽象方法：子类应遵守接口、抽象类定义的契约
    - 子类因父类算法陈旧等原因重构父类方法：子类不能更改方法职责（参数状态到方法行为的映射关系）；
    - 重写后，指向子类对象的引用不能调用父类方法
    - 重写使用`@Override`注解进行标注
    - 父类不希望子类重写方法时，使用final修饰方法（该方法必须是public否则使用final修饰没有意义），仍可被继承
    - 使用final修饰类，表示不能被继承
  - 重载不应违背里氏替换原则
    - 在子类定义的方法与父类方法同名，但是参数列表不同时，发生重载
    - 子类方法与父类方法处理的参数列表交集为空时，不违背里氏替换原则
    - 子类方法与父类方法处理的参数列表交集不为空时，子类方法的参数列表状态必须是父类参数列表状态的超集
      - 表现为：父类方法能处理的参数状态总是调用父类方法，父类方法无法处理的参数状态才调用子类的重载后的方法
  - 重写、重载后的方法的行为需遵守以下规则
    - 返回值类型是父类方法返回值类型的子类
    - 抛出的异常是父类方法抛出异常的子类，如果父类方法没有抛出异常，则子类不能抛出异常
    - 子类的可见性不能比父类低
  - 遵守上述设计符合里氏替换原则：父类对象能正确工作的地方，替换为子类对象后不会有任何改变
- 继承访问器、更改器方法（不限于setter，包括所有更改域的方法）
  - 当继承访问器、更改器方法时，对外界观察者而言，子类继承了父类的私有域，此时开发者就应当检查访问器、更改器方法是否在子类状态上运算封闭
  - 例如Holiday继承Day的set和get方法时，就必须重写set和get方法，确保指向Holiday的引用不会得到、操控非节假日的日期；
- 优雅地使用多态，而不是愚蠢地使用if

#### Object

- 源码：
  - `getClass()`：不可重写
  - `toString()`所有子类都应重写
  - `hashCode()、equals(Object obj)`：要么都重写要么都不重写
  - `clone()`：要调用就必须先重写

```java
package java.lang;

/** Object类是类层次结构的根，是所有对象的超类：包括数组、异常*/
public class Object {
    public Object() {}

    /**返回运行时静态擦除后的Class对象*/
    public final native Class<?> getClass();

    /**重写hashCode方法必须遵守以下契约：
     *  职责：
     *      返回值作为哈希值供哈希表使用
     *      遵守契约减少冲突概率，提高哈希表性能
     *  一致性：
     *      应用上下文不变（同一个应用程序）且对象状态不变时，多次调用hashCode方法返回相同的哈希值
     *    哈希值不能在对象序列化和反序列化时标识对象
     *  自反、等价：
     *      对象A调用equals方法传入对象B返回true时，A.hashCode()==B.hashCode()返回true
     *      对象A调用equals方法传入对象B返回false时，A.hashCode()==B.hashCode()返回false
     * Object实现了此方法，只要对象不同，返回的哈希值就不同
     * 重写equals方法需要同步重写hashCode方法，该契约规定：相等的对象必须具有相等的哈希码*/
    public native int hashCode();

    /**重写equals()应遵守以下契约
     *  职责：
     *     检查某个对象是否与此对象相等
     *  自反性：
     *    对任何非空引用x，x.equals(x)返回true
     *  对称性：
     *     对任何非空引用x、y，若x.equals(y)返回true，则y.equals(x)返回true
     *  传递性：
     *      对任何非空引用x、y、z，若x.equals(y)返回true且y.equals(z)返回true，则x.equals(z)返回true
     *  一致性：
     *      对任何非空引用x、y，若对象状态未更改，则多次调用x.equals(y)一致地返回true或一致地返回false
     *  null不等价于任何对象
     *    对于任何非空引用值x，x.equals(null)应返回false
     * 遵循以上契约的equals将所有对象划分为等价类或非等价类，等价类的所有成员相互相等
     * Object实现了equals方法，当且仅当引用值相等时，equals返回true，即每个等价类有且只有一个元素，即该对象本身
     * 重写equals方法需要同步重写hashCode方法，该契约规定：相等的对象必须具有相等的哈希码
    */
    public boolean equals(Object obj) {
        return (this == obj);
    }

    /**Object实现clone()方法：构造一个新的对象，该对象状态等于源对象状态，Object实现的clone()方法满足以下约定
     *    x.clone()!=x返回true
     *    x.clone().getClass() == x.getClass()返回true
     *    x.clone().equals(x)返回true
     *    x.hashCode()==x.clone().hashCode()返回true
     * 子类如何调用clone()方法
     *    将clone()方法的可见性从protected升级到public
     *    实现Cloneable标记接口
     *    更改方法体内容，更改返回值的类型为更严格的子类型
     *    因为子类一定会重写clone方法，浅拷贝和深拷贝的区别只有方法体的实现
     *    如果方法体只有一条语句：super.clone()，则代表采用浅拷贝
     *    如果方法体对每个指向可变对象的引用调用clone()方法，则代表采用深拷贝
     * 浅拷贝与深拷贝
     *    若对象状态仅由基本数据类型和指向不可变对象的引用组成，则适合使用浅拷贝；只要对象状态具有指向可变对象的引用，则必须采用深拷贝
     *    因为需要对每个指向可变对象的引用调用clone()方法，因此需要保证这些可变对象的类重写clone()方法并实现Cloneable接口，通过这种方法，替换引用所指的对象和引用的值；
     * 约定
     *    只要遵守上述步骤实现浅拷贝或深拷贝，就能满足Object定义Clone方法的契约，尽管这不是必须遵守的
     *    数组对象实现Cloneable接口，只要可变的数组元素引用实现Cloneable接口就能正常调用Cloneable接口
    */
    protected native Object clone() throws CloneNotSupportedException;

    /**Object实现的toString方法
     *    类名@无符号十六进制哈希值
     * 默认的toString方法依赖于哈希值，间接依赖于对象状态、JVM实现、应用程序上下文
     *    不能在不同时间、不同JVM之间稳定
     * 所有子类都应该覆盖此方法
    */
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
}


```
- ==与equals
  - ==的含义：是否引用同一个对象实例
  - equals的含义：两个对象实例是否属于同一个等价类
- 调用equals()、hashCode()前应判断是否为null，一共四种状态共需判断三次，因此采用方法二使用静态方法对方法一进行封装
  - java.util.Objects.equals(s1,s2)
  - 基本数据类型转换为封装类：统一使用静态方法Double.hashCode()
  - Objects.hashCode()
    - 对象为null时返回0
  - Arrays.hashCode()
    - 将使用所有数组元素返回的hashCode进行运算
  - Objects.hash(变量)
    - 对所有变量（引用变量和基本数据类型）调用hashCode进行加权运算
```java

/*方法一：不推荐使用*/
if (s1 == null && s2 == null)
    return true;
else if (s1 == null || s2 == null)
    return false;
else
    return s1.equals (s2)

/*方法二*/
java.util.Objects.equals(s1,s2)

```
- 重写toString()
  - 测试（Assert&debug）、日志、打印对象、打印Collection
  - 约定：
    - 对象总是应该重写toString方法，如果不重写，就不该通过实例化对象调用toString方法
    - 重写toString方法时，打印对象状态，每个域使用,进行分割
    - 子类重写toString方法时通过super.toString进行调用
  - 自定义实现
    - 使用StringBuilder(初始容量)进行固定格式的拼接（使用String的拼接性能差）
    - 例如：`id=1,name=小王,sex=女`
  - 数组对象没有重写toString方法，因此不该通过数组引用调用toString
    - 使用静态方法Arrays.toString()返回String
- 重写equals()、hashCode()
  - 约定
    - 要么同时重写equals和hashCode方法，要么都不重写
    - 重写时，只要对象状态相同，equals就应该返回true，其hashCode也是相同的
    - 重写时，如果语义相等是对象状态的子集，那么equals和hashCode使用的子集应是相等的；
    - 重写时，不能直接使用引用的值判断，而应使用静态方法`java.util.Objects.equals`，因为要求参与比较的对象也重写了equals方法
  - 重写的细节
    - equals内部需要判断是否指向同一个对象
    - 然后判断该对象是否为null
    - 然后判断两个对象的类型关系，这里涉及两个对象在同一条继承链上的问题，如果用于判断是否相等的域子类和父类都有，那么只需要判断能否转型为某个父类（instanceof），如果用于判断是否相等的域只有本类拥有，就需要判断能否转型为该子类（getClass然后判断两个Class对象是否相等）
    - 然后依次判断基本数据类型，对引用字段调用`java.util.Objects.equals`
  - 集合判断key是否相同时，使用hashCode而不是equals
  - 重写hashCode时需要选择每个域所使用的倍数，通常选择17等质数；
- 重写clone()
  - implement Cloneable接口
  - 重写clone方法，第一句总是调用thisClass a = (thisClass)super.clone()方法，并转型
  - 然后对a域中的引用类型依次调用clone方法，返回值通过更改器方法重新赋予给该字段
    - 注意按照约定，即使是内类部也应该通过更改器对域进行访问，避免动态代理报错；
  - 将访问控制提升到public（非必要）
  - 将返回类型从Object提升到thisClass


#### 内部类

- 内部类是编译器级别的处理，虚拟机只知道内部类对象通过隐式引用访问外部类对象；
- 截止23.12.4，除封装性外，并未遇见内部类的重要使用；
### 接口相关



#### 接口
- 接口的设计思想
  - 接口是对实现类职责的公开声明，当不向他人提供服务时，就不该定义接口；当使用他人服务时，必须通过接口调用其服务
  - 信任规范所定义的接口，不信任接口的实现，使用接口类型的引用而不是实现类型的引用，避免第三方服务侵入业务逻辑代码；
- 语法
  - 每个类有且仅有一条继承链，但是可以拥有$[0,\infin)$个接口链
    - 判断接口的类型：`object instanceof aInterface`
  - 允许接口链与继承链拥有相同的方法签名，但是当接口链提供方法实现时可能会产生冲突
    - 定义相同的方法签名往往是为了在注释中说明不同的职责，实现者应同时承担接口链中与继承链中的职责；
    - 若继承链在相同的方法签名中提供实现，那么接口链中提供实现是一种错误设计，因为接口中的实现总是会被覆盖；此时允许接口链中提供相同的方法签名，在注释中写明额外职责并期望实现者会遵守约束
    - 若继承链未在相同的方法签名中提供实现，那么在接口链中提供实现默认继承链中的相同方法现在不会，以后也不会提供实现；
    - 接口链中有且至少有一个相同方法提供实现时，会在实现类产生冲突，解决冲突的方式有两种：当冲突无法在该实现类解决时，使用abstract修饰实现类；当冲突可以在该实现类解决时，提供实现即可（重写
```java
public interface service {
    //不能声明非static域
    private static double StandardPI = 3.14;
    private static double ExtendedPI = 3.1415;
    //非default、static修饰的方法不能添加方法体
    static double getStandardPI(){
        return StandardPI;
    }
    default double getExtendedPI(){
        return ExtendedPI;
    }
}

```
- 遗留问题
  - jdk1.8之前不允许在接口中定义方法，出现许多成对的接口-工具类（提供静态方法）
  - 实际上完全可以将工具类的静态方法添加到接口中；为了所谓的符合抽象规范（实际上为接口方法添加实现并没有任何不合理的地方），提高了设计成本、使用成本；是一种过度设计

#### 函数式接口

- 在`java.util.function`中定义了大量函数式接口
  - ![](Photos/2023-09-05-10-17-50.png)
  - ![](Photos/2023-09-05-10-34-58.png)
  - 约定
    - 小写字母代表主数据类型，大写字母代表类
    - 函数式接口的名称及其意义与参数列表、返回类型匹配
    - 函数式接口存在大量形如intSupplier的接口，以避免自动装箱拆箱
```java

/**@FunctionalInterface注解：声明该接口为函数式接口
 *    启动编译时检查，若该接口定义多个抽象方法或未定义抽象方法，将不会通过编译
 *    启用对lambda表达式、方法引用、构造器引用的支持
*/
@FunctionalInterface
/**T类型的对象集合映射到R类型的对象集合*/
public interface Function<T, R> {
    R apply(T t);

    /**@return复合函数Function<V, R> @param <V> {@code before}：
     *    先调用before接口的抽象方法：T apply(V v)
     *    再调用本接口的R apply(T t)
     *    V类型的对象集合(调用者输入的参数)->T类型的对象集合->R类型的对象集合(调用者得到的参数)
     * @throws NullPointerException               ，若before是null
     */
    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    /**@return复合函数Function<T, V> @param <T> {@code after}：
     *    先调用本接口的抽象方法：R apply(T t)
     *    再调用after接口的V apply(R r)
     *    T类型的对象集合(调用者输入的参数)->R类型的对象集合->V类型的对象集合(调用者得到的参数)
     * @throws NullPointerException 如果after为null
     */
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
}
```

- 将所有只拥有一个抽象方法的接口视为函数式接口并开启函数式编程的支持；
  - 注解@以显式地向开发者声明该接口是函数式接口

```java

@FunctionalInterface
public interface Comparator<T> {
  int compare(T o1, T o2);
}


@FunctionalInterface
public interface runService {
    void runService();
}
public static void main(String[] args){
    runService run = (i)->{System.out.println (i);};
    int i=0;
    while(i<10){
        run.runService (i++);
    }
}
```
- lambda语法

```java
/**括号
 *    无参数或多个参数时必须书写()
 *    仅有一个参数时省略()
 * 花括号
 *    仅有一条语句时，省略花括号和return
 * 类型说明
 *    代码块所装入的方法明确约束类型参数时，可在lambda中省略类型说明
 * return
 *    若存在if语句，则必须在每个条件分支都写上return语句
 *    当只有一条语句时，省略return并将该语句的表达值作为返回值（如果有的话）
 * 
*/
(params here)->{code here};
/**此处代码块所装载的方法明确指出参数类型是String，所以无需在lambda表达式中说明类型参数为String，交给JVM自行判断*/
Comparator<String> comp=(first, second)->first.length()-second.length(); 


```

- lambda作用域
  - lambda语句中可使用外围作用域中的自由变量，当传递代码块时，被lambda使用的自由变量将会被捕获装入匿名类一同传递，遵守值的复制
  - 当lambda延迟调用时，源自由变量被析构不会对lambda中使用的变量有任何危害
  - 当lambda并发调用时，源自由变量更改或者代码块中更改被捕获的自由变量都有可能造成并发问题，因此要求源自由变量是最终值，代码块中不允许对自由变量进行更改；
  - lambda中的变量，与lambda所在作用域中的变量不能同名
- 自定义函数式接口
  - 使用注解`@FunctionalInterface`标记接口
  - 任意有且仅拥有一个抽象方法的接口都是函数式接口，但是使用注解将避免增加抽象方法
- lambda、方法引用、构造器引用有且只有一个能力：将参数、代码块、返回值分别装入函数式接口的抽象方法
  - 在没有函数式编程时，需要定义一个实现类，实现某个仅拥有一个抽象方法的接口，并用业务逻辑代码实现抽象方法，然后实例化这个实现类，并通过传递对象的形式间接传递代码块；
  - Jdk1.8之后对所有仅拥有一个抽象方法的接口开启函数式编程的支持
  - 调用参数中拥有函数式接口的方法时，只需传入lambda表达式、构造器引用、方法引用，Jvm将自动实现匿名类
  - 函数式编程的优势：清晰简洁地传递代码块，而无需考虑代码块的命名，只需关注业务逻辑，包括：参数、实现、返回值；方法名不再重要；

```java
Function<Integer,Integer> function = (num)->{return num*num;};
function.apply (1);
```

- 再谈Comparator
  - Comparator是仅有一个抽象方法的接口，Arrays.sort静态方法允许传入数组及比较器，其定义和使用lambda的方式如下
  - 数组元素T本身可实现Comparable接口，但是Comparable接口只能定义一种自然顺序，当T类型的对象组成的集合拥有不止一种自然顺序时，就需要Comparator比较器定义另一种自然顺序；
  - 例如，String实现了Comparable接口，使用字典顺序作为自然顺序；但是当业务逻辑需要使用字符数量作为自然顺序时，自然就需要Comparator；
  - 假定我们需要复用排序规则以确定更复杂的自然排序，那么就需要使用thenComparing系列方法，此处省略，在需要时阅读源码即可
  - comparing、thenComparing提供形如comparingInt等形式的方法，避免自动装箱拆箱
  - 同时Comparator提供reverseOrder以支持将自然顺序进行逆序

```java

public static <T> void sort(T[] a, Comparator<? super T> c){
  if (c == null) {
    sort(a);
  } else {
    if (LegacyMergeSort.userRequested)
      legacyMergeSort(a, c);
    else
      TimSort.sort(a, 0, a.length, c, null, 0, 0);
  }
}
public static void main(String[] args){
  String[] a={"1234","12","1","123"};
  Arrays.sort (a,(str1,str2)->{return str1.length ()-str2.length ();});
  System.out.println (Arrays.toString (a));
}
//方法引用，等价实现
public static void main(String[] args){
  String[] a={"1234","12","1","123"};
  Arrays.sort (a, Comparator.comparingInt (String::length));
  System.out.println (Arrays.toString (a));
}


```
- 方法引用、构造器引用
  - 使用现成的方法、构造器完成代码的传递
  - 上述使用键提取器完成比较器的构造使用了方法引用，假设现在的排序规则是对字符串排序而不考虑字母的大小写，可使用以下方法
    - `Arrays.sort({"dsA","acs","Adb"},String::compareToIgnoreCase)`
  - 假设需要传递打印的动作，只需传递以下表达式
    - `System.out::println`
  - 因此方法引用允许三种情况
    - object::instanceMethod，this::instanceMethod，super::instanceMethod
    - Class::staticMethod
    - Class::instanceMethod
  - 比较特殊的是第三种情况，需要在调用时额外传入该类的对象作为第一个参数
    - 例如传递`String::compareToIgnoreCase)`等价于
    - `(x,y)->x.compareToIgnoreCase(y)`
  - 传递方法引用时并未提供参数列表，因此默认开启重载
    - 当调用时，会从上下文选择合适的重载方法
  - 同lambda表达式，方法引用同样会被装入函数式接口
- 构造器引用和方法引用不同的地方在于，通过new引用，也是默认开启重载
  - 比较特殊的数组类型，也可以建立构造器引用
    - `int[]::new`：一个方法参数作为数组的长度
    - 等价于lambda表达式:`x->new int[x]`
  
```java
ArrayList<String> names = {"Jogn","Mose","Regure"};
Stream<Person> stream = names.stream().map(Person::new);
List<Person> people =stream.collect(Collectors.tolist());

```



### Java核心对象

#### wrapper
- 包装类包括：`Integer、Long、Float、Double、Short、Byte、Character`
- 当包装类不是null时，通过自动拆箱和自动装箱实现基本数据类型与包装类的等价
  - 对null进行自动拆箱将抛出异常
  - 因为包装类的域被final修饰，所以混用基本数据类型与包装类时将返回基本数据类型
  - 总是使用静态工厂方法valueOf进行装箱，因为包装类是不变类，同String，不同的引用可以指向相同的实例，如果使用new，底层系统就失去了优化的机会
- parse开头代表将字符串转为指定类型的静态API
    - `Integer.parseInt("100", 16)`按十六进制将指定字符串转义为整数
- 整型转字符串
    - `toString(100)`：转十进制
    - Integer.toString(100, 36)：转36进制
    - Integer.toHexString(100)： 转16进制
    - Integer.toOctalString(100)： 转8进制
    - Integer.toBinaryString(100)： 转2进制
- 所有包装类继承自Number，将一个数装箱后，获取该数的不同表示
    - `Number num = new Integer(999);`
    - `byte b = num.byteValue();`
    - `int n = num.intValue();`
    - `long ln = num.longValue();`
    - `float f = num.floatValue();`
    - `double d = num.doubleValue();`
- 使用静态工具类将有符号数转无符号数
    - `Byte.toUnsignedInt(x)`
    - 若x是-1，则结果为255
#### 枚举
- 枚举类通过enum进行声明
  - 枚举类通过构造函数构造常量，每个常量是枚举类的一个对象实例
- 下面两种class实际上等价，但是枚举类遵守语义更易理解
```java
public enum ErrorResponse{
    ITEM_NOT_FOUND(1001, "项目不存在");
    ErrorResponse (int code, String msg){
        this.code=code;
        this.msg=msg;
    }
    private final int code;
    private final String msg;
}

public class ErrorResponse{
    public final static ErrorResponse ITEM_NOT_FOUND = newErrorResponse(1001, "项目不存在");
    
    ErrorResponse (int code, String msg){
        this.code=code;
        this.msg=msg;
    }
    
    private final int code;
    private final String msg;
}
```
- 所有枚举类继承自Enum接抽象类
```JAVA
public abstract class Enum<E extends Enum<E>>
        implements Constable, Comparable<E>, Serializable {
    /**枚举实例的名字，通过toString进行访问*/
    private final String name;

    /**返回枚举实例的名字*/
    public final String name() {
        return name;
    }

    /**该字段表示枚举实例构造的顺序，表现为在枚举类中从上到下的声明顺序；非必要不使用该字段*/
    private final int ordinal;

    /**返回枚举实例声明的顺序*/
    public final int ordinal() {
        return ordinal;
    }

    /**返回枚举实例的名字，但是可以由开发者重写*/
    public String toString() {
        return name;
    }

    /**因为枚举实例是单例模式，因此使用==和equals效果一样，但是语义不同*/
    public final boolean equals(Object other) {
        return this==other;
    }

    /**不允许克隆，如果在子类内部调用该方法总是抛出异常，标记为final不允许继承*/
    protected final Object clone() throws CloneNotSupportedException {
        throw new CloneNotSupportedException();
    }

    /**首先判断二者的类型能否比较，再判断二者声明顺序的大小*/
    public final int compareTo(E o) {
        Enum<?> other = (Enum<?>)o;
        Enum<E> self = this;
        if (self.getClass() != other.getClass() && // optimization
            self.getDeclaringClass() != other.getDeclaringClass())
            throw new ClassCastException();
        return self.ordinal - other.ordinal;
    }
```

#### String
- 常用核心方法
```java

int length();

boolean equals(Object obj)

/**判断忽略大小写后的字符串是否相等*/
boolean equalsIgnoreCase(String anotherString)

/**字符串长度不为0返回true*/
boolean isEmpty();

/**字符串包含非空白字符返回true*/
boolean isBlank();
```
- 拆分||拼接

```java
/**根据给定的正则表达式拆分字符串*/
String[] split(String regex);

String concat(String s)

/**静态方法*/
String.join(String...strs)
```


- 子串||索引

```java
/**返回指定位置的字符*/
char charAt(int index);

/**返回指定子字符串在此字符串中第一次出现的索引*/
int indexOf(String str);

/**返回指定子字符串在此字符串中最右边出现的索引*/
int lastIndexOf(String str);

/**返回从指定索引开始到末尾的子字符串
 * 包含起始索引上的字符
 * */
String substring(int beginIndex);

/**返回指定范围内的子字符串
 * 包含起始索引上的字符
 * 不包含结束索引上的字符
 * */
String substring(int beginIndex, int endIndex);

/**判断是否以指定字符串为前缀*/
boolean startsWith(String prefix);

/**判断是否以指定字符串为后缀*/
boolean endsWith(String suffix);

/**判断字符串是否包含指定的字符序列*/
boolean contains(CharSequence sequence);
```

- 显示控制
```java
String toLowerCase();

String toUpperCase();

/**删除首尾空格，返回副本*/
String trim();

/**删除首尾空格，包括中文的空格字符\u3000*/
String strip()
```

- 格式化字符串
  
```java
/**将参数依次替换字符串中的占位符*/
String formatted()

/**静态方法*/
String.format()

String s = "Hi %s, your score is %d!";
System.out.println(s.formatted("Alice", 80));
System.out.println(String.format("Hi %s, your score is %.2f!", "Bob", 59.5));
```
- 正则

```java
/**将所有旧字符替换为新字符，返回一个新字符串*/
String replace(char oldChar, char newChar);

/**使用新字符串替换正则表达式匹配的所有子字符串*/
String replaceAll(String regex, String replacement);

/**判断字符串是否与给定的正则表达式匹配*/
boolean matches(String regex);
```

- valueOf

```java
/**返回对象的字符串表示形式*/
static String valueOf(Object obj);

/**返回字符数组的字符串表示形式*/
static String valueOf(char[] data);

/**返回字符数组的指定范围的字符串表示形式*/
static String valueOf(char[] data, int offset, int count);

/**返回布尔值的字符串表示形式*/
static String valueOf(boolean b);

/**返回字符的字符串表示形式*/
static String valueOf(char c);

/**返回整数的字符串表示形式*/
static String valueOf(int i);

/**返回长整数的字符串表示形式*/
static String valueOf(long l);

/**返回浮点数的字符串表示形式*/
static String valueOf(float f);

/**返回双精度浮点数的字符串表示形式*/
static String valueOf(double d);

```
#### StringBuilder

- 每次使用+拼接Stirng或调用String的append操作都会创建临时String对象然后由GC进行回收
  - 不需要使用StringBuilder替代普通的字符串+操作
  - JVM在编译时自动把多个连续的+操作编码为StringConcatFactory，自动优化为StringBuilder
- 现在完全没有必要使用StringBuffer
- StringBuilder
  - 职责：在预分配的可变长度缓冲区中高效操作字符串
- 构造器
```java
/**无参构造器，默认预留16个char大小的空缓冲区*/
public StringBuilder(){
  super(16)
}

/**创建指定大小的空缓冲区*/
public StringBuilder(int capacity) {
    super(capacity);
}

/**创建一个缓冲区，其长度为str.length + 16*/
public StringBuilder(String str) {
    super(str);
}

/**创建一个缓冲区，其长度为seq.length + 16*/
public StringBuilder(CharSequence seq) {
    super(seq);
}
```
- append
  - 追加字符串，如果参数为空，则追加字符串"null"
  - 返回this，因此允许链式操作
```java
public StringBuilder append(Object obj)

public StringBuilder append(String str)

public StringBuilder append(StringBuffer sb)

public StringBuilder append(CharSequence s)

public StringBuilder append(CharSequence s, int start, int end)

public StringBuilder append(char[] str)

public StringBuilder append(char[] str, int offset, int len) 

public StringBuilder append(boolean b)
public StringBuilder append(char c)
public StringBuilder append(int i)
public StringBuilder append(long lng)
public StringBuilder append(float f)
public StringBuilder append(double d)
```

#### SpringJoiner

```JAVA

private final String prefix;
private final String delimiter;
private final String suffix;

/**StringJoiner没有添加prefix、suffix或调用add方法时，调用toString返回空字符串
 * 设置EmptyValue后，当调用toString返回EmptyValue的值
*/
setEmptyValue(CharSequence emptyValue)


/**若既没有调用setEmptyValue，也没有调用add，也没有初始化prefix、suffix，则返回空字符串*/
StringJoiner(CharSequence delimiter)

/**若没有调用add
 *  优先返回setEmptyValue
 * 若没有设置setEmptyValue则返回prefix + suffix*/
StringJoiner(CharSequence delimiter, CharSequence prefix, CharSequence suffix)

/**没有元素返回prefix + suffix或emptyValue
 * 否则返回prefix+到目前为止使用 delimiter 分隔的值+suffix
*/
toString()

/**如果 newElement 为 null，则添加 "null"
 * 分隔符只存在于两个元素之间
*/
add(CharSequence newElement)

/**合并两个StringJoiner
 * 如果other不为空，则将其内容的副本添加为下一个元素
 * 如果other使用不同的分隔符，则将另一个 StringJoiner 中的元素与该分隔符连接起来，将结果附加到此 StringJoiner 作为单个元素。
*/
merge(StringJoiner other)

/**返回toString返回的内容的长度
 * 如果没有调用 add 方法，则将返回字符串表示形式的长度（即 prefix + suffix 或 emptyValue 的长度）*/
length()
```

#### BigInteger
<a href="https://www.liaoxuefeng.com/wiki/1252599548343744/1279767986831393">1</a>

#### BigDecimal
<a href="https://www.liaoxuefeng.com/wiki/1252599548343744/1279768011997217">1</a>



### Java核心接口


#### Comparable接口&Comparator比较器接口
- `Comparable<T>`
  - String实现Comparable接口，按字典顺序比较大小

```java

public interface Comparable<T>{
  /**职责：比较当前T类型对象与指定T类型对象的顺序
   *    T类型对象的集合具有从"小"到"大"的顺序
   *    当前对象"小于"指定对象时，返回负整数
   *    相等，返回零
   *    当前对象"大于"指定对象时，返回正整数
   * 实现者必须确保对于所有的 x 和 y
   *    signum(x.compareTo(y)) == -signum(y.compareTo(x))
   *    若y.compareTo(x)抛出异常，则x.compareTo(y)必须抛出异常
   *    此外还必须确保关系是传递的：(x.compareTo(y) > 0 && y.compareTo(z) > 0) 意味着 x.compareTo(z) > 0
   *    对于所有 z，x.compareTo(y) == 0 意味着 signum(x.compareTo(z)) == signum(y.compareTo(z))
   * 异常说明
   *    如果指定的对象为 null，抛出 NullPointerException
   *    如果指定对象的类型不允许与当前对象进行比较，抛出ClassCastException
   * 强烈建议但不是严格要求
   *    x.compareTo(y) == 0 的同时有 x.equals(y)
   *    若实现类违反上述要求应指明这一事实
   *    建议的标注是：注意：这个类具有与 equals 不一致的自然排序
   * */
  public int compareTo(T o);
}

```

- Comparator比较器接口

```java
@FunctionalInterface
public interface Comparator<T> {
  /**职责：类型T的所有对象组成集合S，比较两个参数在S中的自然顺序
   *    返回一个负整数、零或正整数，分别表示第一个参数小于、等于或大于第二个参数
   * 实现者必须确保对于所有的 x 和 y
   *    signum(compare(x, y)) == -signum(compare(y, x))
   *    只有在 compare(y, x) 抛出异常时，compare(x, y) 才能抛出异常
   *    关系是可传递的：((compare(x, y) > 0) && (compare(y, z) > 0)) 意味着 compare(x, z) > 0。
   *    必须确保 compare(x, y) == 0 意味着对于所有的 z，signum(compare(x, z)) == signum(compare(y, z))。
   * 异常：
   *    NullPointerException：如果一个参数为null，而这个比较器不允许null参数。
   *    ClassCastException：如果参数的类型阻止它们被这个比较器比较
   * 约定
   *    compare(x, y) == 0 等价于 x.equals(y)
   *    任何违反这个条件的比较器应该清楚地指出这一事实
   *    推荐的表述是
   *        注意：此比较器施加的排序与 equals 不一致
   * */
  int compare(T o1, T o2);

  /**从类型T提取用于比较的键，实现键的比较并返回比较器
   * 如果keyExtractor是可序列化的，则返回的比较器也是可序列化的
  */
  public static <T, U extends Comparable<? super U>> Comparator<T> comparing(
          Function<? super T, ? extends U> keyExtractor)
  {
      Objects.requireNonNull(keyExtractor);
      return (Comparator<T> & Serializable)
          (c1, c2) -> keyExtractor.apply(c1).compareTo(keyExtractor.apply(c2));
  }


  default <U> Comparator<T> thenComparing(
            Function<? super T, ? extends U> keyExtractor,
            Comparator<? super U> keyComparator)
    {
        return thenComparing(comparing(keyExtractor, keyComparator));
    }
  /**限制说明   
   *    本方法遵守Object.equals(Object)的一般约定
   *    对于每个对象引用o1和o2，当且仅当signum(comp1.compare(o1, o2)) == signum(comp2.compare(o1, o2))时comp1.equals(comp2)
   * 不重写Object.equals(Object)通常是安全的。然而，在某些情况下，重写此方法可能会提高性能，允许程序确定两个不同的比较器施加相同的顺序
   * */
  boolean equals(Object obj);
```

## 面向切面编程

### 反射

### 动态代理

#### JDK动态代理

- 运行时反射生成代理实例，通过关联处理器实现对被代理对象
```java
interface Entertainment {
    void offerEntertainment ();
}

class Cinema implements Entertainment {
    @Override
    public void offerEntertainment () {
        System.out.println ("The movie theater is currently offering movie-watching services.");
    }
}

class Stadium implements Entertainment {
    @Override
    public void offerEntertainment () {
        System.out.println ("The stadium is currently providing sports");
    }
}

class EntertainmentProxy implements InvocationHandler {
    private Entertainment entertainment;

    public EntertainmentProxy (Entertainment entertainment) {
        this.entertainment = entertainment;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("preparing");
        method.invoke(entertainment, args);
        System.out.println("Cleaning");
        return null;
    }
}

public class DynamicProxyExample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Entertainment realEntertainment;
        if (scanner.nextInt() == 1)
            realEntertainment = new Cinema();
        else
            realEntertainment = new Stadium();
        Entertainment proxy = (Entertainment) Proxy.newProxyInstance(
                Entertainment.class.getClassLoader(),
                new Class[]{Entertainment.class},
                new EntertainmentProxy(realEntertainment)
        );
        proxy.offerEntertainment();
    }
}


```

# ？
- 动态代理
  - 动机：职责分离
  - 目的：代码可重用、懒加载、访问控制、远程代理
  - 过程
    - 代理类和被代理对象应具有相同的接口，运行时Proxy类创建实现指定接口的、拥有Object方法的代理类，再创建该代理类的代理对象；
    - 通过`newProxyInstance`创建代理类和代理对象；实现`InvocationHandler`接口的类告诉Proxy如何书写代理类代码
      - `InvocationHandler`接口的invoke方法接受三个参数，分别是代理对象，被代理对象需要执行的方法以及方法参数：参数代理对象通常不用
      - 实现`InvocationHandler`接口的类存储被代理对象，该对象引用将被拷贝给代理对象；被代理对象将用于invoke方法调用
      - 在invoke方法中定义自定义逻辑并在合适的地方调用被代理对象的方法；
      - Proxy类在创建代理类时，简单的覆写了`toString()、equals()、hashCode()`方法，并实现了接口方法；都是通过调用invoke方法实现的
      - Object的其它方法并没有覆写，因此将不会调用invoke，例如getClass()、clone()

```java

//java.lang.reflect.Proxy
//类Proxy是创建代理类的静态工厂

/**使用指定的类加载器，根据指定的接口创建代理类并创建代理对象
 * 代理对象的唯一实例域是拷贝自实现`InvocationHandler`接口的类的被代理对象
 * 而实现`InvocationHandler`接口的类中的被代理对象是通过构造器注入的，因此被代理对象可以是任何实现了指定接口的版本
 * */
static Object newProxyInstance(ClassLoader loader, Class<?>[]
interfaces, InvocationHandler handler)

/**返回实现指定接口的代理类的Class对象
 * 假设类加载器和接口数组相同，那么getProxyClass将返回相同的Class对象
 * 因为newProxyInstance通过调用getProxyClass得到代理对象，因此类加载器和接口数组相同时，调用两次newProxyInstance将得到同一个类的两个对象
*/
static Class<?> getProxyClass(ClassLoader loader, Class<?>...interfaces)

/**如果cl是代理类则返回true*/
static boolean isProxyClass(Class<?> cl)

```


```java

// java.lang.reflect.InvocationHandler

Object invoke(Object proxy,Method method,Object[]args)

```

#### CGLIB动态代理
### 注解

### 异常

- 约定
  - 异常设计的第一个目标：当且仅当开发者需要终止当前线程时线程才会终止
    - 异常设计和处理是应用逻辑的一部分
    - 如果线程终止的原因在开发者预料之外，说明应用逻辑设计不完善；
  - 异常设计的第二个目标：正常业务处理逻辑与错误处理逻辑分离


  - 约定开发者仅抛出继承自RuntimeException可分辨的自定义子类，而不应抛出Exception及其子类（除RuntimeException及其子类外）
  - 捕获Exception及其子类并抛出继承自RuntimeException可分辨的自定义子类
  - 抛出RuntimeException后，在所有的业务流程中不对RuntimeException进行处理，使用AOP在线程结束前（最后一个方法，通常是Controller）捕获异常，然后交给全局异常处理器进行处理
- 异常体系
  - ![](Photos/2023-11-30-09-26-00.png)
  - 受查异常：抛出IOException及其子类后，必须沿栈强制显式捕获或抛出
  - 非受查异常：JVM不要求抛出RuntimeException及其子类的方法声明会抛出RuntimeException，使用这个方法的方法也无需显示捕获或抛出非受查异常；当异常抛出时，会沿栈终止正常业务流程，直到被捕获
- `try-catch-finally`
  - 无论有多少个catch，每次最多只有一个catch分支会被执行，一个catch分支可以使用|处理多个异常
  - 当`try-catch-finally`用于打开资源

### 日志

## 测试

## 泛型

### 集合框架

### 集合实现

## I/O

## 流
# JavaEE
# 并发
# 虚拟机

## .class

- classpath：一组目录的集合，格式和系统相关；指示jvm在指定目录集合中搜索.class文件
  - 不应该在系统环境变量中设置classpath，而是应该在运行java命令时附带classpath参数
  - IDE自动传入的cp参数是当前工程的bin目录和引入的jar包
  - 约定既不在系统环境变量设置classpath，也不在java命令传入参数，而是在工程根目录运行java命令
```bash

# .代表当前目录
# 分号代表目录的分隔符
# 目录格式与分隔符的格式由操作系统决定
java -classpath .;C:\work\project1\bin;C:\shared abc.xyz.Hello

# 可以将classpath简写为cp
java -cp .;C:\work\project1\bin;C:\shared abc.xyz.Hello

# 按约定不设置系统环境变量，并且没有传入cp参数时，默认classpath是当前命令的目录
java abc.xyz.Hello

```
# Security

## Random & SecureRandom




# wait to reconstitution
## JVM相关
源文件：最多存放一个公共类，任意数量默认类，扩展名.java
  - 如果有公共类，源文件名必须与公共类的名字相同；否则名字任取
    - 允许源文件同时存在接口类与公共类
    - 同一个源文件一定属于同一个包，是相互可见的
  - 编译源文件，生成多个class文件；


- 命令行
```java

        javac my_class.java //编译为java字节码序列

        java my_class //运行.class文件，注意不带后缀，my_class为类名和文件名

```
## IO

### File

- 路径
  - 绝对路径：以根目录开头的完整路径
  - `File.separator`获取平台分隔符，避免移植到不同平台出错
    - 在windows，File.separator将转换为`\\`
  - 主流操作系统遵守.->当前目录，..->上级目录，允许路径包括..或.，从左到右进行目录跳转；
- File职责：管理文件/目录
  - 构造器：
    - ->相对路径，当前目录的绝对路径+相对路径=该File的绝对路径
  - `getPath()`->返回构造器传入的路径
  - `getAbsolutePath()`->返回绝对路径
    - `C:\Windows\System32\..\notepad.exe`
  - `getCanonicalPath`->返回规范的绝对路径
    - `C:\Windows\notepad.exe`
  - <FONT COLOR=RED>构造File对象不会做任何IO操作，所以能使用对应目录/文件不存在的路径构造File对象</FONt>
- 文件目录通用操作
```java
/***/
isFile

/***/
isDirectory()

/**可读权限*/
boolean canRead()

/**可写权限*/
boolean canWrite()

/**可执行权限*/
boolean canExecute()

/**字节大小*/
long length()
```
- 只适用文件的操作

```java

createNewFile()

delete()

/**JVM退出时删除该文件*/
createTempFile()

```  

- 目录

```java
/**文件、子目录名
 * this.isDirectory=false->return Null
*/
String[] list() 方法：

/**该目录所有文件、子目录的File引用
 * this.isDirectory=false->return Null
*/
File[] listFiles() 方法：

/**创建当前File对象表示的目录*/
boolean mkdir()：创建当前File对象表示的目录；
/**创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来*/
boolean mkdirs()：创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来；
/**删除当前File对象表示的目录，当前目录必须为空才能删除成功*/
boolean delete()：删除当前File对象表示的目录，当前目录必须为空才能删除成功。
```

### Path

- 对目录进行拼接、遍历等操作