# 多线程

## 并发编程的重要特性

**原子性**

一个操作或者多个操作，要么全部执行成功，要么全部执行失败。满足原子性的操作，中途不可被中断。

**可见性**

多个线程共同访问共享变量时，某个线程修改了此变量，其他线程能立即看到修改后的值。

**有序性**

程序执行的顺序按照代码的先后顺序执行。（由于JMM模型中允许编译器和处理器为了效率，进行指令重排序的优化。指令重排序在单线程内表现为串行语义，在多线程中会表现为无序。那么多线程并发编程中，就要考虑如何在多线程环境下可以允许部分指令重排，又要保证有序性）

synchronized关键字同时保证上述三种特性（仅单线程的情况下能够保证三种特性，多线程的情况下并不能保证有序性，因为单线程也不会有指令重排序的情况出现，所以能够保证）。

synchronized是同步锁，同步块内的代码相当于同一时刻单线程执行，故不存在原子性的问题。

synchronized关键字的语义JMM有两个规定，保证其实现内存可见性：

线程解锁前，必须把共享变量的最新值刷新到主内存中；

线程加锁前，将清空工作内存中共享变量的值，从主内存中冲洗取值。

volatile关键字作用的是保证可见性和有序性，并不保证原子性。

那么，volatile是如何保证可见性和有序性的？我们先进行基于JMM层面的实现基础，后面两章会进行底层原理的介绍。

## 程序运行的基本概念

**程序是什么？**

程序是一组指令和编程语句
**进程是什么？**

进程是计算机中正在运行的程序实例。一个程序可以被看作是一个静态的二进制文件，而进程是这个二进制文件在计算机中被运行的状态。每个进程都有自己独立的虚拟内存空间，包含程序的代码、数据和堆栈等信息。操作系统通过分配资源，如CPU时间、内存空间和输入输出设备等，来管理和控制进程的执行。在操作系统中，多个进程可以同时运行，每个进程都拥有自己的优先级和进程状态，可以通过进程间通信来实现数据共享和交互。

**线程是什么？**

线程是进程中的一个执行单元，也可以说是一个进程中的一个轻量级的子进程。线程与进程的差别在于，一个进程可以包含多个线程，而这些线程共享同一个进程的资源，如内存空间、文件句柄等等。在一个进程中，多个线程可以同时执行不同的任务，从而提高了程序的执行效率和响应速度。同时，线程的创建和上下文切换比进程更加轻量级，因此线程的开销要比进程小得多。

**程序是怎么运行的？**

程序运行的过程通常包括以下几个步骤： 

1. 编译/解释：在程序运行之前，程序需要被编译或解释成计算机能够理解和执行的机器语言代码。编译器将源代码转换为可执行的机器代码，而解释器则逐行解释源代码并在运行时执行。 

2. 加载：程序被编译或解释后，需要将它们加载到内存中才能执行。操作系统负责将程序加载到内存中，并为程序分配必要的资源和空间。 

3. 执行：一旦程序被加载到内存中，它就可以开始执行了。程序在CPU上运行，并根据程序的指令逐步执行任务。 

4. 输出结果：程序执行完成后，通常需要输出结果。输出结果可以是显示在屏幕上、存储在文件中，或通过网络发送到其他计算机等。 

总体来说，程序的运行是一个非常复杂的过程，涉及到操作系统、编译器、CPU等多个组件的协同工作。

**线程如何进行调度？**

线程之间的调度是由操作系统负责。在操作系统中，有一个称为调度器（Scheduler）的组件，它控制着系统中所有线程的执行。调度器的主要任务是根据线程的优先级和各种调度算法，决定哪个线程可以获得执行权，并分配给它CPU时间片来执行。CPU时间片是指操作系统把CPU的执行时间分配给各个线程的单位时间。在操作系统中，线程的优先级通常与进程的优先级相关联。高优先级的线程会比低优先级的线程更容易获得执行权，从而提高系统的响应速度。当线程等待某些条件满足时，调度器会暂停它的执行并执行其他的线程，这个过程叫做线程切换。线程切换会涉及到上下文切换（Context Switch）的操作，即保存当前线程的状态，加载下一个线程的状态，从而让线程之间的切换更加高效、稳定。

