# Java高级

## 1.反射

> 在程序运行期间, 拿到对象的所有信息.

### 1.Class类

> 类似python的type, 用于创建类的类. 实例是类.

- 每加载一种类, JVM就为其创建一个`Class`类型的实例.
- 只有JVM能够创建`Class`的实例.
- JVM为加载的每个类都创建了对应的`Class`实例, 如果获取了这个`Class`实例, 就可以通过实例获取到对应的类信息. 这个方法就是反射.
- 获取`Class`实例的方式:
    - 1.类属性获取: `Class cls = String.class`
    - 2.实例`getClass`方法获取: `Class cls = s.getClass();`
    - 3.类名获取: `Class cls = Class.forName("java.lang.String");`

### 2.访问字段

- `Field cls.getField(name)`:根据字段名获取某个`public`的`field`.
    - 不存在时, 抛出`NoSuchFieldError`异常.
- `Field cls.getDeclareField(name)`: 根据字段名获取当前类(不是父类)的某个`field`. 
    - 不存在时, 抛出`NoSuchFieldError`异常.
- `Field cls.getFields()`: 获取所有的`public`的`field`.
- `Field[] cls.getDeclaredFields()`：获取当前类的所有`field`（不包括父类）
- 字段操作:
    - `f.get(object)`: 获取`object`对象字段`f`的值.
    - `f.set(obj, value)`: 设置`obj`对象字段`f`的值.
    - `f.setAccessible(true)`:  对象在使用时取消访问权限检查.

###  3.访问方法

- `Method getMethod(name, Class...)`：获取某个`public`的`Method`(包括父类).  **按声明顺序传入所需参数类型的`Class`**.
    - 不存在时, 抛出`NoSuchMethodException`异常.
- `Method getDeclaredMethod(name, Class...)`：获取当前类的某个`Method`（不包括父类）. **按声明顺序传入所需参数类型的`Class`**.
    - 不存在时, 抛出`NoSuchMethodException`异常.
- `Method[] getMethods()`：获取所有`public`的`Method`（包括父类）
- `Method[] getDeclaredMethods()`：获取当前类的所有`Method`（不包括父类）
- 方法的执行:
    - `m.invoke(obj, 参数)`: 

### 4.构造方法

- `java.lang.reflect.Constructor`: 包括构造方法的所有信息.
    - 通过`cons.newInstance()`创建实例.
- `getConstructor(Class...)`：获取某个`public`的`Constructor`；**按声明顺序传入所需参数类型的`Class`**.
- `getDeclaredConstructor(Class...)`：获取某个`Constructor`；**按声明顺序传入所需参数类型的`Class`**.
- `getConstructors()`：获取所有`public`的`Constructor`；
- `getDeclaredConstructors()`：获取所有`Constructor`。

### 5.获取父类或接口

- `getSuperclass()`: 获取父类的class
- `getInterfaces()`: 获取接口类

### 6.获取注解

- 判断注解`Class`是否存在:
    - `Class.isAnnotationPresent(Class)`
    - `Field.isAnnotationPresent(Class)`
    - `Method.isAnnotationPresent(Class)`
    - `Constructor.isAnnotationPresent(Class)`
- 获取注解:
    - `Class.getAnnotation(Class)`
    - `Field.getAnnotation(Class)`
    - `Method.getAnnotation(Class)`
    - `Constructor.getAnnotation(Class)`

### 7.动态代理

> `Proxy`: 提供了`newProxyInstance`静态方法, 用于创建代理对象的实例.
>
> `InvocationHandler`: 接口, 定义了`invoke`方法. 用于扩展被代理对象的方法.

- `Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)`
    - 在运行期间动态创建某个`interface`的实例.
    - `loader`: 负责加载类, 可以由`Class.getClassLoader()`获取.
    - `interfaces`: 接口类.
    - `h`: `InvocationHandler`实例, 实现`invoke`方法, 对原有对象(被代理对象)方法进行扩展. 
- `InvocationHandler`: 负责实现接口的方法调用. 通过`invoke(Object proxy, Method method, Object[] args`方法, 调用(通过反射调用, `method.invoke`)并扩展被代理对象的方法.
- 动态代理实际上是JDK在运行期间创建class字节码并加载的过程.

## 2.泛型

>  泛型就是定义一种模板，例如`ArrayList`，然后在代码中为用到的类创建对应的`ArrayList<类型>`, 由编译器针对类型做检查.

- 编译后, 程序会采用去泛型化的措施.
- 

## 3.注解

> 放在类, 方法, 字段, 构造函数前的一种特殊注释或标记. 提供给IDE, 底层库, 或者在程序运行时使用.

### 1.注解的使用

- 使用时可以传递参数, 参数必须是常量(保证在**定义时就已确定了每个参数的值**).
- 对于`RetentionPolicy.RUNTIME`的注解, 可以使用反射, 获取到注解或者判断某元素上是否存在该注解.
    - 根据注解类型和注解参数, 执行对应逻辑代码.
    - 通过`cls.参数方法()`可以获取到使用注解出设置的参数.

### 2.定义

- 基本语法

    - ```java
        @Target(ElementType.Report)
        @Retention(RetentionPolicy.RUNTIME)
        public @interface Report{
            int min() default 1;
            int max() default 100;
        }
        ```

- 元注解 : 可以修饰注解的注解

    - `@Target`: 注解应用代码位置
        - 类或接口：`ElementType.TYPE`；
        - 字段：`ElementType.FIELD`；
        - 方法：`ElementType.METHOD`；
        - 构造方法：`ElementType.CONSTRUCTOR`；
        - 方法参数：`ElementType.PARAMETER`。
    - `@Retention`: 注解的生命周期
        - 仅编译期：`RetentionPolicy.SOURCE`；
        - 仅class文件：`RetentionPolicy.CLASS`；
        - 运行期：`RetentionPolicy.RUNTIME`。
    - `@Repeatable`: 是否可重复使用
    - `@Inherited`: 子类可以继承父类的注解.

- 参数定义
  
    - 参数类似无参方法,  可以使用`default`设置默认值.`int max() default 0;`