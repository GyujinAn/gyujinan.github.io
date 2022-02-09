---
layout: post
title: "쓰레드 thread"
date: 2021-12-11
categories: java
---

<!-- # chapter13 쓰레드 thread-->

## 학습목표

자바 프로그램에서 왜 멀티 쓰레드를 사용하는지 이해하고 쓰레드를 관리하기 위한 여러 사용법을 익힌다.


### 1. 쓰레드란 무엇인가?

1.1 프로세스와 쓰레드

프로세스는 실행 중인 프로그램이다. 프로그램이 실행되면 os는 메모리를 할당하여 저장장치에 있는 데이터(binary code = 명령어 + 전역변수)를 메모리에 적재한다. 이렇게 메모리에 적재 된 프로그램을 프로세스라고 한다. 프로세스는 한개 이상의 쓰레드로 구성 되는데 각 쓰레드들은 호출스택(call stack)을 할당받게 된다. 그러면 스케줄러는 cpu 사용시간을 각 쓰레드에게 할당하여 작업을 처리하는데, 이때  프로세스의 쓰레드들이 할당 받은 시간을 받아 자신의 호출스택에서 작업을 처리한다. 

<br>

1.2 멀티쓰레드와 싱글쓰레드

프로세스 안에 쓰레드가 여러개가 존재하면 멀티쓰레드 프로세스 그리고 하나의 쓰레드가 존재하면 싱글쓰레드 프로세스라고 한다. 이렇게 여러개의 쓰레드를 통해서 프로세스의 작업들을 처리하는 이유는 사용자에게 높은 응답성을 제공하기 위해서 이다. 만약 메신저를 사용할 때 파일 다운로드가 완료되는 시간까지 메세지를 보낼 수 없다면 높은 응답성을 제공한다고 말 할 수 없을 것이다. 하지만 이러한 멀티 쓰레드의 도입으로 인해 개발자 관점에서는 개발의 난이도가 높아지게 되었다. 예를 들면 각 쓰레드의 동기화와 각 쓰레드들이 같은 자원을 사용 함으로써 발생하는 데드락 같은 현상을 고려하여 개발을 진행해야되기 때문이다.


### 2. 쓰레드의 구현과 생성 그리고 실행

2.1 쓰레드의 구현

쓰레드는 두가지 방법으로 구현 할 수 있다. Thread 클래스를 상속하여 run() 메소드를 오버라이딩하는 것이다. 두번째 방법으로는 Runnable 인터페이스를 implement하여 run()메소드를 구현하는 것이다. 두 방법중 대부분의 자바 개발에서 재사용성(reusability)을 높이고 일관성(consistency)을 유지하기 위해 두번째 개발을 지향한다. 그리고 Runnable 인터페이스를 사용하는 방법은 전략(strategy)패턴으로 되어 있어 개발자가 작성하는 비즈니스 로직과 쓰레드의 로직을 분리되어 있기 때문에 더욱 객체지향적 개발이 될 수 있다. 

추가적으로 상속을 통한 방법은 Thread 클래스의 메소드를 상속 받았기 때문에 run()메소들에서 Thread클래스의 메소드에 접근 할 수 있지만 Runnable 인터페이스를 구현하는 방법에서는 Thread 메소드를 바로 접근 할 수가 없다. Thread의 메소드에 접근하기 위해서는 Thread.currentThread() 통해서 Thread에 대한 참조를 얻은 후에 접근 할 수 있다.

```java
//Thread 클래스를 상속하여 구현
class ThreadEX_1 extends Thread{

    public void run() {
        //비즈니스 로직 구현
        ...

        //쓰레드에 바로 접근 가능
        getName();
    }

}
```

```java
//Runnable 인터페이스를 구현
class ThreadEX_2 implements Runnable{

    @Override
    public void run() {
        //비즈니스 로직 구현
        ...

        //쓰레드 접근을 위한 참조변수 획득
        Thread thread = Thread.currentThread();
        thread.getName();
    }

}
```

2.2 쓰레드의 생성

구현법이 다른 만큼 그에 따른 쓰레드 객체 생성법 또한 다르다. 상속을 통한 방법은 new Class명으로 쓰레드 인스턴스를 생성해주면 되지만 전력패턴으로 되어있는 Runnable 인테페이스 구현은 Thread 인스턴스를 생성 할 때 생성자를 파라미터를 통해서 전략클래스의 참조를 넘겨주어야 한다.

