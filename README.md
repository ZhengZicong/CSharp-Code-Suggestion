# CSharp-Code-Suggestion

### 基本语言要素
---
#### 建议1：正确的操作字符串
* 确保尽量少的装箱
* 避免分配额外的内存空间
* 基本类型转换至字符串类型尽量使用它们的 .ToString( ) 方法 &nbsp;&nbsp;&nbsp;[ 它们通常是直接操作内存来完成类型之间的转换的 ]
* 尽可能使用 StringBuilder 类进行字符串拼接
* 使用 string.format( ) 方法进行 StringBuilder 简化拼接操作

#### 建议2：使用默认的转型方法
* 使用类型的转换运算符 [ 包括隐式转换和显示转换(贴标签) ]
* 使用类型内置的转换方法 [ 包括Parse( ),TryParse( )或者ToString( ),ToDouble( ),ToDateTime( )等方法 ]
* 使用帮助类提供的方法 [ 包括System.Convert类、System.BitConvert类 ]
* 使用CLR支持的转型 [ 实际上就是基类和子类之间的相互转换 ]

#### 建议3：区别对待强制类型与as和is
* as操作符永远不会抛出异常 [ 避免引发异常带来效率问题 ]

#### 建议4：TryParse比Parse好
* Parse会引发异常,引发及捕获异常时会对性能造成损耗

#### 建议5：使用int?来确保值类型也可以为null
* 取值转换赋值为int,不会因为是null而引发异常
* 数据被篡改转型失败后应该保存为null值,而不是默认值

#### 建议6：区别readonly和const的使用方法
* const是一个编译期常量,readonly是一个运行时常量
* const只能修饰基元类型、枚举类型或字符串类型,readonly没有限制
* const天然是static的
* readonly对于值类型变量,值本身不可改变
* readonly对于引用类型变量,引用本身(相当于指针)不可改变

#### 建议7：将0值作为枚举的默认值
* 编译器自动从0值开始计数
* 枚举默认值为0,您如果没有为0值的枚举值,使用也不会引发异常 [ 出乎意外的选项应是不允许的 ]

#### 建议8：避免给枚举类型的元素提供显式的值
> 上一个建议已经讲到如果没有为元素显式赋值,编译器会逐个为元素的值+1
```csharp
//注意以下的代码
enum Week
{
  Monday = 1,
  Tuesday = 2,
  ValueTemp,
  Wednesday = 3,
  Thursday = 4,
  Friday = 5,
  Saturday = 6,
  Sunday = 7
}
```
> 执行时实际会发现 if ( Week.ValueTemp == Week.Wednesday ) 结果为 true ,当编译器发现元素 ValueTemp 时,它会自动在 Tuesday = 2 的基础上 +1,所以实际 ValueTemp 的值和 Wednesday 的值都是 3

#### 建议9：习惯重载运算符
* 构建类时考虑是否可以通过使用 operator 关键字定义静态函数来重载运算符

#### 建议10：创建对象时需要考虑是否实现比较器
* 构建类时考虑是否可以通过实现 IComparable<T> 比较器接口

#### 建议11：区别对待 == 和 Equals
* 一般来说,对于引用类型,我们要定义"值类型相等",应该仅仅去重载 Equals( ) 方法,同时让 "==" 表示"引用性相等"

#### 建议12：重写Equals时也要重写GetHashCode
* 除非考虑到自定义类型会被用作于散列的集合的键值;否则,不建议重写 Equals( ) 方法,因为这会带来一系列问题
* CLR首先会对 GetHashCode( ) 进行比较 [ 如未重写则会调用 object.GetHashCode( ) 方法,会对散列的集合处理产生影响 ]

#### 建议13：为类型输出格式化字符串
* 为类型重写 Object.ToString( ) 方法
* 让类型继承接口 IFormattable ,可以根据需求的变化为类型提供多个格式化器

