---
layout: post
title: "JAVA动态代理"
categories:
- java

tags:
- 动态代理

---
##代理设计模式
定义：为其他对象提供一种代理以控制对这个对象的访问。

代理模式的结构如下图所示。
![代理模式](/assets/images/JavaProxy.jpg "代理模式类图")

##动态代理使用
java动态代理机制以巧妙的方式实现了代理模式的设计理念。
![动态代理类图](/assets/images/javaproxyclass.jpg "动态代理类图")

代理模式示例代码
{% highlight java %}
public interface Subject   
{   
  public void doSomething();   
}   
public class RealSubject implements Subject   
{   
  public void doSomething()   
  {   
    System.out.println( "call doSomething()" );   
  }   
}   
public class ProxyHandler implements InvocationHandler   
{   
  private Object proxied;   
     
  public ProxyHandler( Object proxied )   
  {   
    this.proxied = proxied;   
  }   
     
  public Object invoke( Object proxy, Method method, Object[] args ) throws Throwable   
  {   
	//在转调具体目标对象之前，可以执行一些功能处理

	//转调具体目标对象的方法
    return method.invoke( proxied, args);  
    
	//在转调具体目标对象之后，可以执行一些功能处理
  }    
} 
{% endhighlight %}

{% highlight java %}
import java.lang.reflect.InvocationHandler;   
import java.lang.reflect.Method;   
import java.lang.reflect.Proxy;   
import sun.misc.ProxyGenerator;   
import java.io.*;   
public class DynamicProxy   
{   
  public static void main( String args[] )   
  {   
    RealSubject real = new RealSubject();   
    Subject proxySubject = (Subject)Proxy.newProxyInstance(Subject.class.getClassLoader(), 
     new Class[]{Subject.class}, 
     new ProxyHandler(real));
         
    proxySubject.doSomething();
   
    //write proxySubject class binary data to file   
    createProxyClassFile();   
  }   
     
  public static void createProxyClassFile()   
  {   
    String name = "ProxySubject";   
    byte[] data = ProxyGenerator.generateProxyClass( name, new Class[] { Subject.class } );   
    try  
    {   
      FileOutputStream out = new FileOutputStream( name + ".class" );   
      out.write( data );   
      out.close();   
    }   
    catch( Exception e )   
    {   
      e.printStackTrace();   
    }   
  }   
}  
{% endhighlight %}

##动态代理内部实现
首先来看看类**Proxy**的代码实现
**Proxy**的主要静态变量
{% highlight java %}
// 映射表：用于维护类装载器对象到其对应的代理类缓存
private static Map loaderToCache = new WeakHashMap(); 

// 标记：用于标记一个动态代理类正在被创建中
private static Object pendingGenerationMarker = new Object(); 

// 同步表：记录已经被创建的动态代理类类型，主要被方法 isProxyClass 进行相关的判断
private static Map proxyClasses = Collections.synchronizedMap(new WeakHashMap()); 

// 关联的调用处理器引用
protected InvocationHandler h;
{% endhighlight %}
**Proxy**的构造方法
{% highlight java %}
// 由于 Proxy 内部从不直接调用构造函数，所以 private 类型意味着禁止任何调用
private Proxy() {} 

// 由于 Proxy 内部从不直接调用构造函数，所以 protected 意味着只有子类可以调用
protected Proxy(InvocationHandler h) {this.h = h;} 
{% endhighlight %}
**Proxy**静态方法**newProxyInstance**
{% highlight java %}
public static Object newProxyInstance(ClassLoader loader, Class<?>[]interfaces,InvocationHandler h) throws IllegalArgumentException { 
    // 检查 h 不为空，否则抛异常
    if (h == null) { 
        throw new NullPointerException(); 
    } 

    // 获得与制定类装载器和一组接口相关的代理类类型对象
    Class cl = getProxyClass(loader, interfaces); 

    // 通过反射获取构造函数对象并生成代理类实例
    try { 
        Constructor cons = cl.getConstructor(constructorParams); 
        return (Object) cons.newInstance(new Object[] { h }); 
    } catch (NoSuchMethodException e) { throw new InternalError(e.toString()); 
    } catch (IllegalAccessException e) { throw new InternalError(e.toString()); 
    } catch (InstantiationException e) { throw new InternalError(e.toString()); 
    } catch (InvocationTargetException e) { throw new InternalError(e.toString()); 
    } 
}
{% endhighlight %}