```java
public class ThreadMain{
    public static void main(String[] args) {
        
        //상속 된 쓰레드의 생성
        ThreadEX_1 threadEX_1 = new ThreadEX_1();

        //Runnable이 구현 된 전략클래스 쓰레드 생성
        Thread thread = new Thread(new ThreadEX_2());
    }
}

```

2.3.1 쓰레드의 실행

쓰레드의 실행은 실제 os의 쓰레드 생성 및 호출스택 할당과 같은 초기화 단계를 실행하기 위해 구현 된 run()메소드 호출을 통해서가 아닌 start()메소드를 호출을 통해서 한다. 그리고 쓰레드는 한번만 실행되고 두번 실행될 수 없다. (그 이유는 잘 모르겠다.) start()가 두번 호출 될 수 없고 만약 두번 호출 된다면 IllegalThreadStateException를 발생시킨다. 

```java
public synchronized void start() {
    /**
        * This method is not invoked for the main method thread or "system"
        * group threads created/set up by the VM. Any new functionality added
        * to this method in the future may have to also be added to the VM.
        *
        * A zero status value corresponds to state "NEW".
        */
    if (threadStatus != 0)
        throw new IllegalThreadStateException();
    ...
}

```

2.3.2 쓰레드의 실행 과정

1) 특정 쓰레드에서 쓰레드의 start()를 호출한다.

2) start() 메소드는 os가 사용 할 쓰레드를 생성하고 해당 쓰레드의 호출스택을 생성해준다.

3) 호출스택이 생성되면 해당 호출스택에 첫번째 스택에 run()를 적재한다.

4) jvm 스케줄러에 의해 호출스택의 작업시간을 할당 받아 run()의 작업이 실행된다.

5) run() 메소드가 종료되면 쓰레드는 종료되고 호출스택은 반환된다.


※ start() 메소드에서 쓰레드 생성 및 호출 스택이 일어나지 않고 start0()라고 하는 네이티브 메소드를 통해서 생성된다.
```java
    public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0(); <-- 쓰레드, 호출 스택 생성을 위한 네이티브 메소드
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
```

※ Exception의 printStackTrace() 메소드를 통해 호출스택을 확인하면 쓰레드 생성을 통해 할당 받은 호출스택의 제일 아래 스택에는 run() 메소드가 존재하는 걸 확인 할 수 있다.

### 3. 쓰레드의 우선순위(priority)

Thread 클래스에는 priority라는 필드가 존재한다. 해당 필드는 cpu 할당시간의 우선순위를 나타낸다. 숫자가 높으면 높을 수록 cpu에 대한 시간을 다른 쓰레드에 비해 많이 받을 수 있다. 그리고 쓰레드가 생성되면 쓰레드를 생성한 쓰레드의 우선순위를 상속받는다. 우선순위에 대한 쓰레드 메소드는 두개가 존재한다.

```java
public final void setPriority(int newPriority)

public final int getPriority()

```

하지만 멀티코어 상황에서 쓰레드의 우선순위가 적용되지 않는 경우가 많아서 실제로 잘 사용되지는 않는다.


### 4. 쓰레드 그룹(Thread Group)

4.1 쓰레드 그룹이란
ThreadGroup은 쓰레드 객체를 관리하기 위한 자바 객체이다. 그러므로 쓰레드는 하나의 ThreadGroup이라는 객체에 속하게 된다. 쓰레드 그룹을 통해서 쓰레드들을 디렉토리와 파일 같이 tree 구조로 관리 함으로써 보안상 이점을 가질 수 있다. 쓰레드 그룹은 자신과 자신 자식 그룹만 수정을 할 수 있고 다른 쓰레드 그룹은 수정 할 수 없기 때문이다. 그리고 기본적으로 쓰레드의 쓰레드 그룹은 자신을 생성한 쓰레드에 속한 쓰레드 그룹으로 속하게 된다. 예를 들어 새로운 쓰레드 그룹을 생성하지 않고 쓰레드를 생성한다면 모든 쓰레드는 main 그룹이라는 main 쓰레드가 속해 있는 쓰레드 그룹에 소속된다. 

