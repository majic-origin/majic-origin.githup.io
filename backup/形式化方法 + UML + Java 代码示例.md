### 一、什么是形式化方法
形式化方法是基于严格数学逻辑的系统设计、分析与验证方法，核心是用无歧义的数学模型、形式化语言来描述系统需求、验证系统行为，避免自然语言描述的模糊性，保障系统的正确性、安全性。
它主要分为两个部分：
形式化规格说明：用数学语言精准定义系统 “要做什么”，比如用逻辑公式、状态机描述系统的约束与功能。
形式化验证：通过数学证明、模型检测等手段，验证系统是否满足规格说明，排查潜在的逻辑漏洞。
形式化方法常用于高可靠性场景（航空航天、芯片、金融系统），在软件开发中，也可以用它来提前验证业务逻辑的正确性，再落地编码。
### 二、形式化方法的 Java 代码示例
我们用一个简单的银行账户取款场景来演示：
先通过形式化逻辑定义约束，再用 Java 代码实现并验证。
1. 形式化规格说明（数学逻辑描述）
设账户余额为balance，取款金额为amount，需要满足：
前置条件：amount > 0 且 balance >= amount
后置条件：balance' = balance - amount（balance'为取款后余额）
不变量：balance >= 0（账户余额永远非负）



```
`public class BankAccount {
    private double balance;

    public BankAccount(double initialBalance) {
        // 初始化时保证余额非负
        if (initialBalance < 0) {
            throw new IllegalArgumentException("初始余额不能为负");
        }
        this.balance = initialBalance;
    }

    /**
     * 取款方法
     * @param amount 取款金额
     * @throws IllegalArgumentException 违反形式化约束时抛出异常
     */
    public void withdraw(double amount) {
        // 对应前置条件：amount > 0
        if (amount <= 0) {
            throw new IllegalArgumentException("取款金额必须大于0");
        }
        // 对应前置条件：balance >= amount
        if (balance < amount) {
            throw new IllegalArgumentException("余额不足，无法取款");
        }

        // 执行取款操作
        balance -= amount;

        // 验证不变量：balance >= 0（形式化验证的代码落地）
        if (balance < 0) {
            throw new IllegalStateException("账户余额异常，出现负数");
        }
    }

    public double getBalance() {
        return balance;
    }

    public static void main(String[] args) {
        BankAccount account = new BankAccount(1000);
        System.out.println("初始余额：" + account.getBalance());

        // 合法取款
        account.withdraw(300);
        System.out.println("取款300后余额：" + account.getBalance());

        // 非法取款（金额为负）
        try {
            account.withdraw(-100);
        } catch (IllegalArgumentException e) {
            System.out.println("取款失败：" + e.getMessage());
        }

        // 非法取款（余额不足）
        try {
            account.withdraw(800);
        } catch (IllegalArgumentException e) {
            System.out.println("取款失败：" + e.getMessage());
        }
    }
}`



```
### 三、《大象 ——thinking in UML》核心内容 + UML 转 Java 代码示例
《大象 ——thinking in UML》是 UML（统一建模语言）的经典入门书，核心是教读者用 UML 图做面向对象系统建模，再把模型落地为代码。
1. 核心 UML 图（书中重点）
用例图：描述系统功能与用户的交互
类图：描述系统的类结构、关系（继承、关联、依赖）
时序图：描述对象之间的交互流程
状态图：描述对象的状态变化
2. UML 类图转 Java 代码示例
我们用一个简单的 **"学生 - 课程" 关联 ** 的类图（书中常见的建模场景），对应 Java 代码实现：
UML 类图逻辑（文字描述）
类Student：属性id、name；方法study()、attendCourse()
类Course：属性courseId、courseName；方法getInfo()
关联关系：Student与Course是多对多（一个学生选多门课，一门课有多个学生）

> 

> `

```
import java.util.List;
import java.util.ArrayList;

// Student类（对应UML类图的Student类）
public class Student {
    private String id;
    private String name;
    private List<Course> courses; // 多对多关联

    public Student(String id, String name) {
        this.id = id;
        this.name = name;
        this.courses = new ArrayList<>();
    }

    // 对应UML方法：attendCourse()
    public void attendCourse(Course course) {
        if (!courses.contains(course)) {
            courses.add(course);
            course.addStudent(this); // 维护双向关联
        }
    }

    // 对应UML方法：study()
    public void study() {
        System.out.println("学生 " + name + " 正在学习");
    }

    public String getName() {
        return name;
    }

    public List<Course> getCourses() {
        return courses;
    }
}

```
// Course类（对应UML类图的Course类）
// 测试类

```
import java.util.ArrayList;
import java.util.List;

public class Course {
    private String courseId;
    private String courseName;
    private List<Student> students; // 多对多关联

    public Course(String courseId, String courseName) {
        this.courseId = courseId;
        this.courseName = courseName;
        this.students = new ArrayList<>();
    }

    // 维护双向关联
    public void addStudent(Student student) {
        if (!students.contains(student)) {
            students.add(student);
        }
    }

    // 对应UML方法：getInfo()
    public String getInfo() {
        return "课程：" + courseName + "（编号：" + courseId + "）";
    }

    public List<Student> getStudents() {
        return students;
    }
}

```

```
public class UmlDemo {
    public static void main(String[] args) {
        Student stu1 = new Student("S001", "张三");
        Student stu2 = new Student("S002", "李四");

        Course course1 = new Course("C001", "Java编程");
        Course course2 = new Course("C002", "UML建模");

        // 学生选课
        stu1.attendCourse(course1);
        stu1.attendCourse(course2);
        stu2.attendCourse(course1);

        // 测试方法
        System.out.println(course1.getInfo() + " 选课学生：");
        for (Student s : course1.getStudents()) {
            System.out.println("- " + s.getName());
        }

        stu1.study();
    }
}
```
**### 最终总结**
形式化方法：基于数学逻辑的系统验证方法，核心是用严格约束保障系统正确性，可通过 Java 代码落地业务逻辑校验。
《大象 ——thinking in UML》：UML 建模经典书籍，核心是用 UML 图做面向对象建模，再将模型转化为 Java 等代码实现。