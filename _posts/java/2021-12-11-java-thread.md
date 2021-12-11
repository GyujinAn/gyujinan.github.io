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

프로세스는 실행 중인 프로그램이다. 프로그램이 실행되면 os는 메모리를 할당하여 저장장치에 있는 데이터(binary code = 명령어 + 전역변수)를 메모리에 적재한다. 이렇게 메모리에 적재 된 프로그램을 프로세스라고 한다. 프로세스는 한개 이상의 쓰레드로 구성 되는데 각 쓰레드들은 호출스택(call stack)을 할당받게 된다. 그러면 스케줄러는 cpu 사용시간을 할당하여 작업을 처리하는데, 이때  프로세스의 쓰레드들이 할당 받은 시간 받아 자신의 호출스택에서 작업을 처리한다. 

<br>

1.2 멀티쓰레드와 싱글쓰레드

프로세스 안에 쓰레드가 여러개가 존재하면 멀티쓰레드 프로세스 그리고 하나의 쓰레드가 존재하면 싱글쓰레드 프로세스라고 한다. 이렇게 여러개의 쓰레드를 통해서 프로세스의 작업들을 처리하는 이유는 사용자의 응답성을 높이기 위한 cpu 자원에 대한 효율성을 얻기 위해서다. 만약 메신저를 사용할때 파일다운로드 되는 시간에 메신저를 사용 할 수 없다면 cpu는 사용자의 응답에 최적화 되어 사용되지 않을 것이다. 하지만 이러한 멀티 쓰레드의 도입으로 인해 개발자 관점에서는 개발의 난이도가 높아지게 되었다. 예를 들면 각 쓰레드의 동기화와 각 쓰레드들이 같은 자원을 사용 함으로써 발생하는 데드락 같은 현상을 고려하여 개발을 진행해야되기 때문이다.


### 2. 쓰레드의 구현과 생성 그리고 실행

2.1 쓰레드의 구현

쓰레드는 두가지 방법으로 구현 할 수 있다. Thread 클래스를 상속하여 run() 메소드를 오버라이딩하는 것이다. 두번째 방법으로는 Runnable 인터페이스를 implement하여 run()메소드를 구현하는 것이다. 두 방법중 대부분의 자바 개발에서 재사용성(reusability)을 높이고 일관성(consistency)을 유지하기 위해 두번째 개발을 지향한다. 그리고 Runnable 인터페이스를 사용하는 방법은 전략(strategy)패턴으로 되어 있어 개발자가 작성하는 비즈니스 로직과 쓰레드의 로직을 분리되어 있기 때문에 더욱 객체지향적 개발이 될 수 있다. 

추가적으로 상속을 통한 방법은 Thread 클래스의 메소드를 상속 받았기 때문에 run()메소들에서 Thread클래스의 메소드에 접근 할 수 있지만 Runnable 인터페이스를 구현하는 방법에서는 Thread 메소드를 바로 접근 할 수가 없다. Thread의 메소드에 접근하기 위해서는 Thread.currentThread() 통해서 Thread에 대한 참조를 얻은 후에 접근 할 수 있다.

```
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

```
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

```
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

```
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
```
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

```
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

```

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

```
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



```
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
```
public final void setDaemon(boolean on) {
    checkAccess();
    if (isAlive()) {
        throw new IllegalThreadStateException(); <-- IllegalThreadStateException을 발생
    }
    daemon = on;
}
```