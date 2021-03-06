####为什么要实现Serializable?

最重要的两个原因是：
　　1、将对象的状态保存在存储媒体中以便可以在以后重新创建出完全相同的副本；
　　2、按值将对象从一个应用程序域发送至另一个应用程序域。
        通俗的说：在分布式应用中，你就得实现序列化，如果你不需要分布式应用，那就没那个必要实现序列化。

拓展：
`Serializable`是一个空接口，没有什么具体内容，它的目的只是简单的标识一个类的对象可以被序列化。

>>什么情况下需要序列化
>* a）当你**想把的内存中的对象写入到硬盘**的时候；
>* b）当你想用套接字在**网络上传送对象**的时候；
>* c）当你想通过RMI传输对象的时候；再稍微解释一下:
>    * a)比如说你的内存不够用了，那计算机就要将内存里面*的一部分对象暂时的保存到硬盘中，等到要用的时候再读入到内存中*，硬盘的那部分存储空间就是所谓的[虚拟内存](https://www.baidu.com/s?wd=%E8%99%9A%E6%8B%9F%E5%86%85%E5%AD%98&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YvmhDYmvndrHR4nj-BnWD30ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPHmknjfkn1cd)。在比如过你要将某个特定的对象保存到文件中，我隔几天在把它拿出来用，那么这时候就要实现Serializable接口；
  >  -* b)在进行java的**Socket**编程的时候，你有时候可能要传输某一类的对象，那么也就要实现Serializable接口；最常见的你传输一个字符串，它是JDK里面的类，也实现了Serializable接口，所以可以在网络上传输。
>    ** c)如果要通过远程的方法调用（RMI）去调用一个远程对象的方法，如在计算机A中调用另一台计算机B的对象的方法，那么你需要通过JNDI服务获取计算机B目标对象的引用，将对象从B传送到A，就需要实现序列化接口。
####底层实现原理
  ```
Foo  myFoo = new Foo();  
myFoo .setWidth(37);  
myFoo.setHeight(70); 
 ```
 当 通过下面的代码序列化之后，MyFoo对象中的width和Height实例变量的值（37，70）都被保存到foo.ser文件中，这样以后又可以把它 从文件中读出来，重新在堆中创建原来的对象。当然保存时候不仅仅是保存对象的实例变量的值，JVM还要保存一些小量信息，比如类的类型等以便恢复原来的对象。
```
FileOutputStream fs = new FileOutputStream("foo.ser");  
ObjectOutputStream os = new ObjectOutputStream(fs);  
os.writeObject(myFoo);
```
######大概步骤
1.  Make a FileOutputStream      
`FileOutputStream fs = new FileOutputStream("foo.ser"); `
2.  Make a ObjectOutputStream  
`ObjectOutputStream os =  new ObjectOutputStream(fs);   `
3. write the object
`os.writeObject(myObject1);  
os.writeObject(myObject2);  
os.writeObject(myObject3);  `
4. close the ObjectOutputStream
`os.close();`

>  serialVersionUID适用于Java的序列化机制。简单来说，Java的序列化机制是通过判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常，即是InvalidCastException。

* serialVersionUID有两种显示的生成方式：        
  *   一是默认的1L，比如：private static final long serialVersionUID = 1L;        
  * 二是根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段，比如：        
private static final  long   serialVersionUID = xxxxL;