<!-- 
두 메소드의 내용를 어디에 넣어야될까? 자바의 정석 p 742
ThreadGroup getThreadGroup()
void uncaughtException(Thread t, Throwable e) -->


4.2 jvm의 쓰레드 그룹
jvm은 자바 프로그램을 실행하면서 main과 system이라는 쓰레드 그룹을 생성하여 필요한 쓰레드 그룹 안에 생성하는데, gc를 수행하는 Finalizer 쓰레드 등등은 system 그룹에 포함시키고 main 쓰레드 등등은 main 그룹에 포함시킨다. 그리고 개발자들이 생성한 쓰레드와 쓰레드 그룹은 main 그룹 혹은 main그룹의 하위 그룹으로 생성된다. Thread.getAllStackTraces()를 호출에 jvm이 생성한 쓰레드 그룹을 확인 할 수 있다.

```java

public class ThreadEx{
    public static void main(String[] args) {

        Map map = Thread.getAllStackTraces();

        Iterator iterator = map.keySet().iterator();

        int x = 0;

        while(iterator.hasNext()){
            Object next = iterator.next();
            Thread thread = (Thread) next;

            System.out.println("["+ x + "] name : " + thread.getName()
                            + ", group : " + thread.getThreadGroup().getName());

            StackTraceElement[] stackTraceElements = (StackTraceElement[]) map.get(next);

            for(StackTraceElement stackTraceElement : stackTraceElements){
                System.out.println("    "+stackTraceElement);
            }
        }
    }
}

---실행 결과---
[0] name : main, group : main <-- 메인 쓰레드 그룹
    java.lang.Thread.dumpThreads(Native Method)
    java.lang.Thread.getAllStackTraces(Thread.java:1610)
    ThreadEx.main(ThreadEx.java:17)
[0] name : Signal Dispatcher, group : system 
[0] name : Reference Handler, group : system 
    java.lang.Object.wait(Native Method)
    java.lang.Object.wait(Object.java:502)
    java.lang.ref.Reference.tryHandlePending(Reference.java:191)
    java.lang.ref.Reference$ReferenceHandler.run(Reference.java:153)
[0] name : Monitor Ctrl-Break, group : main
    java.util.Properties$LineReader.readLine(Properties.java:478)
    java.util.Properties.load0(Properties.java:353)
    java.util.Properties.load(Properties.java:341)
    sun.net.NetProperties.loadDefaultProperties(NetProperties.java:70)
    sun.net.NetProperties.access$000(NetProperties.java:41)
    sun.net.NetProperties$1.run(NetProperties.java:47)
    sun.net.NetProperties$1.run(NetProperties.java:45)
    java.security.AccessController.doPrivileged(Native Method)
    sun.net.NetProperties.<clinit>(NetProperties.java:44)
    sun.net.spi.DefaultProxySelector$1.run(DefaultProxySelector.java:95)
    sun.net.spi.DefaultProxySelector$1.run(DefaultProxySelector.java:93)
    java.security.AccessController.doPrivileged(Native Method)
    sun.net.spi.DefaultProxySelector.<clinit>(DefaultProxySelector.java:92)
    java.lang.Class.forName0(Native Method)
    java.lang.Class.forName(Class.java:264)
    java.net.ProxySelector.<clinit>(ProxySelector.java:72)
    java.net.SocksSocketImpl$3.run(SocksSocketImpl.java:356)
    java.net.SocksSocketImpl$3.run(SocksSocketImpl.java:354)
    java.security.AccessController.doPrivileged(Native Method)
    java.net.SocksSocketImpl.connect(SocksSocketImpl.java:353)
    java.net.Socket.connect(Socket.java:606)
    java.net.Socket.connect(Socket.java:555)
    java.net.Socket.<init>(Socket.java:451)
    java.net.Socket.<init>(Socket.java:228)
    com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:44)
[0] name : Finalizer, group : system main <-- 시스템 쓰레드 그룹
    java.lang.Object.wait(Native Method)
    java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:144)
    java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:165)
    java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:216)

```

4.3 쓰레드 그룹과 컴포넌트 패턴