类**Proxy**的**getProxyClass**方法调用**ProxyGenerator**的 **generateProxyClass**方法产生ProxySubject.class的二进制数据：
{% highlight java %}
public static byte[] generateProxyClass(final String name, Class[] interfaces)
{% endhighlight %}
我们可以**import sun.misc.ProxyGenerator**，调用 **generateProxyClass**方法产生binary data，然后写入文件，最后通过反编译工具来查看内部实现原理。
反编译后的**ProxySubject.java**
**Proxy**静态方法**newProxyInstance**
{% highlight java %}
import java.lang.reflect.*;   
public final class ProxySubject extends Proxy   
    implements Subject   
{   
    private static Method m1;   
    private static Method m0;   
    private static Method m3;   
    private static Method m2;   
    public ProxySubject(InvocationHandler invocationhandler)   
    {   
        super(invocationhandler);   
    }   
    public final boolean equals(Object obj)   
    {   
        try  
        {   
            return ((Boolean)super.h.invoke(this, m1, new Object[] {   
                obj   
            })).booleanValue();   
        }   
        catch(Error _ex) { }   
        catch(Throwable throwable)   
        {   
            throw new UndeclaredThrowableException(throwable);   
        }   
    }   
    public final int hashCode()   
    {   
        try  
        {   
            return ((Integer)super.h.invoke(this, m0, null)).intValue();   
        }   
        catch(Error _ex) { }   
        catch(Throwable throwable)   
        {   
            throw new UndeclaredThrowableException(throwable);   
        }   
    }   
    public final void doSomething()   
    {   
        try  
        {   
            super.h.invoke(this, m3, null);   
            return;   
        }   
        catch(Error _ex) { }   
        catch(Throwable throwable)   
        {   
            throw new UndeclaredThrowableException(throwable);   
        }   
    }   
    public final String toString()   
    {   
        try  
        {   
            return (String)super.h.invoke(this, m2, null);   
        }   
        catch(Error _ex) { }   
        catch(Throwable throwable)   
        {   
            throw new UndeclaredThrowableException(throwable);   
        }   
    }   
    static    
    {   
        try  
        {   
            m1 = Class.forName("java.lang.Object").getMethod("equals", new Class[] {   
                Class.forName("java.lang.Object")   
            });   
            m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class[0]);   
            m3 = Class.forName("Subject").getMethod("doSomething", new Class[0]);   
            m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);   
        }   
        catch(NoSuchMethodException nosuchmethodexception)   
        {   
            throw new NoSuchMethodError(nosuchmethodexception.getMessage());   
        }   
        catch(ClassNotFoundException classnotfoundexception)   
        {   
            throw new NoClassDefFoundError(classnotfoundexception.getMessage());   
        }   
    }   
}  
{% endhighlight %}