#### 建议14：正确实现浅拷贝和深拷贝
* 浅拷贝-将对象的所有字段复制到新的对象中.其中值类型字段在副本中修改不会影响源对象的值.而引用类型字段在副本中修改则会影响到源对象本身
* 深拷贝-同样将对象中的所有字段复制到新对象中.不过无论是对象的值类型字段和是引用类型字段,都会被重新创建赋值,对于副本的修改,不会影响到源对象本身
* 浅拷贝过程中,应将字符串看成是值类型 [ 理论上string类型是引用类型 ]
* 建议实现接口ICloneable.Clone( )方法完成浅拷贝,由自己提供额外的方法完成深拷贝

#### 建议15：使用dynamic来简化反射实现
* 建议始终使用dynamic来简化反射实现 [ 除非你愿意牺牲大范围的代码整洁度去写复杂的反射优化 ]


### 集合和LINQ
---
#### 建议16：元素数量可变的情况下不应使用数组
* 数组不应指定过大长度,这会让数组成为一个大对象 [ 大对象在回收过程中会带来效率低的问题 ]
* 在元素数量可变的情况下请考虑ArrayList或List&lt;T&gt;

#### 建议17：多数情况下使用foreach进行循环遍历
* 使用foreach最大简化迭代代码
* foreach会自动将代码置入try-finally块
* 若类型实现了IDispose接口,他会在循环结束后自动调用Dispose方法

#### 建议18：foreach不能代替for
* foreach循环会调用MoveNext方法来遍历元素,在MoveNext方法内部会进行版本检测,当检测到版本号有变动,就会抛出InvalidOperationException异常.所以它不支持循环时对集合进行增删改查

#### 建议19：使用更有效的对象和集合初始化
* FCL3.5之后提供新语法:对象和集合初始化设定项
```csharp
  List<Person> personList = new List<Person>(){
    new Person() { Name='Rose', Age = 19 },
    mike,
    null
  }
```
> 初始化设定项绝不仅是为了对象和集合初始化的方便,它更重要的作用是为了LINQ查询中的匿名类型进行属性初始化

#### 建议20：使用泛型集合代替非泛型集合
* 非泛型集合在进行循环访问,转型或拆箱和装箱操作中对效率影响极大

