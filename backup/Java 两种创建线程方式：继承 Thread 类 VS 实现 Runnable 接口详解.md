### 一、两种实现代码示例
**1. 方式 1：继承 Thread 子类创建线程**
```
// 1.自定义类继承Thread类
class MyThread extends Thread{
    // 重写run()：线程执行任务方法
    @Override
    public void run() {
        for(int i=0;i<5;i++){
            System.out.println("继承Thread线程执行："+i);
        }
    }
}

public class TestThread{
    public static void main(String[] args) {
        // 创建线程对象 + 调用start()开启线程
        MyThread t1 = new MyThread();
        t1.start();
    }
}

```

2. 方式 2：实现 Runnable 接口，传入 Thread 构造器创建线程


```

// 1.自定义类实现Runnable接口
class MyRunnable implements Runnable{
    @Override
    public void run() {
        for(int i=0;i<5;i++){
            System.out.println("实现Runnable线程执行："+i);
        }
    }
}

public class TestRunnable{
    public static void main(String[] args) {
        // 任务对象
        MyRunnable task = new MyRunnable();
        // 把任务传入Thread，创建线程
        Thread t1 = new Thread(task);
        t1.start();
    }
}

```

### **二、核心五大区别**

1. 继承限制不同（最关键）
继承 Thread：Java 单继承局限
已经继承Thread，无法再继承其他父类，扩展性差。
实现 Runnable：无继承限制
只是实现接口，本类还可以正常继承别的父类，规避单继承缺陷，项目首选。
2. 资源共享能力不同
Thread 继承：多个线程无法共享同一个任务资源
每个new MyThread()都是独立对象，成员变量各自一份，很难共享数据。
Runnable 实现：多线程共用同一个任务对象，轻松共享资源
只创建 1 个MyRunnable任务，丢给多个Thread，多个线程共用同一份成员变量，适合多线程卖票、资源争抢场景。

```
// 共享资源示例：3个线程共用同一个任务
MyRunnable ticketTask = new MyRunnable();
Thread t1=new Thread(ticketTask);
Thread t2=new Thread(ticketTask);
Thread t3=new Thread(ticketTask);
```
3. 职责拆分不同
Thread 方式：线程对象 + 任务代码耦合在一起，线程和业务任务绑定。
Runnable 方式：解耦！任务（Runnable）与线程（Thread）分离，任务单独封装，同一个任务可以随时交给任意线程执行，符合面向对象设计。
4. 代码复用性
Thread：任务绑定线程，复用差。
Runnable：任务是独立接口实现类，可复用在线程池、定时任务等多处。
5. 底层原理
Thread：重写自身run()方法，源码Thread.run()默认调用内部 target (Runnable) 的 run，子类重写直接覆盖。
Runnable：任务封装在 Runnable 的 run，Thread 执行时调用传入 target.run ()。
三、开发选型结论
日常项目、多线程共享资源 → 优先：实现 Runnable 接口（工业标准写法）
仅简单独立任务、无需继承其他类，才偶尔使用继承 Thread。


### **四、拓展：Lambda 简化 Runnable 写法（JDK8+）**

```
// 一行代码快速创建线程
new Thread(()->{
    System.out.println("lambda简写Runnable");
}).start();

```