[ProxyGenerator](http://www.docjar.com/html/api/sun/misc/ProxyGenerator.java.html)内部是如何生成class二进制数据，可以参考源代码。
{% highlight java %}
private byte[] generateClassFile() {   
  /*  
   * Record that proxy methods are needed for the hashCode, equals,  
   * and toString methods of java.lang.Object.  This is done before  
   * the methods from the proxy interfaces so that the methods from  
   * java.lang.Object take precedence over duplicate methods in the  
   * proxy interfaces.  
   */  
  addProxyMethod(hashCodeMethod, Object.class);   
  addProxyMethod(equalsMethod, Object.class);   
  addProxyMethod(toStringMethod, Object.class);   
  /*  
   * Now record all of the methods from the proxy interfaces, giving  
   * earlier interfaces precedence over later ones with duplicate  
   * methods.  
   */  
  for (int i = 0; i < interfaces.length; i++) {   
      Method[] methods = interfaces[i].getMethods();   
      for (int j = 0; j < methods.length; j++) {   
    addProxyMethod(methods[j], interfaces[i]);   
      }   
  }   
  /*  
   * For each set of proxy methods with the same signature,  
   * verify that the methods' return types are compatible.  
   */  
  for (List<ProxyMethod> sigmethods : proxyMethods.values()) {   
      checkReturnTypes(sigmethods);   
  }   
  /* ============================================================  
   * Step 2: Assemble FieldInfo and MethodInfo structs for all of  
   * fields and methods in the class we are generating.  
   */  
  try {   
      methods.add(generateConstructor());   
      for (List<ProxyMethod> sigmethods : proxyMethods.values()) {   
    for (ProxyMethod pm : sigmethods) {   
        // add static field for method's Method object   
        fields.add(new FieldInfo(pm.methodFieldName,   
      "Ljava/lang/reflect/Method;",   
       ACC_PRIVATE | ACC_STATIC));   
        // generate code for proxy method and add it   
        methods.add(pm.generateMethod());   
    }   
      }   
      methods.add(generateStaticInitializer());   
  } catch (IOException e) {   
      throw new InternalError("unexpected I/O Exception");   
  }   
  /* ============================================================  
   * Step 3: Write the final class file.  
   */  
  /*  
   * Make sure that constant pool indexes are reserved for the  
   * following items before starting to write the final class file.  
   */  
  cp.getClass(dotToSlash(className));   
  cp.getClass(superclassName);   
  for (int i = 0; i < interfaces.length; i++) {   
      cp.getClass(dotToSlash(interfaces[i].getName()));   
  }   
  /*  
   * Disallow new constant pool additions beyond this point, since  
   * we are about to write the final constant pool table.  
   */  
  cp.setReadOnly();   
  ByteArrayOutputStream bout = new ByteArrayOutputStream();   
  DataOutputStream dout = new DataOutputStream(bout);   
  try {   
      /*  
       * Write all the items of the "ClassFile" structure.  
       * See JVMS section 4.1.  
       */  
          // u4 magic;   
      dout.writeInt(0xCAFEBABE);   
          // u2 minor_version;   
      dout.writeShort(CLASSFILE_MINOR_VERSION);   
          // u2 major_version;   
      dout.writeShort(CLASSFILE_MAJOR_VERSION);   
      cp.write(dout);   // (write constant pool)   
          // u2 access_flags;   
      dout.writeShort(ACC_PUBLIC | ACC_FINAL | ACC_SUPER);   
          // u2 this_class;   
      dout.writeShort(cp.getClass(dotToSlash(className)));   
          // u2 super_class;   
      dout.writeShort(cp.getClass(superclassName));   
          // u2 interfaces_count;   
      dout.writeShort(interfaces.length);   
          // u2 interfaces[interfaces_count];   
      for (int i = 0; i < interfaces.length; i++) {   
    dout.writeShort(cp.getClass(   
        dotToSlash(interfaces[i].getName())));   
      }   
          // u2 fields_count;   
      dout.writeShort(fields.size());   
          // field_info fields[fields_count];   
      for (FieldInfo f : fields) {   
    f.write(dout);   
      }   
          // u2 methods_count;   
      dout.writeShort(methods.size());   
          // method_info methods[methods_count];   
      for (MethodInfo m : methods) {   
    m.write(dout);   
      }   
             // u2 attributes_count;   
      dout.writeShort(0); // (no ClassFile attributes for proxy classes)   
  } catch (IOException e) {   
      throw new InternalError("unexpected I/O Exception");   
  }   
  return bout.toByteArray();  
{% endhighlight %}

##美中不足
诚然，**Proxy**已经设计得非常优美，但是还是有一点点小小的遗憾之处，那就是它始终无法摆脱仅支持**interface**代理的桎梏，因为它的设计注定了这个遗憾。回想一下那些动态生成的代理类的继承关系图，它们已经注定有一个共同的父类叫**Proxy**。Java的继承机制注定了这些动态代理类们无法实现对class的动态代理，原因是多继承在Java中本质上就行不通。有很多条理由，人们可以否定对 class代理的必要性，但是同样有一些理由，相信支持class动态代理会更美好。接口和类的划分，本就不是很明显，只是到了Java中才变得如此的细化。如果只从方法的声明及是否被定义来考量，有一种两者的混合体，它的名字叫抽象类。实现对抽象类的动态代理，相信也有其内在的价值。此外，还有一些历史遗留的类，它们将因为没有实现任何接口而从此与动态代理永世无缘。如此种种，不得不说是一个小小的遗憾。但是，不完美并不等于不伟大，伟大是一种本质，Java动态代理就是佐例。

##参考资料
1、[JDK动态代理实现原理](http://rejoy.iteye.com/blog/1627405)

2、[Java动态代理机制分析及扩展](http://www.ibm.com/developerworks/cn/java/j-lo-proxy1/index.html)

