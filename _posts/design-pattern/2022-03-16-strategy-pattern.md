---
layout: post
title: "전략패턴(strategy pattern)"
date: 2022-03-16
categories: DesignPattern
---
# 전략패턴의 정의

전략패턴(strategy pattern)은 선언 된 알고리즘을 인터페이스로 캡슐화하고 클래스로 구현하여 클라이언트 측에 느슨한 결합으로 제공하는 디자인 패턴이다.

# 전략패턴의 구조

![strategy-pattern-01](/public/images/strategy-pattern-01.png)

위의 그림은 전략패턴의 구조를 uml로 나타낸 것이다. 전략패턴에서는 알고리즘을 캡슐화하는 인터페이스가 존재한다. 위의 그림의 Strategy 인터페이스가 이에 해당한다. 그리고 알고리즘은 구상클래스로 구현된다. 위의 그림에서는 ConcreateStrategy1, ConcreateStrategy2, ConcreateStrategy3이 이에 해당한다. 런타임 상황에서 클라이언트측 코드(Context클래스)에서는 Strategy 인터페이스를 통해서 각 구현체들을 전략적으로 교체하고 호출 할 수 있다.

# 전략패턴의 구현

자바로 전략패턴을 구현해본다. 상황은 필자가 좋아하는 농구선수 케빈 듀란트를 가정해서 한다. 코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/designpatterns/strategy01)에 저장되어있다. 케빈 듀란트는 세가지 공격전략을 가지고 있다. 삼점숫, 레이업, 미들슛이다. 이에 대해 매개체인 Attack이라는 인터페이스에 doAttack()이라는 메소드를 선언하고 클래스를 implements 함으로써 세가지 공격전략에 대한 구현체를 각각 만든다.

```
public interface Attack {

    void doAttack();
}

public class ThreePointShot implements Attack{
    @Override
    public void doAttack() {
        System.out.println("**try a three-point shot**");
    }
}

public class Layup implements Attack{
    @Override
    public void doAttack() {
        System.out.println("**try a layup shot**");
    }
}

public class MiddleShot implements Attack{
    @Override
    public void doAttack() {
        System.out.println("**try a middle shot**");
    }
}
```

다음으로 케빈 듀란트를 나타낼 BasketballPlayer클래스를 구현한다. 해당 클래스는 Attack을 구성으로 가지고 있어야한다. 그리고 동적으로 공격전략을 교체 할 수 있도록 setAttack()를 정의해야한다.

```
public class BasketballPlayer {
    public Attack attack;

    public void doAttack(){
        attack.doAttack();
    }

    public void setAttack(Attack attack) {
        this.attack = attack;
    }
}
```

마지막으로 main메소드에서 상황을 가정하여 실행해본다.

```

public class Main {

    public static void main(String[] args) {
        BasketballPlayer kevinDurant = new BasketballPlayer();
        System.out.println("농구 경기 시작");
        System.out.println("듀란트 공격 턴");
        System.out.println("수비가 골대에 모인 상황");
        kevinDurant.setAttack(new ThreePointShot());
        kevinDurant.doAttack();
        System.out.println("듀란트 공격 완료");

        System.out.println("듀란트 공격 턴");
        System.out.println("수비가 듀란트에게 가까이 붙은 상황");
        kevinDurant.setAttack(new Layup());
        kevinDurant.doAttack();
        System.out.println("듀란트 공격 완료");


    }
}
```

위의 실행 과정을 보면 케빈듀란트는 상황에 따라 동적으로 적절한 공격전략을 선택하여 실행하는 것을 알 수 있다.

# 전략패턴의 장점

## 서버코드에서의 장점

전략패턴에서는 서버코드에서는 용이한 확장성이 보장된다. 알고리즘에 대해서 추가적인 요구사항이 발생한 경우 기존의 구현클래스들은 수정하지 않은체 새로운 구현클래스를 만들어 대응 할 수 있다. 즉 ConcreateStrategy1, ConcreateStrategy2, ConcreateStrategy3은 그대로 둔 채 ConcreateStrategy4를 구현하여 대응 할 수 있다. 그리고 서버코드에서는 구현클래스를 클라이언트 측에 제공할 방법만 제시하면 끝이다.(구현클래스를 제공하는 방법에는 팩토리 메소드 패턴 등이 있을 수 있지만 전략패턴과 관련 없는 내용이므로 생략한다.)

## 클라이언트(사용자)코드에서의 장점

클라이언트(사용자)코드에서의 장점은 Context와 Stategy가 느슨하게 결합되어 개발자는 구현메소드(ConcreateStrategy클래스의 strategyMethod()를 의미한다.)의 내용을 알 필요 없이 각 ConcreateStrategy클래스가 어떤 기능을 하는지만 숙지한채 상황에 따라 구현클래스를 교체하여 사용 할 수 있다.

# 전략패턴의 사용예시

실제 전략패턴이 사용되는 라이브러리와 프레임워크를 찾아본다.

