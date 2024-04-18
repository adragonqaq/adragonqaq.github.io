---
layout: mypost
title: "线程中断（interrupt）"
date:  2024-04-18
categories: java
---



### 线程中断的三种方式

* 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
* 使用stop方法强行终止线程（这个方法不推荐使用，因为stop和suspend、resume一样，也可能发生不可预料的结果）。
* 使用int4errupt方法中断线程。


#### 使用退出标识

```java

public class ThreadFlag extends Thread
{
    public volatile boolean exit = false;
    
    public void run()
    {
        while (!exit);
    }
    public static void main(String[] args) throws Exception
    {
        ThreadFlag thread = new ThreadFlag();
        thread.start();
        sleep(5000); // 主线程延迟5秒
        thread.exit = true;  // 终止线程thread
        thread.join();
        System.out.println("线程退出!");
    }
}

```
在上面代码中定义了一个退出标志exit，当exit为true时，while循环退出，exit的默认值为false.在定义exit时，使用了一个Java关键字volatile，这个关键字的目的是使exit同步，也就是说在同一时刻只能由一个线程来修改exit的值


#### 使用stop方法终止线程

使用stop方法可以强行终止正在运行或挂起的线程。我们可以使用如下的代码来终止线程
```
   thread.stop();
```

虽然使用上面的代码可以终止线程，但使用stop方法是很危险的，就象突然关闭计算机电源，而不是按正常程序关机一样，可能会产生不可预料的结果，因此，并不推荐使用stop方法来终止线程

#### 使用interrupt方法终止线程

```
使用interrupt方法来中断线程可分为两种情况：

（1）线程处于阻塞状态，如使用了sleep方法。

（2）使用while（！isInterrupted（））{……}来判断线程是否被中断。
```

在第一种情况下使用interrupt方法，sleep方法将抛出一个InterruptedException例外，而在第二种情况下线程将直接退出。下面的代码演示了在第一种情况下使用interrupt方法

```java
public class ThreadInterrupt extends Thread
{
    public void run()
    {
        try
        {
            sleep(50000);  // 延迟50秒
        }
        catch (InterruptedException e)
        {
            System.out.println(e.getMessage());
        }
    }
    public static void main(String[] args) throws Exception
    {
        Thread thread = new ThreadInterrupt();
        thread.start();
        System.out.println("在50秒之内按任意键中断线程!");
        System.in.read();
        thread.interrupt();
        thread.join();
        System.out.println("线程已经退出!");
    }
}

```
上面代码的运行结果如下：
```
在50秒之内按任意键中断线程!
 
sleep interrupted
线程已经退出! 

```
在调用interrupt方法后， sleep方法抛出异常，然后输出错误信息：sleep interrupted.

注意：
在Thread类中有两个方法可以判断线程是否通过interrupt方法被终止。
一个是静态的方法interrupted（），
一个是非静态的方法isInterrupted（），
这两个方法的区别是interrupted用来判断当前线是否被中断，
而isInterrupted可以用来判断其他线程是否被中断。
因此，while （！isInterrupted（））也可以换成while （！Thread.interrupted（））。



### Thread.currentThread().interrupt() 用法详解

#### 基本概念
```
Thread.currentThread().interrupt()` 是 Java 中用于中断当前线程的方法。它有以下几个特点和用法：
调用 Thread.currentThread().interrupt() 会设置当前线程的中断状态为 “中断”。
当线程被中断时，其中断状态会被置为 true。
如果线程处于阻塞状态（如调用了 sleep()、wait()、join() 等方法），那么调用 interrupt() 方法将会抛出 InterruptedException 异常并清除中断状态。
在其他情况下，调用 interrupt() 方法只会设置中断状态，需要通过判断线程的中断状态来决定是否退出。
以下是 Thread.currentThread().interrupt() 的一些常见用途和注意事项：
响应中断：当某个线程需要响应外部中断请求时，可在合适的位置调用 interrupt() 方法，以便检查中断状态并作出相应的处理。
退出循环/任务：在循环或任务执行期间，可以在适当的条件下调用 interrupt() 方法来中断线程，并在循环中判断中断状态，从而安全地退出循环或任务。
传递中断：如果一个线程拥有一个子线程，并且该子线程也需要响应中断请求，可以在父线程中对子线程调用 interrupt() 方法，以传递中断请求。
恢复中断状态：在捕获 InterruptedException 异常时，通常应该重新中断当前线程，以便将中断状态传递给上层调用者。
需要注意的是，在调用 interrupt() 方法后，线程不会立即停止或退出，而是通过检查中断状态来决定是否继续执行。因此，开发人员需要在适当的时候检查线程的中断状态，并根据需要进行相应的处理，例如停止循环、释放资源等。