쓰레드 그룹들은 컴포넌트 패턴으로 설계 되었기 때문에 상위 쓰레드 그룹의 속성을 설정하면 자동으로 하위그룹의 속성들을 설정해준다. 그러므로 하위 그룹을 한번의 설정으로 하위그룹까지 간편하게 관리 할 수 있다. 예를 들어 ThreadGroup의 setMaxPriority()를 통해서 최대 우선 순위 값을 설정하면 쓰레드 그룹의 하위 그룹의 maxPriority 값은 자동으로 수정된다. 

```java
public
class ThreadGroup implements Thread.UncaughtExceptionHandler {
...

    ThreadGroup groups[]; <-- 하위 쓰레드 그룹의 참조값을 가지고 있다.

...

public final void setMaxPriority(int pri) {
    int ngroupsSnapshot;
    ThreadGroup[] groupsSnapshot;
    synchronized (this) {
        checkAccess();
        if (pri < Thread.MIN_PRIORITY || pri > Thread.MAX_PRIORITY) {
            return;
        }
        maxPriority = (parent != null) ? Math.min(pri, parent.maxPriority) : pri;
        ngroupsSnapshot = ngroups;
        if (groups != null) {
            groupsSnapshot = Arrays.copyOf(groups, ngroupsSnapshot);
        } else {
            groupsSnapshot = null;
        }
    }
    for (int i = 0 ; i < ngroupsSnapshot ; i++) {
        groupsSnapshot[i].setMaxPriority(pri); <-- 하위 쓰레드 그룹의 maxPriority값을 수정한다.
    }
}

...
}

```

### 5. 데몬 쓰레드 (daemon thread)

5.1 데몬쓰레드란 

일반 Thread를 도와주는 보조적인 역할을 하는 Thread로서 일반 쓰레드와 다르게 프로세스의 모든 쓰레드가 종료되면 자신도 같이 종료된다. 그러므로 데몬쓰레드는 무한 루프에서 대기하고 있다가 특정 조건에 부합했을때 작업을 실행하도록 구현 할 수 있다. 대표적인 데몬 쓰레드로는 gc가 작동하는 Finalizer 쓰레드가 있다. 해당 쓰레드는 jvm에 자바 프로그램을 실행 할 때 생성한다. 



```java
public class ThreadEx{
    public static void main(String[] args) {
        Map map = Thread.getAllStackTraces();

        Iterator iterator = map.keySet().iterator();

        int x = 0;

        while(iterator.hasNext()){
            Object next = iterator.next();
            Thread thread = (Thread) next;

            System.out.println("["+ x + "] name : " + thread.getName()
                            + ", group : " + thread.getThreadGroup().getName()
                            + ", daemon : " + thread.isDaemon());

            StackTraceElement[] stackTraceElements = (StackTraceElement[]) map.get(next);

            for(StackTraceElement stackTraceElement : stackTraceElements){
                System.out.println("    "+stackTraceElement);
            }
        }
    }
}


---실행 결과---
[0] name : main, group : main, daemon : false 
    java.lang.Thread.dumpThreads(Native Method)
    java.lang.Thread.getAllStackTraces(Thread.java:1610)
    ThreadEx.main(ThreadEx.java:18)
[0] name : Signal Dispatcher, group : system, daemon : true <-- jvm에 의해 생성 된 데몬쓰레드
[0] name : Reference Handler, group : system, daemon : true <-- jvm에 의해 생성 된 데몬쓰레드
    java.lang.Object.wait(Native Method)
    java.lang.Object.wait(Object.java:502)
    java.lang.ref.Reference.tryHandlePending(Reference.java:191)
    java.lang.ref.Reference$ReferenceHandler.run(Reference.java:153)
[0] name : Monitor Ctrl-Break, group : main, daemon : true <-- jvm에 의해 생성 된 데몬쓰레드
    java.net.SocketInputStream.socketRead0(Native Method)
    java.net.SocketInputStream.socketRead(SocketInputStream.java:116)
    java.net.SocketInputStream.read(SocketInputStream.java:171)
    java.net.SocketInputStream.read(SocketInputStream.java:141)
    sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
    sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
    sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
    java.io.InputStreamReader.read(InputStreamReader.java:184)
    java.io.BufferedReader.fill(BufferedReader.java:161)
    java.io.BufferedReader.readLine(BufferedReader.java:324)
    java.io.BufferedReader.readLine(BufferedReader.java:389)
    com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:49)
[0] name : Finalizer, group : system, daemon : true <-- jvm에 의해 생성 된 데몬쓰레드
    java.lang.Object.wait(Native Method)
    java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:144)
    java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:165)
    java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:216)

Process finished with exit code 0

```

