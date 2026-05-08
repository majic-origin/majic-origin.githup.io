### 一、用处 & 优势
💡 用处

1. Java 反射（Reflection）是指：程序在运行时，获取 / 操作任意类的信息。
2. 它可以做到：
3. 在运行时，拿到一个类的所有属性、方法、构造器；
4. 在运行时，创建对象、调用方法、修改属性；
5. 实现框架 / 工具的通用逻辑（如 Spring、MyBatis、ORM 框架）。

✅ 优势

1. 高度灵活：可以不依赖编译期的代码，动态创建对象、调用方法；
2. 框架核心基础：几乎所有主流 Java 框架（Spring、Hibernate 等）都用反射实现依赖注入、ORM 映射；
3. 扩展性强：可以在不修改源码的情况下，通过配置文件动态加载类；
4. 适合做工具类：通用序列化、JSON 解析、对象拷贝等工具都依赖反射。

三、Java 反射完整代码示例
我们用一个 User 类，演示反射的基本用法：

```
package reflect;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

// 目标类：一个普通的User类
class User {
    // 私有属性
    private String name;
    private int age;

    // 无参构造器
    public User() {
        System.out.println("无参构造器执行");
    }

    // 有参构造器
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 普通方法
    public void sayHello() {
        System.out.println("大家好，我是" + name + "，今年" + age + "岁");
    }

    // 私有方法
    private void privateMethod() {
        System.out.println("这是一个私有方法，通过反射调用了");
    }

    // getter/setter
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

public class ReflectionDemo {
    public static void main(String[] args) throws Exception {
        // --------------------------
        // 1. 获取Class对象（反射的入口）
        // --------------------------
        // 方式1：类名.class（推荐）
        Class<User> clazz1 = User.class;
        // 方式2：对象.getClass()
        User user = new User();
        Class<? extends User> clazz2 = user.getClass();
        // 方式3：Class.forName("全类名")
        Class<?> clazz3 = Class.forName("reflect.User");

        System.out.println("Class对象：" + clazz1.getName());

        // --------------------------
        // 2. 通过反射创建对象
        // --------------------------
        // 无参构造器创建对象
        User user1 = clazz1.newInstance(); // 老版本写法
        System.out.println("user1：" + user1);

        // 获取有参构造器，创建对象
        Constructor<User> constructor = clazz1.getConstructor(String.class, int.class);
        User user2 = constructor.newInstance("张三", 20);
        System.out.println("user2：" + user2);

        // --------------------------
        // 3. 获取并调用方法
        // --------------------------
        // 获取sayHello方法
        Method sayHelloMethod = clazz1.getMethod("sayHello");
        // 调用方法
        sayHelloMethod.invoke(user2);

        // 获取并调用私有方法
        Method privateMethod = clazz1.getDeclaredMethod("privateMethod");
        privateMethod.setAccessible(true); // 暴力访问，跳过权限检查
        privateMethod.invoke(user2);

        // --------------------------
        // 4. 获取并修改属性
        // --------------------------
        // 获取私有属性name
        Field nameField = clazz1.getDeclaredField("name");
        nameField.setAccessible(true); // 暴力访问
        nameField.set(user2, "李四"); // 修改属性值

        Field ageField = clazz1.getDeclaredField("age");
        ageField.setAccessible(true);
        ageField.set(user2, 25);

        // 再次调用方法，验证修改结果
        sayHelloMethod.invoke(user2);
    }
}

```


1. 获取 Class 对象
Class 对象是反射的入口，它代表了一个类在 JVM 中的元数据信息。
java
运行
```
Class<User> clazz1 = User.class; // 方式1：类名.class
Class<?> clazz3 = Class.forName("reflect.User"); // 方式3：通过全类名加载
```
2. 创建对象
clazz.newInstance()：调用无参构造器创建对象；
clazz.getConstructor(参数类型.class)：获取指定参数的构造器；
constructor.newInstance(参数)：调用构造器创建对象。
3. 调用方法
getMethod("方法名", 参数类型.class)：获取public方法；
getDeclaredMethod("方法名", 参数类型.class)：获取所有方法（包括 private）；
method.invoke(对象, 参数)：调用方法；
setAccessible(true)：暴力访问，跳过权限检查，才能调用 private 方法 / 属性。
4. 修改属性
getDeclaredField("属性名")：获取所有属性（包括 private）；
field.setAccessible(true)：暴力访问；
field.set(对象, 值)：修改属性值。