**线程切换的概念是什么？**

线程切换是指当一个线程的执行被暂停，另一个线程获得执行权并开始执行时所发生的一系列操作。这个过程中，操作系统需要保存当前线程的状态（包括寄存器、程序计数器、堆栈等信息），并将下一个线程的状态加载到对应的寄存器和堆栈中。当下一个线程执行完成后，它的状态信息将被保存到内存中，再将下一个线程的状态信息加载到寄存器和堆栈中，从而保证线程之间的切换更加高效、稳定。线程切换通常是由操作系统和CPU的硬件支持实现的，可能会涉及到一些上下文切换的操作，如获取和释放锁、切换内存映射表等等。线程切换的目的是让多个线程可以并发执行，提高系统的响应速度和效率。

因为线程切换涉及到上下文切换，所以线程并不是越多越好的，过多的线程会是的计算机忙于上下文切换的工作。

**Java线程和内核线程之间存在什么关系？**

Java线程和内核线程之间的关系是比较复杂的。由于Java是一门跨平台的语言，而不同的操作系统实现线程的方式也有所不同，所以Java的线程实现也会因操作系统不同而有所差异。一般情况下，Java线程是在JVM内部实现的，而JVM会将Java线程映射到操作系统的内核线程上，由操作系统实现线程的调度和执行。也就是说，Java线程是运行在内核线程之上的，它们需要由操作系统提供的内核线程支持才能运行。但是，Java线程的数量和内核线程的数量并没有直接的关系，而是由JVM和操作系统的具体实现决定的，不同的实现可能会有不同的策略来映射JVM线程到操作系统线程。

**Java线程和内核线程之间是否存在数量关系？**

Java的线程和内核线程存在一定的数量关系，但并非直接对应关系。Java线程是在JVM（Java虚拟机）中实现的，而JVM会将Java线程映射到操作系统的线程中。这些操作系统线程是内核线程，由操作系统调度和执行。因此，Java线程数不能超过操作系统最大线程数，同时，Java线程还受到JVM内存限制的影响。总体上讲，Java线程和内核线程的关系是比较复杂的，取决于JVM和操作系统的具体实现。

## 锁

### 锁的概念

线程锁是一种同步机制，用于防止多个线程同时访问共享资源时出现的问题。当多个线程访问共享资源的时候，可能会出现竞争条件（race condition），导致数据不一致或程序异常。线程锁通过控制线程的访问顺序，来防止这种竞争条件的出现。
 线程锁分为两类：悲观锁和乐观锁。悲观锁假设会发生并发冲突，因此在访问共享资源时会使用锁来防止多个线程同时访问。乐观锁则假设不会发生并发冲突，因此先进行操作，再根据操作结果判断是否出现冲突，如果出现冲突再使用锁来重新操作。
 常见的线程锁有互斥锁、读写锁、自旋锁等，它们分别适用于不同场景下的并发访问问题。互斥锁适用于单个线程访问共享资源的情况，而读写锁适用于多个线程对同一资源进行读取和写入的情况，自旋锁则是适用于竞争不激烈的情况下，效率更高的一种锁。

### 锁的类型

常见的锁类型如下：

**互斥锁（Mutex）**

互斥锁是最基本的锁机制之一，它对共享资源加锁时，只允许一个线程访问，其他线程必须等待。当一个线程获得了互斥锁后，其他线程将会进入阻塞状态，直到锁被释放。这种锁机制会导致线程阻塞，因此在高并发和高冲突的场景下容易出现性能瓶颈。

**读写锁（ReadWrite Lock）**

