#### 倒计时CountDownLatch

CountDownLatch是一个非常实用的多线程控制工具类，称之为“**倒计时器**”，**它允许一个或多个线程一直等待，直到其他线程的操作执行完后再执行**

* 在每一个线程执行完毕之后，都需要执行`countDownLatch.countDown()`方法，不然计数器就不会准确；
* 只有所有的线程执行完毕之后，才会执行 `countDownLatch.await()`之后的代码；
* CountDownLatch 阻塞的是主线程；

CountDownLatch是在java1.5被引入的，它存在于java.util.concurrent包下。**CountDownLatch这个类能够使一个线程等待其他线程完成各自的工作后再执行**。例如，应用程序的主线程希望在负责启动框架服务的线程已经启动所有的框架服务之后再执行。

CountDownLatch是通过一个计数器来实现的，计数器的初始值为线程的数量。每当一个线程完成了自己的任务后，计数器的值就会减1。当计数器值到达0时，它表示所有的线程已经完成了任务，然后在闭锁上等待的线程就可以恢复执行任务

![0?wx_fmt=png](https://ss.csdn.net/p?http://mmbiz.qpic.cn/mmbiz_png/UtWdDgynLdY9n5c11P9g7rGwa8ALhzDDPmy7yWAIIGiaicCdmqkJQBwPJGK4ibsCabIp8u363wLst3oSHoS7UxmCg/0?wx_fmt=png)

**CountDownLatch在实时系统中的使用场景**

让我们尝试罗列出在java实时系统中CountDownLatch都有哪些使用场景。我所罗列的都是我所能想到的。如果你有别的可能的使用方法，请在留言里列出来，这样会帮助到大家。

（1）**实现最大的并行性**：有时我们想同时启动多个线程，实现最大程度的并行性。例如，我们想测试一个单例类。如果我们创建一个初始计数为1的CountDownLatch，并让所有线程都在这个锁上等待，那么我们可以很轻松地完成测试。我们只需调用 一次countDown()方法就可以让所有的等待线程同时恢复执行。

（2）**开始执行前等待n个线程完成各自任务**：例如应用程序启动类要确保在处理用户请求前，所有N个外部系统已经启动和运行了。

（3）**死锁检测**：一个非常方便的使用场景是，你可以使用n个线程访问共享资源，在每次测试阶段的线程数目是不同的，并尝试产生死锁。



#### **循环屏障CyclicBarrier**

CyclicBarrier是另一种多线程并发控制使用工具，和CountDownLatch非常类似，他也可以实现线程间的计数等待，但他的功能要比CountDownLatch更加强大一些。

CyclicBarrier 的字面意思是**可循环使用（Cyclic）的屏障（Barrier）**。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活

CyclicBarrier默认的构造方法是`CyclicBarrier(int parties)`，**其参数表示屏障拦截的线程数量**，每个线程调用`await`方法告诉CyclicBarrier我已经到达了屏障，然后当前线程被阻塞。

CyclicBarrier强调的是n个线程，大家相互等待，只要有一个没完成，所有人都得等着

CyclicBarrier类位于java.util.concurrent包下，CyclicBarrier提供2个构造器：

```java
public CyclicBarrier(int parties, Runnable barrierAction) {
}
 
public CyclicBarrier(int parties) {
}
```

参数parties指让多少个线程或者任务等待至barrier状态；参数barrierAction为当这些线程都达到barrier状态时会执行的内容

**需要在每个线程后面都加上await()方法**

**CyclicBarrier和CountDownLatch的区别**

（1）CountDownLatch的计数器只能使用一次。而CyclicBarrier的计数器可以使用reset() 方法重置。所以CyclicBarrier能处理更为复杂的业务场景，比如如果计算发生错误，可以重置计数器，并让线程们重新执行一次。

（2）CyclicBarrier还提供其他有用的方法，比如getNumberWaiting方法可以获得CyclicBarrier阻塞的线程数量。isBroken方法用来知道阻塞的线程是否被中断。比如以下代码执行完之后会返回true。

（3）CountDownLatch会阻塞主线程，CyclicBarrier阻塞子线程。