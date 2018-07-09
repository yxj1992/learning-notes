### 如何创建并运行java线程  

Java线程类也是一个object类,它的实例都继承自java.lang.Thread或其子类。 可以用如下方式用java中创建一个线程：  
```  
Tread thread = new Thread();
```  
执行该线程可以调用该线程的start()方法:  
```
thread.start();
```  

在上面的例子中，我们并没有为线程编写运行代码，因此调用该方法后线程就终止了。  

编写线程运行时执行的代码有两种方式：一种是创建Thread子类的一个实例并重写run方法，第二种是创建类的时候实现Runnable接口。接下来我们会具体讲解这两种方法：  

#### 创建Thread的子类  

创建Thread子类的一个实例并重写run方法，run方法会在调用start()方法之后被执行。例子如下：  
```
public class MyThread extends Thread {
   public void run(){
     System.out.println("MyThread running");
   }
}
```  
可以用如下方式创建并运行上述Thread子类  
```
MyThread myThread = new MyThread();  
myTread.start();
```  

一旦线程启动后start方法就会立即返回，而不会等待到run方法执行完毕才返回。就好像run方法是在另外一个cpu上执行一样。当run方法执行后，将会打印出字符串MyThread running。  

你也可以如下创建一个Thread的匿名子类：  
```
Thread thread = new Thread(){
   public void run(){
     System.out.println("Thread Running");
   }
};
thread.start();
```  

当新的线程的run方法执行以后，计算机将会打印出字符串”Thread Running”。  

#### 实现Runnable接口  
第二种编写线程执行代码的方式是新建一个实现了java.lang.Runnable接口的类的实例，实例中的方法可以被线程调用。下面给出例子：  
```  
public class MyRunnable implements Runnable {
   public void run(){
    System.out.println("MyRunnable running");
   }
}
```  

为了使线程能够执行run()方法，需要在Thread类的构造函数中传入 MyRunnable的实例对象。示例如下：  
```
Thread thread = new Thread(new MyRunnable());
thread.start();
```  
当线程运行时，它将会调用实现了Runnable接口的run方法。上例中将会打印出”MyRunnable running”。  

同样，也可以创建一个实现了Runnable接口的匿名类，如下所示：  
```
Runnable myRunnable = new Runnable(){
   public void run(){
     System.out.println("Runnable running");
   }
}
Thread thread = new Thread(myRunnable);
thread.start();
```  


#### 创建子类还是实现Runnable接口？  

对于这两种方式哪种好并没有一个确定的答案，它们都能满足要求。就我个人意见，我更倾向于实现Runnable接口这种方法。因为线程池可以有效的管理实现了Runnable接口的线程，如果线程池满了，新的线程就会排队等候执行，直到线程池空闲出来为止。而如果线程是通过实现Thread子类实现的，这将会复杂一些。  

有时我们要同时融合实现Runnable接口和Thread子类两种方式。例如，实现了Thread子类的实例可以执行多个实现了Runnable接口的线程。一个典型的应用就是线程池。  

#### 常见错误：调用run()方法而非start()方法  

创建并运行一个线程所犯的常见错误是调用线程的run()方法而非start()方法，如下所示：  
```
Thread newThread = new Thread(MyRunnable());
newThread.run();  //should be start();
```  

起初你并不会感觉到有什么不妥，因为run()方法的确如你所愿的被调用了。但是，事实上,run()方法并非是由刚创建的新线程所执行的，而是被创建新线程的当前线程所执行了。也就是被执行上面两行代码的线程所执行的。想要让创建的新线程执行run()方法，必须调用新线程的start方法。  

#### 线程名  

当创建一个线程的时候，可以给线程起一个名字。它有助于我们区分不同的线程。例如：如果有多个线程写入System.out，我们就能够通过线程名容易的找出是哪个线程正在输出。例子如下：  
```
MyRunnable runnable = new MyRunnable();
Thread thread = new Thread(runnable, "New Thread");
thread.start();
System.out.println(thread.getName());
```  
需要注意的是，因为MyRunnable并非Thread的子类，所以MyRunnable类并没有getName()方法。可以通过以下方式得到当前线程的引用：  
```
Thread.currentThread();
```  
因此，通过如下代码可以得到当前线程的名字：  
```
String threadName = Thread.currentThread().getName();
```  

#### 线程代码举例：  
这里是一个小小的例子。首先输出执行main()方法线程名字。这个线程JVM分配的。然后开启10个线程，命名为1~10。每个线程输出自己的名字后就退出。  

```
public class ThreadExample {
  public static void main(String[] args){
     System.out.println(Thread.currentThread().getName());
      for(int i=0; i<10; i++){
         new Thread("" + i){
            public void run(){
             System.out.println("Thread: " + getName() + "running");
            }
         }.start();
      }
  }
}
```  
需要注意的是，尽管启动线程的顺序是有序的，但是执行的顺序并非是有序的。也就是说，1号线程并不一定是第一个将自己名字输出到控制台的线程。这是因为线程是并行执行而非顺序的。Jvm和操作系统一起决定了线程的执行顺序，他和线程的启动顺序并非一定是一致的。  