读写锁是一种特殊的锁机制，它允许多个线程同时读取共享资源，但只允许一个线程写入共享资源。这种锁机制可以提高读取共享资源的效率，但写操作仍然会导致线程阻塞。

**自旋锁（Spin Lock）**
自旋锁是一种非阻塞锁，它不会让线程进入阻塞状态，而是通过轮询方式等待锁的释放。当线程尝试获取锁时，如果锁已经被其他线程持有，线程不会进入阻塞状态，而是不断地尝试获取锁，直到成功获取锁为止。这种锁机制可以减少线程的阻塞和唤醒，提高了并发性能，但同时也会增加CPU的使用率。

**偏向锁（Biased Locking）**

偏向锁是一种针对无竞争场景的优化锁机制。当一个线程访问一个同步块时，如果同步块没有被其他线程访问，JVM会将上次获得锁的线程ID记录下来，下次访问时直接判断是不是同一个线程访问，如果是，则不需要进行加锁和解锁
操作，可以直接访问。这种锁机制可以减少线程的竞争，提高并发性能。

**轻量级锁（Lightweight Locking）**

轻量级锁是一种针对有竞争场景的优化锁机制。当一个线程尝试获取锁时，JVM会使用CAS操作，将对象头的Mark Word替换为指向锁记录的指针，将锁记录的状态设置为“锁定”，这样就避免了线程进入阻塞状态。当同步块执行完毕时，JVM将锁记录中的Mark Word恢复到对象头中，这个过程中不会有线程阻塞，因此性能较高。

**重量级锁（Heavyweight Locking）**

重量级锁是一种传统的锁机制，它将所有的竞争线程都进入阻塞状态，直到锁被释放。这种锁机制会导致线程阻塞，性能较差，但在大量竞争的场景下可以确保数据的安全性。

**什么是CAS操作？**

 在多线程并发的场景下，多个线程同时读写同一个变量，可能会导致竞争和冲突，如果不加锁或者同步，就会出现数据不一致或者出错的情况。传统的加锁机制（例如synchronized）需要将竞争的线程进入锁定状态，只有一个线程能够访问共享变量，其他线程则被阻塞，等待唤醒。这种做法显然会导致线程阻塞，影响性能，且在多核CPU架构下，频繁的线程切换也会浪费CPU资源。
 CAS操作采用了另一种方法，它不需要将线程进入锁定状态，而是通过比较和替换的方式来保证原子性操作。CAS操作包含三个参数，即被操作的内存地址V、期望的旧值A和新值B，它的操作过程如下：

1. 首先，线程会读取内存地址V中的旧值A；
   2. 然后，线程将期望的旧值A与内存地址V中的旧值A进行比较，如果两者相等，则说明内存中的值没有被修改，就可以将新值B写入内存地址V中，操作成功；
   3. 如果内存地址V中的旧值A已经被其他线程修改，线程比较的结果就会不相等，此时就需要重新尝试比较和替换操作。
      CAS操作是一种“乐观锁”的实现，它的优势在于不需要线程进入锁定状态，避免了线程阻塞和等待，因此在高并发和高冲突的场景下具有更好的性能和可伸缩性。但是，如果多个线程同时尝试更新同一个内存地址V，并且不断地重试，就会导致CPU的使用率非常高，因此应该在合适的场景下选择使用CAS操作。

**CAS操作存在那些问题又是如何解决的？**

CAS操作存在以下几个问题：

1. ABA问题：即当原始值从A变成了B，又从B变回了A，CAS操作会认为原值没有被改变，从而出错。这个问题可以通过加版本号（类似于时间戳）来解决，每次比较操作时都判断版本号是否相同。

2. 自旋开销大：由于CAS操作是自旋操作，会一直尝试直到成功，所以如果同时有多个线程在尝试CAS操作，会造成大量的CPU资源浪费。这个问题可以通过在自旋的过程中加入一定的延迟和重试次数来缓解。