#### 建议21：选择正确的集合
* 如果集合的数目固定并且不涉及转型,使用数组效率高,否则就使用 List&lt;T&gt; 
> 集合的分类
![list type](https://github.com/ZhengZicong/CSharp-Code-Suggestion/blob/master/image_1.png)<br/>
> FCL集合类图
![fcl list](https://github.com/ZhengZicong/CSharp-Code-Suggestion/blob/master/image_2.png)

#### 建议22：确保集合的线程安全
* 可以通过 Lock( ) 来实现集合在多个线程被操作时保持同步
* 可以使用System.Collections.Concurrent命名空间下实现线程的集合类
* ConcurrentBag&lt;T&gt; 对应 List&lt;T&gt;
* ConcurrentDictionary<TKey,TValue> 对应 Dictionary<TKey,TValue>
* ConcurrentQueue&lt;T&gt; 对应 Queue&lt;T&gt;
* ConcurrentStack&lt;T&gt; 对应 Statck&lt;T&gt;

#### 建议23：避免将List&lt;T&gt;作为自定义集合类的基类
* 如果要实现一个自定义的集合类,不应该以一个FCL集合类为基类
* 实现一个自定义集合类,应扩展相应的泛型接口.FCL集合类应该以组合的形式包含至自定义的集合类

#### 建议24：迭代器应该是只读的
* 不要为迭代器设置可写属性

#### 建议25：谨慎集合属性的可写操作
* 如果类型的属性中有集合属性,将其设置为可写,则会增加抛出异常的几率

#### 建议26：使用匿名类型存储LINQ查询结果
* 使用匿名类代替复杂多变的业务临时类型使代码变的简洁及易于维护
> 匿名类的基本特性
1. 既支持简单类型也支持复杂类型,简单类型必须是一个非空初始值,复杂类型则是一个以new开头的初始化项
2. 匿名类型的属性是只读的,没有属性设置器,它一旦被初始化就不可更改
3. 如果两个匿名类的属性值相同,那么就认为这两个匿名类型相等
4. 匿名类型可以在循环中用作初始化器
5. 匿名类型支持智能感知
6. 匿名类型也可以拥有方法

#### 建议27：在查询中使用Lambda表达式
* LINQ实际上是基于扩展方法和Lambda表达式的,任何LINQ查询都能够通过调用扩展方法和LINQ表达式来代替

#### 建议28：理解延迟求值和主动求值之间的区别
* 使用LINQ求值时,延迟求值能够带来显著的效果提升
* 应细知延迟求值和主动求值的区别,体会两者在应用中带来的结果,避免意想不到的Bug

#### 建议29：区别LINQ查询中的IEnumberable&lt;T&gt;和IQueryable&lt;T&gt;
* 使用本地数据源时采用IEnumberable&lt;T&gt;,远程数据源用IQueryable&lt;T&gt; [ Enumberable中查询参数接受的是Func<>,使用Queryable时接受的参数是Experssion<> ]

#### 建议30：使用LINQ取代集合中的比较器和迭代器
* 实现比较器和迭代器做操作可扩展性太低,对代码侵入性过高.强烈建议你使用LINQ所带来的便捷性

#### 建议31：在LINQ查询中避免不必要的迭代
* 实际编码中,要充分运用 First( ) 和 Take( ) 等方法,为应用带来高效性,而不会让时间浪费在一些无效的迭代中


### 泛型、委托和事件
---
#### 建议32：总是优先考虑泛型
* 无论是泛型类还是泛型方法都同时具备可重用性、类型安全和高效率等特性

#### 建议33：避免在泛型类型中声明静态成员
* 随着你为T指定不同的数据类型,泛型类也会变成不同的数据类型,在它们之间是不共享静态成员的

#### 建议34：为泛型参数设定约束
* 泛型约束让泛型参数具有更多的行为和属性,编码过程中应该始终考虑为泛型参数设定约束 [ 约束使泛型参数称为一个实实在在的"对象",让它具有了我们想要的行为和属性,而不是一个object ]

#### 建议35：使用default为泛型类型变量指定初始值
* 如果不知道泛型具体类型为值类型还是引用类型,要让编译器接收为一个泛型类型参数指定一个初始值,最妥当的办法就是使用default关键字 [ T t = default( T ); ] 

#### 建议36：使用FCL中的委托声明
* 应习惯在代码中使用FCL自带的委托声明来代替自己的委托声明,FCL中每一种委托都代表一类特殊的用途,使用自带委托可以让代码更具有简洁性和标准性

#### 建议37：使用Lambda表达式代替方法和匿名方法
* 使用Lambda或匿名方法可以更好的简化及美化代码 [ 前提是不重用 ]

#### 建议38：小心闭包中的陷阱
```csharp
  //设想以下代码会输出什么
  static void Main(string[] args)
  {
    //我们的意图是让匿名方法(在这里表现为Lambda表达式)接受参数i,并输出
    List<Action> lists = new List<Action>();
    for(int i = 0 ; i < 5 ; i++ )
    {
      Action t = () => { Console.WriteLine(i.ToString()); };
      lists.Add(t);
    }
    foreach(Action t in lists)
    {
      t();
    }
  }
  //你预想的结果可能是: 0 1 2 3 4 
  //而实际输出的是: 5 5 5 5 5
```
> 如果匿名方法引用了某个局部变量,编译器就会自动将该引用提升到该闭包对象中,将会偏离编码时预期的结果

#### 建议39：了解委托的实质
> 理解c#中的委托需要把握两个要点:
1. 委托类似于方法(类型安全)的指针
2. 委托是一个类(或这个类的集合),当对其(一)进行实例化的时候,要将引用方法作为它的构造方法的参数

#### 建议40：使用event关键字为委托施加保护
* event为委托加了保护,保证委托什么时候通知,是委托自己的职责,而不是由调用者决定

#### 建议41：实现标准的事件模型
> 微软为事件模型设定的几个规范
1. 委托类型的名称以EventHandler结束
2. 委托原型返回值为void
3. 委托具有两个参数:sender表示事件触发者,e表示事件参数
4. 事件参数的名称以EventArgs结束

#### 建议42：使用泛型参数兼容泛型接口的不变性
* 让返回值类型返回比声明的类型派生程度更大的类型,叫做"协变"
```csharp
  //例 以下称为协变
  public Employee GetAEmployee(string name)
  {
    Console.WriteLine("我是雇员:{0}",name);
    return new Programemer(){ Name = name }; // Programemer是Employee的子类
  }
```
* 但以下代码将会不成立 [ 编译错误 ]
```csharp
  interface ISalary<T> { void Pay( ); }
  
  class BaseSalary<T> : ISalary<T> { void Pay( ){ Console.WriteLine("pay base salary"); } }
  
  static void Main(string[] args)
  {
    ISalary<Programemer> s = new  ISalary<Programemer>();
    PrintSalary(s);
  }
  
  static void PrintSalary(ISalary<Employee> s)
  {
    s.Pay();
  }
```
> 可以注意到,本建议开头指出的"协变"是针对返回值而言的,但是所举上面的这个例子却没有体现出"返回值"这个概念
```csharp
  static void PrintSalary<T>(ISalary<T> s)
  {
    s.pay();
  }
```
> 上面这个改动可以正常运行.说明实际上,只要泛型类型参数在一个接口声明中不被用来作为方法的输入参数,我们都可姑且把它看成"返回值"类型的。上面这个改动,是满足"协变"的定义的.

#### 建议43：让接口中的泛型参数支持协变
* 除了建议42中提到的使用泛型参数兼容泛型接口的不可变性外,还有一种办法就是为接口中的泛型声明加上out关键字来支持协变
```csharp
  interface ISalary<out T> //使用out关键字
  {
    void Pay();
  }
```
* out关键字是FCL4.0中新增的功能,它可以在泛型接口和委托中使用,用来让类型参数支持协变。通过协变,可以使用比声明的参数派生类型更大的参数
* 如果需要编写泛型接口,除非确定该接口中的泛型参数不涉及变体,否则都建议加上out关键字.协变增大了接口的使用范围,而且几乎不会带来什么副作用

#### 建议44：理解委托中的协变
* 委托中的泛型变量天然是部分支持协变的

#### 建议45：为泛型类型参数指定逆变
* 逆变是指方法的参数可以是委托或泛型接口的参数类型的基类
1. FCL4.0中支持逆变的常用委托有 Func<in T,out TResult>、Predicate<in T>
2. 常用的泛型接口有 IComparer<in T>


### 资源管理和序列化
---
#### 建议46：显式释放资源需要继承接口 IDisposable
1. 托管资源：由CLR管理分配和释放的资源,即从CLR里new出来的对象
2. 非托管资源：不受CLR管理的对象,如Windows内核对象,或者文件、数据连接、套接字、COM对象等
* 如果类型需要显示释放资源,那么一定要继承IDispose接口

#### 建议47：及时提供了显示释放方法,也应该在终结器中提供隐式清理
* 对于实现了Dispose模式的类型,在每次创建对象时,CLR都会将该对象指针分配到一个终结列表,垃圾回收器在回收该对象内存前,会首先将终结列表中的指针放到一个freachable队列中.同时,CLR还会分配专门的线程读取freachable队列,并调用对象的终结器,只有这个时候,对象才会被标识为垃圾,并且在下一次进行回收时释放对象占用的内存
```csharp
//在标准的Dispose模式中,我们注意到一个以 ~ 开头的方法,如下所示:
  ///<summary>
  /// 必须,防止程序员忘记显示调用Dispose方法
  ///</summary>
  ~SampleClass()
  {
    Dispose(false);
  }
```
> 这个方法叫做类型的终结器:意义在于:我们不能奢望类型的调用者肯定会调用 Dispose( ) 方法,基于终结器会被垃圾回收器调用这个特点,它被用作资源释放的补救措施


#### 建议48：Dispose方法应允许被多次调用
* 在Dispose模式中,应该始终为类型创建一个变量,用来表示对象是否已经Dispose过,保证一个类型的Dispose方法允许被多次调用而不抛异常
* 当对象调用过 Dispose( ) 方法后,此时进行调用对象的公开的方法时,应该为调用者抛出一个ObjectDisposedException异常

#### 建议49：在Dispose模式中应提取一个受保护的虚方法
* 在标准的Dispose模式中,真正实现IDispose接口的Dispose方法并没有做实际的清理工作,而是交给一个带布尔参数且受保护的虚方法
```csharp
  public void Dispose( ){
    Dispose(true);
    GC.SuppressFinalize(this);
  }
  protected virtual void Dispose(bool disposing){
    //实际清理操作
  }
```
* 之所以提供一个虚方法,是因为考虑到该类型会被其他类型继承的情况.如果类型存在一个子类,子类也许会实现自己的Dispose模式.受保护的虚方法用来提醒子类,必须在实现自己的清理方法时注意到父类的清理工作,即子类需要在自己的释放方法里调用base.Dispose( )方法

#### 建议50：在Dispose模式中应区别对待托管资源和非托管资源
* 我们应该已经注意到了,在标准的Dispose模式中,真正撰写资源释放代码的那个虚方法是带有一个布尔参数的,这个布尔参数,是在释放资源时要区别对待托管资源和非托管资源 [ 是否要清理托管资源 ]
* 托管资源中的普通类型不需要手动清理,而非普通类型是需要手动清理的(即调用 Dispose( ) 方法)
```csharp
  protected virtual void Dispose(bool disposing)
  {
    if(disposing)
    {
      //清理托管资源
    }
    //清理非托管资源
  }
```
> Dispose模式设计思路基于：如果调用者显式的调用了Dispose( )方法,那么类型应该将资源全部释放.如果调用者忘记调用Dispose()方法,那么类型就会全部交给垃圾回收器回收[建议47],所以不进行手工清理

#### 建议51：具有可释放字段的类型或拥有本机资源的类型应该是可释放的
* 当类型拥有本机资源(即非托管类型资源),它也应该继承IDisposable接口

#### 建议52：及时释放资源
* 微软官方解释满足垃圾回收的条件
1. 系统具有极低的物理内存
2. 由托管堆上已分配的对象使用的内存超出了可接受的范围
3. 调用 GC.Collect( ) 方法
* 当一个实际已经没有用处的短期非托管对象,未满足以上条件时,它将一直占据着内存不放,对应用系统来说是一种极大的浪费.该浪费甚有可能会干扰程序的正常运行.应在使用完后主动调用 Dispose( ) 进行垃圾回收

#### 建议53：必要时应将不再使用的对象引用赋值为null
* 在实际编码中,感到自觉的静态引用类型占用的内存空间比较大时,并且用完后不再使用,便立刻可以将其赋值为null.也许非必要的,但这是一个好习惯
* 尽量少使用静态变量

#### 建议54：为无用字段标注不可序列化
* 有以下几方面的原因,决定了要为无用字段标注不可序列化
1. 节省空间  -类型在序列化后往往会存储在某个地方,如果一个字段在反序列化后不需要保持状态,那他就不应该被序列化,这会占用宝贵的空间资源
2. 反序列化后字段信息已经没有意义了   -如Windows内核句柄,在反序列化已失去意义,所以它不应该被序列化
3. 字段因为业务上的原因不允许被序列化   -例如明文密码不应该被序列化后一同保存在文件中
4. 如果字段本身所对应的类型未被设定为可序列化,那它j就该被标注为不可序列化,否则会抛出SerializationException

#### 建议55：利用定制特性减少可序列化的字段
* 在System.Runtime.Serialization命名空间下,有4个这样的特性,下面是MSDN上对它们的解释：
1. OnDeserializedAttribute,当它应用于某方法时,会指定在对象反序列化后立即调用此方法
2. OnDeserializingAttribute,当它应用于某方法时,会指定在反序列化对象时调用此方法
3. OnSerializedAttribute,如果将它应用于某方法,则应指定在序列化该对象后是否调用该方法
4. OnSerializingAttribute,如果将它应用于某方法,则应指定在序列化该对象前是否调用该方法
5. NonSerialized,如果将它应用于某个属性,则在序列化时忽略这个字段
> 利用这些特性,可以灵活地处理序列化和反序列化的过程

#### 建议56：使用继承ISerializable接口更灵活地控制序列化过程
* 当建议55的特性不能完全满足自定义序列化的要求时,那就需要继承ISerializable
* 继承ISerializable接口,它将会忽略掉类型所有的序列化特性,转而调用GetObjectData方法来处理序列化,在一个带参的构造方法中处理反序列化
```csharp
// 例
[Serializable]
public class Employee : ISerializable
{
  public string Name{get;set;}
  public int Age{get;set;}
  public string Description{get;set;}
  
  public Employee(){}
  //反序列化
  public Employee(SerializationInfo info ,StreamingContext context)
  {
    Name = info.GetString("Name");
    Age = info.GetInt32("Age");
    Description = string.Format("{0}{1}",Name,Age);
  }
  //序列化
  void ISerializable.GetObjectData(SerializationInfo info,StreamingContext context)
  {
    info.AddValue("Name",Name);
    info.AddValue("Age",Age);
  }
}
```
> 继承ISerializable可以完成特性做不到的功能,例如将Employee对象序列化,然后在反序列化中将其变成另外一个对象

#### 建议57：实现ISerializable的子类型应负责父类的序列化
* 子类应调用父类序列方法或自己实现父类的序列化实现


### 异常与自定义异常
---
#### 建议58：用抛出异常代替返回错误代码
* 当方法出现异常时需要更多细节时,就要与调用者约定更多的错误代码,会发现错误代码飞速膨胀无法维护.使用CLR异常机制,可以发现代码变得清晰、更易于理解
* 在catch中,所完成的功能通常是'通知',而不是'处理'.应保证catch和finally中的代码是可以被执行的.换句话说,尽量不要在catch和finally中让代码出错

#### 建议59：不要在不恰当的场合下引发异常
* 程序员,尤其是类库开发人员,要掌握的两条首要原则如下
1. 正常 '可控' 的业务流程不应使用异常来处理,应采用Tester-Doer验证处理
2. 不要总是尝试去捕获异常或引发异常,而应允许异常向调用堆栈往上传播
* 以下几类情况适合引发异常
1. 如果允许代码后造成内存泄露、资源不可用,或者应用程序状态不可恢复,则引发异常
2. 在捕获异常的时候,如果需要包装一些更有用的信息,则引发异常
3. 如果底层异常在高层操作的上下文中没有意义,则可以考虑捕获这些底层异常,并引发新的有意义的异常
> 当需要调用第三方api提供接口时,如果对方的异常报告机制使用的是错误代码,最好重新引发该接口提供的错误,因为你需要让自己的团队更好地理解这些错误

#### 建议60：重新引发异常时使用 Inner Exception
```csharp
  //将异常重新包装为一个Exception,并将SocketException作为Inner Exception(即err)向上传递
  catch(SocketException err)
  {
    throw new Exception("网络异常",err);
  }
```
```csharp
  //使用Exception.Data属性来返回额外信息
  catch(SocketException err)
  {
    err.Data.Add("SocketInfo","网络连接失败,请稍后重试");
    throw err; //向上层传递异常
  }
  //上层进行捕获,可以通过键值来得到异常信息
  catch(SocketException err)
  {
    Console.WriteLine(err.Data["SocketInfo"].ToString());
  }
```
> 以上代码可以让为异常提供额外的信息,有利于记录异常信息到日志,便于开发者分析具体的原因

#### 建议61：避免在Finally内撰写无效代码
* 在CLR中不存在一种打破try-finally执行顺序的情况(除非是程序在try退出或彻底停止)

```csharp
  //注意：以下代码返回的User值为 User { Name = 'Rose',BirthDay = 2010.2.2 } 
  private static User TestUserReturnInTry(){
      User user = new User(){ Name="Mike",BirthDay = new Date(2010,1,1) };
      try
      {
        return user;
      }
      finally
      {
        user.Name = "Rose";
        user.BirthDay = new DateTime(2010,2,2);
        user = null;
      }
  }
```
> 至于User的结果不为null,书上给出的解释如下:<br/>
当我们在方法内部new一个对象的时候,是创建了一个User对象并返回引用放置在栈上,然后在try块中,把刚保存的引用再放到栈上,并将其保存为另一个局部变量,这个变量才是最终要返回的User.由于两个变量指向的都是同一个对象,所以,只要对象的值在变动,返回值就会跟着变化
* 在CLR中,方法的参数及返回值都是用栈来保存的.在方法内部,会首先将参数依次压栈,当需要使用这些参数的时候,方法会直接去栈里取用参数值,方法返回时,会将返回值压入栈顶.如果参数的类型是值类型,压栈的就是复制的值.如果是引用类型,则在方法内对参数的修改也会带到方法外

#### 建议62：避免嵌套异常
* 在建议59中已经强调过,应允许异常在调用堆栈中往上传播,不要过多使用catch,然后再throw.过多使用catch会带来两个问题
1. 代码更多了.这看上去好像你根本不知道该怎么处理异常
2. 隐藏了堆栈信息,使你不知道真正发送异常的地方
* 当真的需要捕获这个异常来恢复一些状态,然后重新抛出,应该是这样的
```csharp
  try{ }
  catch (Exception)
  {
    throw
  }
  
  //而不是下面这样的
  try{ }
  catch (Exception err)
  {
    throw err;
  }
```
> 直接throw err而不是throw将会重置堆栈信息

#### 建议63：避免"吃掉"异常
* 如果你不知道如何处理某个异常,那么千万不要"吃掉"异常,如果你不小心"吃掉"了一个本该往上传递的异常,那么,这里可能诞生一个bug,而且,解决它会很费周折

#### 建议64：为循环增加Tester-Doer模式而不是将try-catch置于循环内
* 如果需要在循环中引发异常,你需要特别注意,因为抛出异常是一个相当影响性能的过程.应尽量在循环当中对异常发生的一些条件进行判断,然后根据条件进行处理

#### 建议65：总是处理未捕获的异常
* 处理未捕获的异常是每个应用程序应具备的基本功能,c#在AppDomain提供了UnhandledException事件来接收未捕获到的异常的通知

#### 建议66：正确捕获多线程中的异常
* 从.NET 2.0开始,任何线程上未处理的异常,都会导致应用程序的退出(先会触发AppDomain的UnhandledException)
```csharp
  //所以新起的异常捕获需要用try-catch在线程内部将代码包起来
  Thread t = new Thread((ThreadStart) delegate{
    try{
      throw new Exception("非窗体线程异常");
    }
    catch (Exception err)
    {
      Console.WriteLine("工作线程异常:{0}",err.Message);
    }
  });
  t.Start();
```
#### 建议67：慎用自定义异常
* 如果要对某类程序出错信息做特殊处理,那就自定义异常。需要自定义异常的理由如下
1. 方便调试.通过抛出一个自定义的异常实例,我们可以使捕获代码精确地知道所发生的事情,并以合适的方式进行恢复
2. 逻辑包装.自定义异常可包装多个其他异常,然后抛出一个业务异常
3. 方便调用者编码.在编写自己的类库或者业务层代码的时候,自定义异常可以让调用方便处理业务异常逻辑
4. 引入新异常类.这使程序员能够根据异常类在代码中采取不同的操作

#### 建议68：从System.Exception或其他常见的基本异常中派生异常
* 微软曾建议过.使用System.Application让用户抛出应用异常,使用System.SystemApplication来抛出CLR异常.遗憾的是,微软后来自己也没有遵循规则,导致它的这条建议仅仅作为建议存在过,而没有实际意义
* 现在,微软已修正这一点,当前的建议是:从System.Exception或其它常见基本异常之一派生异常

#### 建议69：应使用Finally避免资源泄露
* 应在finally块中进行垃圾回收或资源释放等处理防止资源泄露

#### 建议70：避免在调用栈较低的位置记录异常



















