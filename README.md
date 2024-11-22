
Java中，**代理模式**是一种常见的设计模式，用于为某对象提供一种代理，以控制对该对象的访问。根据代理类的实现方式，可以分为静态代理和动态代理。以下将分别介绍这两种方式，并进行对比分析。


## 静态代理


**静态代理**是指在编译时期就已经确定了代理类的实现。代理类需要实现与目标对象相同的接口，并持有目标对象的引用，通过代理对象调用目标对象的方法。


### 静态代理的实现步骤


1. 定义接口。
2. 编写接口的实现类（目标类）。
3. 编写代理类，实现接口并持有目标类的引用。
4. 在代理类中，通过目标类的引用调用实际的方法。


### 示例代码



```
// 定义接口
public interface Hello {
    void morning(String name);
}

// 目标类
public class HelloWorld implements Hello {
    @Override
    public void morning(String name) {
        System.out.println("Good morning, " + name);
    }
}

// 代理类
public class HelloProxy implements Hello {
    // 内部维护一个目标代理对象的属性字段
    private Hello target;
    public HelloProxy(Hello target) {
        this.target = target;
    }

    @Override
    public void morning(String name) {
        System.out.println("Before method invoke...");
        target.morning(name); // 调用目标类方法
        System.out.println("After method invoke...");
    }
}

// 测试静态代理
public class Main {
    public static void main(String[] args) {
        Hello target = new HelloWorld();       // 创建目标对象
        Hello proxy = new HelloProxy(target); // 创建代理对象
        proxy.morning("Bob");                 // 调用代理方法
    }
}

```

输出：



```
Before method invoke...
Good morning, Bob
After method invoke...

```

优点


* 结构简单，容易理解。
* 代理类可以扩展目标类的功能（如添加日志、权限校验等）。


缺点


* 每增加一个接口，都需要单独编写代理类，扩展性差。
* 代理类的维护工作量较大。


## 动态代理


**动态代理**是指在运行时动态生成代理类，而不需要提前定义实现类。它的实现主要依赖于Java的`java.lang.reflect.Proxy`和`InvocationHandler`。


### 动态代理的实现步骤


1. 定义接口。
2. 创建`InvocationHandler`接口的实现类，用来处理方法调用。
3. 使用`Proxy.newProxyInstance()`生成动态代理对象。


### 示例代码



```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
// 定义接口
public interface Hello {
    void morning(String name);
}
// 测试动态代理
public class Main {
    public static void main(String[] args) {
        // 创建InvocationHandler
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("Before method invoke...");
                if (method.getName().equals("morning")) {
                    System.out.println("Good morning, " + args[0]);
                }
                System.out.println("After method invoke...");
                return null;
            }
        };
        // 创建动态代理对象
        Hello hello = (Hello) Proxy.newProxyInstance(
            Hello.class.getClassLoader(),     // 类加载器
            new Class[] { Hello.class },      // 需要实现的接口
            handler                           // 方法调用处理器
        );
        // 调用代理方法
        hello.morning("Bob");
    }
}

```

输出



```
Before method invoke...
Good morning, Bob
After method invoke...

```

**动态代理的核心：**


1. **InvocationHandler**接口：定义了代理逻辑的核心。
2. **Proxy.newProxyInstance()**：负责在运行时动态生成代理类并返回代理实例。


动态代理本质上是JVM在运行时生成代理类的字节码，并加载到内存中。


## **静态代理与动态代理的对比**




| **特点** | **静态代理** | **动态代理** |
| --- | --- | --- |
| **代理类实现方式** | 编译时定义代理类 | 运行时动态生成代理类 |
| **对接口的支持** | 每个接口需要单独实现代理类 | 可通用，只需实现`InvocationHandler` |
| **代码扩展性** | 低，新增接口时需增加代理类 | 高，只需更改代理逻辑即可 |
| **性能开销** | 无需反射，性能较高 | 依赖反射调用，性能略低 |
| **应用场景** | 小型项目或接口稳定的场景 | 大型项目、动态扩展功能的场景 |


## **总结**


* **静态代理**适用于小型项目，或者代理类相对固定的场景。
* **动态代理**适用于需要动态扩展功能或减少重复代码的场景。
* **动态代理的灵活性更强**，尤其是在AOP（面向切面编程）框架中广泛使用，如Spring框架中的动态代理。


动态代理的强大之处在于，它将代理逻辑与具体实现解耦，使得代码更加灵活、可维护，同时提供了更高的复用性。


  * [静态代理](#%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86)
* [静态代理的实现步骤](#%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E7%9A%84%E5%AE%9E%E7%8E%B0%E6%AD%A5%E9%AA%A4)
* [示例代码](#%E7%A4%BA%E4%BE%8B%E4%BB%A3%E7%A0%81)
* [动态代理](#%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86)
* [动态代理的实现步骤](#%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E7%9A%84%E5%AE%9E%E7%8E%B0%E6%AD%A5%E9%AA%A4)
* [示例代码](#%E7%A4%BA%E4%BE%8B%E4%BB%A3%E7%A0%81-1)
* [静态代理与动态代理的对比](#%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E4%B8%8E%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E7%9A%84%E5%AF%B9%E6%AF%94):[豆荚加速器](https://yirou.org)
* [总结](#%E6%80%BB%E7%BB%93)

   \_\_EOF\_\_

       - **本文作者：** [Erosion2020](https://github.com)
 - **本文链接：** [https://github.com/erosion2020/p/18561350](https://github.com)
 - **关于博主：** 评论和私信会在第一时间回复。或者[直接私信](https://github.com)我。
 - **版权声明：** 本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！
 - **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。
     