3. 只能保证一个变量的原子性：CAS操作只能保证单个变量的原子性，无法保证多个变量之间的原子性。如果要保证多个变量之间的原子性，需要使用锁或者其他并发控制机制。
   另外，需要注意的是，CAS操作虽然可以避免锁的争用，提高并发性能，但也会增加代码的复杂度和出错的可能性。因此，在使用CAS操作时，需要根据具体情况及时评估其得失，权衡利弊。
   
   另外，CAS操作的在比对旧值之后，进行修改数值时需要保证修改数值的操作是原子性操作，那是如何保证的呢？
   
   使用到CPU指令Lock，Lock指令是一种CPU指令，用于实现多线程同步。当多个线程同时访问共享资源时，Lock指令可以确保只有一个线程能够操作该资源，从而保证线程安全。Lock指令的主要作用是将内存中的一个变量或者一个内存区域锁定，使得其他线程无法访问该变量或内存区域，直到当前线程释放锁为止。 

Lock指令的基本原理是，在锁定某个变量或内存区域之前，首先检查该变量或内存区域是否已经被其他线程锁定。如果已经被锁定，则当前线程会进入等待状态，直到其他线程释放锁。如果该变量或内存区域没有被锁定，则当前线程可以锁定该变量或内存区域，防止其他线程访问。当当前线程完成操作后，通过执行Unlock指令释放锁，其他线程可以重新访问该变量或内存区域。

需要注意的是，由于Lock指令会阻塞其他线程的访问，因此在设计并发程序时需要注意避免使用过多的锁，以避免出现死锁等问题。此外，随着硬件技术的发展，现代CPU也提供了更高效的同步指令，如Compare-and-Swap（CAS）指令，可以用于实现更高效的线程同步。

**synchronized底层原理**

主要描述一下使用synchronized时锁的升级过程。

从没有使用synchronized到开始使用synchronized时：

升级为偏向锁，对象头中标记当前资源被那个线程使用，该线程能够直接访问资源(即没有竞争的状态下标记线程能够直接访问资源)

当出现线程与标记线程争抢资源时，

升级为轻量级锁，两个线程进行自旋争抢资源。

当线程自旋次数达到一定阈值之后：

升级为重量级锁，进行阻塞等待