## 자바 라이브러리에서 제공하는 Comparator Interface

자바의 comparator 인터페이스의 구현를 통하여 정렬에 대한 알고리즘을 전략적으로 선택하여 사용 할 수 있다.

Comparator 인터페이스를 implements하여 정렬 알고리즘을 정의한다.

```
@FunctionalInterface
public interface Comparator<T> {
     int compare(T o1, T o2);
     ...
}
```

구현클래스를 통해서 알고리즘을 정의한 후 array의 정렬인 경우에는 Arrays.sort(array, Comparator)의 호출을 통해서 컬렉션의 정렬인 경우에는 Collections.sort(list, Comparator)를 통하여 정렬 알고리즘을 전략적으로 주입 할 수 있다. 주입 된 알고리즘은 결국 Arrays의 mergeSort()메소드 호출를 통하여 실행된다.

```
public class Arrays {
...
    private static void mergeSort(Object[] src,
                                  Object[] dest,
                                  int low, int high, int off,
                                  Comparator c) {
        int length = high - low;

        // Insertion sort on smallest arrays
        if (length < INSERTIONSORT_THRESHOLD) {
            for (int i=low; i<high; i++)
                for (int j=i; j>low && c.compare(dest[j-1], dest[j])>0; j--)
                    swap(dest, j, j-1);
            return;
        }

        // Recursively sort halves of dest into src
        int destLow  = low;
        int destHigh = high;
        low  += off;
        high += off;
        int mid = (low + high) >>> 1;
        mergeSort(dest, src, low, mid, -off, c);
        mergeSort(dest, src, mid, high, -off, c);

        // If list is already sorted, just copy from src to dest.  This is an
        // optimization that results in faster sorts for nearly ordered lists.
        if (c.compare(src[mid-1], src[mid]) <= 0) {
           System.arraycopy(src, low, dest, destLow, length);
           return;
        }

        // Merge sorted halves (now in src) into dest
        for(int i = destLow, p = low, q = mid; i < destHigh; i++) {
            if (q >= high || p < mid && c.compare(src[p], src[q]) <= 0)
                dest[i] = src[p++];
            else
                dest[i] = src[q++];
        }
    }
...
}
```

해당 메소드를 살펴보면 c.compare()매소들 호출을 통하여 알고리즘을 실행했다는 것을 알 수 있다.
<!-- ## 4. 전략패턴 사례

자바에서는 객체의 대소비교를 위해서 interface Comparator<T> 를 제공한다. Comparator는 전략패턴의 Strategy 인터페이스라고 할 수 있다. 즉 Comparator의 구현클래스들은 Comparator로 캡슐화된 대소비교를 위한 알고리즘 집합체이다. 위의 예시 다른 점은 Comparator의 구현체는 클라이언트 측에서 직접 구현하여 주입해 줄 수 있다는 것이다. 아래는 Comparator 인터페이스의 사용예시이다.

```java
class Person{
    public Person(int no) {
        this.no = no;
    }

    int no;

    @Override
    public String toString() {
        return "Person{" +
                "no=" + no +
                '}';
    }
}

class AscendingPersonComparator implements Comparator<Person> {
    @Override
    public int compare(Person o1, Person o2) {
        if(o1.no > o2.no){
            return 1;
        }else if(o1.no < o2.no){
            return -1;
        }else {
            return 0;
        }
    }
}

class DescendingPersonComparator implements Comparator<Person>{
    @Override
    public int compare(Person o1, Person o2) {
        if(o1.no > o2.no){
            return -1;
        }else if(o1.no < o2.no){
            return 1;
        }else {
            return 0;
        }
    }
}

public class Main {

    public static void main(String[] args) {

        List<Person> list = new ArrayList<>();

        list.add(new Person(2));
        list.add(new Person(0));
        list.add(new Person(4));
        list.add(new Person(1));
        list.add(new Person(3));

        System.out.println("---현재상태---");
        list.stream().forEach(System.out::print);

        System.out.println();
        System.out.println("---오름차순으로 전략교체---");
        Collections.sort(list, new AscendingPersonComparator());

        list.stream().forEach(System.out::print);

        System.out.println();
        System.out.println("---내림차순으로 전략교체---");
        Collections.sort(list, new DescendingPersonComparator());

        list.stream().forEach(System.out::print);

    }


}

실행결과
---현재상태---
Person{no=2}Person{no=0}Person{no=4}Person{no=1}Person{no=3}
---오름차순으로 전략교체---
Person{no=0}Person{no=1}Person{no=2}Person{no=3}Person{no=4}
---내림차순으로 전략교체---
Person{no=4}Person{no=3}Person{no=2}Person{no=1}Person{no=0}
```

코드와 실행결과를 보면 두개의 전략 즉 오름차순과 내림차순에 대한 비교 메소드을 구현하여 런타임시에 교체하여 사용한 것을 알 수 있다. 아래의 코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/designpatterns/strategy02)에 저장되어 있다. -->