개발자가 데몬 쓰레드를 만들고 싶은 경우는 Thread 클래스의 setDaemon(boolean on) 메소드를 호출하여 true값을 넘겨주면 된다. setDaemon()은 start() 전에 호출 되어야 하므로 start()가 호출된 후 호출되면 IllegalThreadStateException을 발생시킨다.

<!-- 대몬쓰레드의 예시를 하나 만들어보면 좋겠다 -->
```java
public final void setDaemon(boolean on) {
    checkAccess();
    if (isAlive()) {
        throw new IllegalThreadStateException(); <-- IllegalThreadStateException을 발생
    }
    daemon = on;
}
```


### 6. thread 생명주기

6.1 쓰레드 상태

thread는 NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED와 같은 상태 값을 가지며 그 상태에 따른 생명주기를 가지고 있다. 

1) NEW : 쓰레드가 생성되고 아직 start()가 호출되지 않은 상태
2) RUNNABLE : 쓰레드가 실행 중이거나 실행 가능한 상태
3) BLOCKED : 동기화에 의해서 LOCK이 풀릴 때까지 기다리고 있는 상태
4) WATING, TIMED_WAITING : 쓰레드가 종료되지는 않았지만 실행가능하지 않은 일시정시 상태, TIMED_WAITING은 일정 시간 동안 일시정지인 상태이다.
5) TERMINATED : 쓰레드가 종료 된 상태

이러한 상태는 Thread 클래스의 enum State를 통해 알 수 있다.

```java

public
class Thread implements Runnable {
...

    public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
...
}

```

![thread 생명주기](/public/images/java-thread-01.png)

6.2 쓰레드 생명주기 순서

1) 쓰레드가 생성되고 나면 기본적으로 NEW 상태로 생성된다.

2) NEW 상태인 쓰레드의 start()가 호출되면 쓰레드는 RUNNABLE 상태로 변경되고 큐(Queue)에서 스케줄러에게 실행시간을 할당받기를 기다린다.

3) 큐에서 자신의 차례를 기다리는 쓰레드는 자신의 차례가 되면 실행시간을 할당받아서 작업을 처리한다. 실행 중인 상태도 RUNNABLE 상태이다.

4) 실행 중에 yield()를 만나게 되면 큐의 가장 뒷자리로 이동하여 다시 실행대기상태가 된다. 그리고 할당받은 실행시간은 다음쓰레드가 사용하게 된다.

5) 또 실행 중에 wait(), suspend(), sleep()를 만나게 되면 쓰레드는 WAITING, TIMED_WAITING 상태가 되고 일시정지 상태가 된다.

6) 일시정지 상태의 쓰레드가 resume(), notify(), interrupt()와 같은 메소드를 만나면 다시 RUNNABLE 상태로 돌아가 실행대기상태가 된다.

7) 위와 같은 cycle을 반복하다. 작업을 모두 처리하거나 stop()을 만나면 상태는 TERMINATED로 변경되고 쓰레드는 소멸된다. 

6.3 sleep(long millis) 

sleep()을 호출한 쓰레드는 WAITING 상태로 일시정지된다. 참고로 sleep이 호출된 쓰레드가 아닌 sleep()가 올라간 호출 스택의 쓰레드가 일시 정지 된다. 그래서 sleep 메소드는 static으로 지원된다. 일시정지 상태에서 지정된 시간이 지나거나 해당 쓰레드의 interrupt()가 호출되면 sleep()메소드는  InterruptedException를 발생시키고 일시정지 상태에서 깨어나 실행되기 상태가 된다.

```java
public static native void sleep(long millis) throws InterruptedException; // native method라서 실제 method body를 확인 할 수 없다.

public static void sleep(long millis, int nanos)
```

6.4 interrupt()와 interrupted() 

