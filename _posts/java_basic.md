# Java basic  
  
## 源文件规则  
  
* 一个源文件只能有一个public类    
* 一个源文件可以有多个非public类    
* 源文件名称应与public类的类名保持一致  
* 如果类定义在某个包中，则package语句应该放在首行    
* import 和 package对源文件定义的所有类有效，不能在同一源文件中给不同的类不同的包声明  
  
## 构造方法  
```java  
public class test{  
    private i;  
    {  
      //这里是构造代码块   
        }  
    public test(){  
        }  
    public test(int i){  
        //这个构造方法有个参数i  
	}  
}  
```  
每个类都有构造方法，如果没有显示的定义，java编译器将为该类提供一个默认构造方法  
{}为构造代码块,构造代码块中的内容在对象被初始化时调用    
构造方法与类名一样，可以有多个，对象初始化时候调用    
  
## 修饰符  
* private      
修饰member,只能被自身访问  
* public       
修饰类  
* protected    
修饰member,可以被自身，及自身子类访问  
* abstract    
可以修饰class,interface method，  
修饰class时，class可以包含未实现方法，不能被实例化，abstract class可以extend  
所有interface都是abstract的，interface声明abstract不是必须的  
abstract 方法不在声明它的类中实现，但必须在某个子类中重写。  
一旦类中包含了abstract方法，那类该类必须声明为abstract类。  
仅当 abstract 类的子类实现其超类的所有 abstract 方法时，才能实例化 abstract 类的子类。这种类称为具体类，以区别于 abstract 类。  
如果 abstract 类的子类没有实现其超类的所有 abstract 方法，该子类也是 abstract 类。  
abstract 关键字不能应用于 static、private 或 final 方法，因为这些方法不能被重写  
* final  
final 可以修饰class method field variable  
修饰class时，class不能有子类  
修饰method时，method不能被overridden  
  
* strictfp  
Interface The interface is accessible anywhere its package is.  
Member    The member is accessible anywhere its class is.  
Class     All methods of the class are implicitly strictfp.  
Method  
* static  
Class         An inner class declared static is a top-level class, not associated with amember of the containing class.  
Method    A static method is a class method. It is not passed an implicit this objectreference. It can be invoked through the class name.  
Field         A staticfield is a class field. There is only one instance of the field,regardless of the number of class instances created. It can be accessedthrough the class name.  
Initializer  The initializer is run when the class is loaded rather than when an instance iscreated.  
* synchronized  
Method    The method makes nonatomic modifications to the class or instance, so caremust be taken to ensure that two threads cannot modify the class or instanceat the same time. For a static method, a lock for the class is acquiredbefore executing the method. For a non-static method, a lock for thespecific object instance is acquired.  
* transient  
Field    The field is not part of the persistent state of the object and should not beserialized with the object. Used with object serialization; seejava.io.ObjectOutputStream.  
* volatile  
Field    The field can be accessed by unsynchronized threads, so certain optimizationsmust not be performed on it. This modifier can sometimes be used as analternative to synchronized.   