> 详细可见文章：[详细了解 Synchronized 锁升级过程](https://zhuanlan.zhihu.com/p/477623320)

**对象的内存分布**

1. 对象头（markword），12个字节
2. 类型指针(class pointer)，即指向class，4个字节，jvm默认参数`+UserCompressedClassPointers`（压缩class指针），所以默认情况下是4个字节,否则8个字节
3. 实例数据(instantce data)，即对象中的成员变量，成员变量如果是引用类型的话jvm默认参数`+UseCompressedOops`（压缩普通对象指针）所以这个指针的大小也是压缩后的4个字节，否则8个字节
4. 字节对齐，补齐至字节数能够被内存每次读取字节数整除，目的是提高内存系统的性能。详细见：[理一理字节对齐的那些事](https://zhuanlan.zhihu.com/p/44625744)

**cache line**

> 参考：[CPU cache line与多线程性能优化](https://zhuanlan.zhihu.com/p/374586744)

CPU映射内存中的数据到缓存时，最小的单位就是**cache line**，现在cache line的大小一般都是64个字节。也就是说，CPU就算只需要在主存中映射1个字节的数据到缓存中，那么它也会将这个字节所在内存段的64个字节全部映射到缓存当中。

假如说在多线程的环境下，需要保证这个字节数据的一致性（该字节数据发生修改时及时通知其他线程获取最新的值），那么保证数据一致性的最小单位也是一个cache line。假设此时Core A和Core B正在访问Byte A和Byte B，恰巧Byte A和Byte B又在同一个cache line中，那么此时如果Core A修改了Byte A或者是Core B修改了Byte B都要互相通知对方取新的值。

在这个过程中就会浪费掉一定的性能。那么如何解决这个问题呢？

只需要将需要保证数据一致性的数据大小向cache line的大小对齐，假设我只使用了八个字节，那么我每次都将数据大小补齐到64个字节。

我们这里使用Java的代码演示一下：

优化前

```java
class Data {
    public volatile long value;
}

public class Main {
    public static Data[] arr = new Data[2];

    static {
        arr[0] = new Data();
        arr[1] = new Data();
    }

    public static void main(String[] args) throws InterruptedException {
        StopWatch watch = StopWatch.create("操作计时");

        Thread t1 = new Thread(() -> {
            while (arr[0].value < 10_000_000)
                arr[0].value++;
        });

        Thread t2 = new Thread(() -> {
            while (arr[1].value < 10_000_000)
                arr[1].value++;
        });

        watch.start("操作");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        watch.stop();
        System.out.println(watch.prettyPrint(TimeUnit.MILLISECONDS));
        // 输出耗时104ms
    }
}
```

```java
class Padding{
    public volatile long p1,p2,p3,p4,p5,p6,p7;
}

class Data extends Padding{
    public volatile long value;
}

public class Main {
    public static Data[] arr = new Data[2];

    static {
        arr[0] = new Data();
        arr[1] = new Data();
    }

    public static void main(String[] args) throws InterruptedException {
        StopWatch watch = StopWatch.create("操作计时");

        Thread t1 = new Thread(() -> {
            while (arr[0].value < 10_000_000)
                arr[0].value++;
        });

        Thread t2 = new Thread(() -> {
            while (arr[1].value < 10_000_000)
                arr[1].value++;
        });

        watch.start("操作");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        watch.stop();
        System.out.println(watch.prettyPrint(TimeUnit.MILLISECONDS));
        // 输出耗时26ms
    }
}
```

**指令重排序**

Java的指令重排序是指在编译Java程序时为了提高程序性能，将原代码中的指令顺序进行了优化调整，以减少指令之间的依赖和提高并行度。这种优化方式可以使程序执行更快，但也可能导致程序的输出结果发生变化或不符合预期。

 在Java中，指令重排序通常是由编译器和处理器共同完成的。编译器会对代码进行优化，并在处理器执行时继续进行指令重排序。在多线程环境中，可能存在线程之间共享的变量，如果指令重排序不正确，会导致程序出现数据竞争的问题，从而影响程序的输出结果。

 为了保证程序的正确性，Java提供了内存屏障和同步机制，可以防止某些类型的指令重排序，例如在多线程环境下涉及共享内存访问的指令重排序。在编写Java程序时，需要了解指令重排序的原理和影响，以避免出现因指令重排序而产生的不可预期的结果。

 举例说明：

```java
public class Main {
    static int x, y, a, b;

    public static void main(String[] args) throws InterruptedException {
        long counter = 0;

        while (true) {
            counter++;
            x = 0;
            y = 0;
            a = 0;
            b = 0;

            Thread t1 = new Thread(() -> {
                a = 1;
                x = b;
            });

            Thread t2 = new Thread(() -> {
                b = 1;
                y = a;
            });

            t1.start();
            t2.start();
            t1.join();
            t2.join();

            if (x == 0 && y == 0){
                System.out.printf("第%d次,x=%d,y=%d%n", counter, x, y);
                return ;
            }
        }
    }
}
```

分析一下上述代码的行为，如果代码是顺序执行的，那么每一次循环结束时a,b的值都不可能都为0。

但是实际运行时发现在循环75423次之后出现了x=0,y=0的情况：

![指令重排序配图-1.png](https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/%E6%8C%87%E4%BB%A4%E9%87%8D%E6%8E%92%E5%BA%8F%E9%85%8D%E5%9B%BE-1.png)



## volatile的底层原理

> 引用文章,除了对synchronized对保证多线程的有序性描述有点问题应该没有问题：[volatile底层原理详解](https://zhuanlan.zhihu.com/p/133851347)

## 强/弱/软/虚引用

> 参考文章： [强引用、软引用、弱引用和虚引用](https://juejin.cn/post/7075893982953209887#comment)

需要了解，一个对象能够同时被多种引用同时引用，如ThreadLocal在使用时就是强引用和弱引用同时使用的，当强引用时则仅有弱引用关联。

虚引用不理解应用场景，软、弱引用都可以用于缓存场景，但是又略有不同。

软引用可以用于本地缓存的场景，但是这个缓存是高频访问的缓存

弱引用的应用：

![弱引用使用场景-1.png](https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/%E5%BC%B1%E5%BC%95%E7%94%A8%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF-1.png)

这个存在与jar包中的默认模板文件不用使用完之后就直接关闭流，可以考虑修改为每次使用时都看看弱引用`byte[]`是否存在，如果不存在则从磁盘中加载文件到`byte[]`中，然后使用；如果存在则直接使用这个`byte[]`写入到输出流中。

## ThreadPoolExecutor

### 核心参数

```java
public ThreadPoolExecutor(
    int corePoolSize,                   // 核心线程数
    int maximumPoolSize,                // 最大线程数
    long keepAliveTime,                 // 非核心线程的空闲时间
    TimeUnit unit,                      // 空闲时间的时间单位
    BlockingQueue<Runnable> workQueue,  // 阻塞队列
    ThreadFactory threadFactory,        // 线程工厂
    RejectExecutionHandler handler      // 拒绝策略
);
```

**什么是核心线程？**

线程池的核心线程是指在线程池中最初创建的一组线程，它们可以一直存活着，即使当前没有任务需要处理。当有任务需要处理时，线程池可以立即使用这些核心线程来执行。当任务量增加时，线程池可以创建更多的线程，但是当任务量减少时，线程池也会回收多余的线程，以节省系统资源。

那么核心线程数指的就是可以线程池中可以创建的核心线程数量。

上面的回答中提到的**创建更多的线程**指的可不是核心线程，当这些线程空闲的时间达到**keepAliveTime**和**unit**共同决定的时长之后销毁掉。

**最大核心数**指的就是线程池中总共可以创建的核心线程数与其他线程的数量总和。

假设当前设置的`corePoolSize = 1,maximumPoolSize = 2`，那么此时有1个核心线程1个其他线程，说明他们在同一时间内只能够同时处理两个任务。当第三个任务提交到线程池执行时这个任务会暂时存放在workQueue中，等待线程池中的线程执行完当前正在执行的任务后处理workQueue中的任务。

BlockingQueue是一个接口，对应的有好几种实现类，具体可参考文章: [深入理解Java系列 | BlockingQueue用法详解](https://juejin.cn/post/6999798721269465102)

当workQueue里已经放不下更多的任务的时候（当然了可以不限阻塞队列大小，但是大概率用不到），就会触发到拒绝策略，由handler提供。

RejectExecutionHandler是Java中线程池的拒绝策略，它定义了当线程池无法接受新的任务时的处理方式。其几种实现如下：

1. AbortPolicy：直接抛出RejectedExecutionException异常，阻止系统接受新的任务。

2. CallerRunsPolicy：让提交任务的线程来执行该任务，如果队列已满，则该任务会在提交线程中运行。相当于将线程池转换为同步执行的任务队列。

3. DiscardPolicy：静默丢弃无法处理的任务，不做任何处理。

4. DiscardOldestPolicy：丢弃队列中最早的任务，尝试再次提交当前的任务。
   
   可以根据具体的业务场景需求选择合适的拒绝策略。
   
   RejectExecutionHandler是一个接口，我们可以使用我们自己的实现。

然后就是**threadFactory**，线程池中的线程都是由这个线程工厂来创建的。

### 核心属性ctl

### 线程池的状态变换

### execute方法

### addWorker方法