보통 Runnable의 run()을 구현 할 때, 
```java
    public void run(){
        while(!interrupted()){

        }
    }
```
와 같은 구조로 구현된다. interrupted상태(interrupted상태를 나타내는 인스턴스 변수는 Thread 클래스의 필드로 존재하지는 않는다.) 변수를 통해서 run()구현체를 컨트롤 한다. 

interrupted상태를 컨트롤 하기 위해 세가지 메소드가 존재한다.

1) public void interrupt()

2) public static boolean interrupted()

3) public boolean isInterrupted()

interrupt()는 interrupted상태를 false에서 true로 변경한다. 그러면 run() 메소드 안에서 무한루프를 탈출함으로써 쓰레드를 종료시킨다. 만약 쓰레드가 RUNNABLE상태가 아니라 sleep(), wait(), join()에 의한 WAITING 혹은 TIMED_WAITING 상태라면 해당 메소드에서 InterruptedException을 발생시키고 RUNNABLE상태로 바뀐다. 마찬가지로 interrupted상태를 false에서 true로 변경되니 무한루프를 탈출하고 쓰레드가 종료된다.

interrupted()는 while문의 조건으로 사용된다. interrupted상태 값을 반환한다. 상태 값이 false면 무한루프를 유지시킨다. 그리고 true이면 무한루프를 탈출 하고  interrupted상태 값 false로 초기화 한다. interrupted상태 값이 초기화 되었으므로 새로운 쓰레드를 통해서 다시 실행 할 수 있을 것이다.

isInterrupted()는 interrupted()와 같지만 interrupted상태 값의 초기화가 이루어지지 않는다.

6.5 yield()

무한루프를 통해서 run()을 구현하다보면 실제로 작업이 이루어지지 않고 의미 없는 루프를 도는 경우가 발생한다. 이러한 상황을 바쁜 대기상태(busy-waiting)라고 한다. 이러한 상황의 쓰레드가 존재한다면 Thread.yield()를 통해서 해당 쓰레드가 할당받은 시간을 대기하고 있는 쓰레드에게 양보하고 자신은 실행대기상태로 돌아갈 수 있다. 

예를 들면
```java
while(!interrupted()){
    if(flag){

    }
}
```
위와 같이 flag를 통해서 루프를 컨트롤 할 수 있는 상황에서 flag값이 false인 경우 바쁜 대기상태(busy-waiting)가 되어 쓸데없이 자원을 사용하게 된다. 

이런 경우

```java
while(!interrupted()){
    if(flag){

    }else{
        Thread.yield();
    }
}
```
Thread.yield()를 호출하여 해당 쓰레드가 할당받은 시간을 다른쓰레드에게 양보하면 더욱 효율적으로 cpu 자원을 활용 할 수 있을 것이다.


6.6 join()

join()은 어떤 쓰레드가 특정 쓰레드의 작업을 기다릴 필요가 있을때 사용된다. 파마미터를 통해 시간을 정해주면 해당 파라미터 시간 만큼 특정 쓰레드가 실행되도록 기다려준다. 

예를 들면
```java
    public static void main(String[] args) {

        Thread th1 = new Thread();
        th1.join();
    }
```
메인 메소들에서 특정 쓰레드를 생성하여 join()를 호출하면 메인 쓰레드의 상태는 일시정지상태로 변경되고 th1 쓰레드의 작업이 완료될때 다시 RUNNABLE상태로 돌아온다. 즉 th1의 작업이 완료되어야 다시 메인 메소드 작업이 시작되는 것이다.

### 7. 쓰레드의 동기화

7.1 쓰레드의 동기화란?

멀티 쓰레드는 여러 쓰레드가 같은 자원을 공유 할 수 있다. 멀티 쓰레드가 무작위로 같은 자원을 공유하다보면 예상과는 다른 결과가 나올 수 있다. 그러므로 여러 쓰레드는 필요에 따라 동기화(synchronization) 되어야 한다. 동기화(synchronization)란 한 쓰레드가 작업진행 중인 객체의 특정지역에 다른 쓰레드가 접근하지 못하도록 막는 것이다. 여기서 객체의 특정 지역을 임계 영역(critical section)이라 하고 다른 쓰레드의 접근을 막는 것은 잠금(lock)을 통해서 이루어진다. 즉 임계영역에 접근하려고 하는 쓰레드는 lock을 획득해야된다. 그리고 lock은 자동으로 획득하기 때문에 개발시 신경 쓸 필요는 없다. 참고로 쓰레드가 lock을 통해 접근하는 것은 코드영역이므로 클래스의 필드가 private가 아닌 public이라면 객체를 통한 인스턴스 변수에 동기화를 할 수 없다.  