```
总结：Thread.currentThread().interrupt() 是用于设置当前线程的中断状态为 “中断” 的方法，可以帮助线程响应中断请求并进行相应的处理。

Thread.currentThread().interrupt() 用于中断当前线程并设置其中断状态为 “中断”。它可以在多种场景下使用，以下是关于该方法的详细用法和注意事项：

#### 在捕获 InterruptedException异常时重新中断线程：

```

try {
    // 可能会抛出 InterruptedException 的代码块
} catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // 重新中断线程
    // 处理中断异常
}
```
> 当捕获到 InterruptedException 异常时，通常应该重新中断当前线程以传递中断状态给上层调用者。

#### 中断当前线程并退出循环

```
while (!Thread.currentThread().isInterrupted()) {
    // 循环体代码

    if (需要中断条件) {
        Thread.currentThread().interrupt(); // 中断当前线程
    }
}

```

> 在需要退出循环的条件满足时，通过调用 interrupt() 方法来中断当前线程。然后，在循环的开头或结尾检查线程的中断状态，如果被中断则退出循环。

####  线程间传递和处理中断请求

```
Thread childThread = new Thread(() -> {
while (!Thread.currentThread().isInterrupted()) {
// 子线程的任务代码
}
});

childThread.start();
// ...
childThread.interrupt(); // 中断子线程

```
> 通过在父线程中对子线程调用 interrupt() 方法，可以传递中断请求给子线程，使其退出循环或任务。


#### 响应外部中断请求

```
while (循环条件) {
    if (Thread.currentThread().isInterrupted()) {
        // 中断状态为 true，进行相应的处理
        break; // 或者其他适当的操作
    }
    // 循环体代码
}
```

> 在循环或任务执行期间，使用 isInterrupted() 方法来检查线程的中断状态，并在必要时进行处理

>需要注意的是，Thread.currentThread().interrupt() 方法只会设置中断状态，而不会立即停止线程的执行。开发人员需要在适当的时候检查线程的中断状态，并根据需要进行相应的处理，例如停止循环、释放资源等。

>总结：Thread.currentThread().interrupt() 用于中断当前线程并设置其中断状态为 “中断”。它可以用于重新中断线程、退出循环、传递中断请求和响应外部中断请求等场景。应根据具体情况，在适当的时候检查线程的中断状态，并进行相关的处理。


#### 不使用interrupt()中断会发生什么

具体而言，在以下情况下未使用 Thread.currentThread().interrupt() 方法可能发生的情况如下：

在普通线程中未处理中断请求：

如果线程处于运行状态且没有检查中断状态，线程将继续执行，不会响应中断请求。
这可能导致线程无法正确地停止或退出循环，使得应用程序无法及时响应中断请求。
在阻塞方法中未处理中断请求：

如果线程处于阻塞状态（如调用了 sleep()、wait()、join() 等方法），并且没有捕获 InterruptedException 异常并进行相应的处理，线程将继续阻塞。
这可能导致线程无法在收到中断请求时立即唤醒，并且无法及时响应中断


#### 总结

>如果不使用 Thread.currentThread().interrupt() 方法来设置线程的中断状态为 “中断”，则线程的中断状态将保持不变。在这种情况下，线程可能会继续执行而不响应中断请求，导致无法正确停止或退出循环，或者无法在阻塞状态下及时响应中断请求。因此，应根据需要在适当的时候检查线程的中断状态，并进行相应的处理。



调用 Thread.currentThread().interrupt() 会中断当前线程。这通常用于在处理异常时清理资源或者在需要终止线程时使用。这个调用会设置线程的中断状态为 true。在一些需要处理中断的操作中，会检查线程的中断状态，以确定是否需要终止操作并退出线程。

需要注意的是，如果线程处于阻塞状态（如等待 I/O、等待锁、睡眠等），调用 interrupt() 方法会导致 InterruptedException 被抛出，从而提前结束阻塞状态。



>https://blog.csdn.net/a984171281/article/details/119569848
> https://blog.csdn.net/qq_39017153/article/details/132163611