7.2 임계 영역을 설정하는 방법

synchronized 키워드를 통해서 임계 영역을 설정 할 수 있는데 방법은 크게 두가지이다. 참고로 임계영역은 코드영역에 설정하는 것이므로 클래스의 필드가 private이 아닌 public인 경우 다른 쓰레드에 의해서 임의로 접근되서 수정이 발생 할 수 있다.

1) 메서드 전체를 임계 영역으로 설정하는 방법
```java
public synchronized void exam(){

}
```
1번 예제와 같이 메소드 전체를 임계 영역으로 지정하면 exam()를 호출하는 쓰레드는 해당 객체에 대한 lock을 획득해야되기 때문에 두개의 쓰레드가 동시에 exam()메소드를 호출 할 수 없다.


2) 특정 영역을 임계 영역으로 지정
```java
synchronized(객체의 참조변수){

}
```
2번 예제와 같이 메소드의 특정 영역을 임계 영역으로 지정하고 객체 참조변수를 파라미터로 입력하면 해당 객체에 대한 lock을 얻어 임계영역을 실행하게 된다. 메소드 전체을 임계영역으로 하면 비용이 많이 발생하므로 꼭 필요한 영역만 지정하여 사용하는 것이 추천된다.


7.3 wait()와 notify()

어떤 객체의 lock을 하나의 쓰레드가 계속 가지고 있다면 다른 쓰레드가 해당 객체에 접근을 못해 데드락 상태가 발생 할 수 있다. 객체에 대한 lock을 관리하기 위해 wait()와 notify() 메소드를 지원한다. wait()와 notify()는 Object 클래스의 메소드이므로 모든 객체에서 호출이 가능하다.  

1) wait(): 쓰레드가 작업도중 wait()를 만나게 되면 쓰레드는 lock을 받납하고 waiting pool에서 대기한다. 모든 객체는 waiting pool를 가지고 있고 쓰레드는 해당 객체의 waiting pool에서 기다리게 된다.

2) notify(): 객체의 waiting pool에서 임의의 쓰레드에게 실행통지를 보낸다. 임의의 쓰레드는 lock이 반납되길 기다리고 있다가. lock을 획득한 후, 다시 작업을 실행한다. 임의의 쓰레드에게 실행통지를 보낸다는 것인 dead lock을 풀 수 있는 적절한 쓰레드가 아닌 쓰레드에게 보낼 확률도 있다는 것이다. 이러한 현상을 기아현상(starvation)이라고 한다. 

3) notifyAll(): 기본적으로 notify()와 같지만 임의의 쓰레드에게 실행통지를 보내는 것이 아니라, 모든 쓰레드에게 통지를 보낸다. 해당 객체의 waiting pool에 있는 모든 쓰레드는 lock을 획득하게 하기 위해 경쟁한다. 이 경쟁 상태를 race condition이라고 한다.


7.4 volatile 

<!-- volatile는 컴퓨팅(cs)에 대한 이해가 없어서 잘 이해가 안됨 나중에 복습하기 p787-->

멀티 코어 프로세스에서는 여러 cpu의 cash에 memory의 data를 저장하여 사용하기 때문에 memory에 저장 된 instance 변수가 동기화 되지 않는 상황이 발생 할 수 있다. 이러한 상황을 대비해 개발자는 volatile를 사용 할 수 있다. 변수 앞에 volatile을 붙이면 cpu가 변수값을 읽어 올 때, cash가 아닌 메모리에서 data를 읽어온다.

추가적으로 synchronized 블록에서 접근하는 변수는 volatile를 사용하지 않아도 된다. 왜냐하면 synchronized 블록을 들어갈 때와 나올 때 캐쉬 메모리 간의 동기화가 이루어지기 때문이다. 

![thread 생명주기](/public/images/java-thread-02.png)



<!-- Lock, Condition 그리고 fork & join에 대해서 알 필요성이 생길 때 정리하기 -->