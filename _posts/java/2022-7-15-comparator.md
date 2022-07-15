---
layout: post
title: "comparator"
date: 2022-7-15
categories: java
---

# 사용 목적

wrapper 클래스 혹은 String, Date, File과 같이 자바에서 제공하는 특정형식의 데이터를 나타내는 객체들은 Comparable 인터페이스가 구현되어 있어 정렬이 가능하다. 개발자가 직접 정렬비교 알고리즘을 정의하여 정렬하고 싶을 때에는 Comparator를 구현하여 정렬비교 알고리즘을 전략적으로 주입하여 사용 할 수 있다. ([전략패턴](https://angyujin.tistory.com/4))

# 사용 방법

## 정렬비교 알고리즘 정의

정렬비교 알고리즘 정의는 Comparator 인터페이스를 implements하여 정의한다.

```
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

위와 같이 Comparator는 함수형 인터페이스로 compare를 구현하여 정렬비교 알고리즘을 정의한다. 메소드의 두 파라미터 o1, o2에 대한 정렬비교 알고리즘을 구현한다. 정렬비교의 결과로 int 타입의 수를 return한다. return의 의미는 java doc에 설명되어있는데 아래와 같다.

> Compares its two arguments for order. Returns a negative integer, zero, or a positive integer as the first argument is less than, equal to, or greater than the second.

o1이 o2보다 앞에 정렬되면 음수, 같으면 0, 뒤에 정렬되면 양수를 return한다.

## 정렬비교 알고리즘 주입

정렬비교 알고리즘 정렬 메소드에 파라미터로 주입하여 사용 할 수 있다. 정렬 메소드는 배열 정렬을 위한 메소드와 컬렉션 정렬을 위한 메소드 두가지가 존재한다.

배열정렬 메소드: Arrays클래스의 sort(array, Comparable 구현객체) 메소드  
컬렉션정렬 메소드: Collections클래스의 sort(Collection 객체, Comparable 구현객체) 메소드

# 사용 예시

아래의 코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/general/comparator)에 모두 저장되어 있다.

## 정렬 타겟 클래스

-   Person 클래스를 정의하였고 no이라는 int 타입의 값이 존재한다. no를 기준으로 정렬 할 것이다.

```
public class Person{
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
```

## 정렬비교 알고리즘 정의

-   오름차순 알고리즘를 정의한 클래스이다. Person 클래스의 no를 기준으로 비교한다.

```
public class AscendingPersonComparator implements Comparator<Person> {
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
```

-   내림차순 알고리즘를 정의한 클래스이다. Person 클래스의 no를 기준으로 비교한다.

```
public class DescendingPersonComparator implements Comparator<Person> {
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
```

## 정렬비교 알고리즘 사용

배열은 Arrays.sort(array, new AscendingPersonComparator()) 통해서 그리고 컬렉션은 Collections.sort(list, new AscendingPersonComparator()) 통해서 알고리즘을 주입하여 정렬한 것을 확인 할 수 있다.

```
public class Main {

    public static void main(String[] args) {
        examArraySort();
        examCollectionSort();
    }

    static private void examArraySort(){
        System.out.println("---------examArraySort() is started---------");

        Person [] array = new Person[5];
        array[0] = new Person(2);
        array[1] = new Person(0);
        array[2] = new Person(4);
        array[3] = new Person(1);
        array[4] = new Person(3);

        System.out.println("---현재상태---");
        printArr(array);

        System.out.println();
        System.out.println("---오름차순으로 전략교체---");
        Arrays.sort(array, new AscendingPersonComparator());
        printArr(array);

        System.out.println();
        System.out.println("---내림차순으로 전략교체---");
        Arrays.sort(array, new DescendingPersonComparator());
        printArr(array);

    }

    static private void printArr(Person [] array){
        for(Person p : array){
            System.out.println(p.toString());
        }
    }

    static private void examCollectionSort(){
        System.out.println("---------examCollectionSort() is started---------");

        List<Person> list = new ArrayList<>();

        list.add(new Person(2));
        list.add(new Person(0));
        list.add(new Person(4));
        list.add(new Person(1));
        list.add(new Person(3));

        System.out.println("---현재상태---");
        list.stream().forEach(System.out::println);

        System.out.println();
        System.out.println("---오름차순으로 전략교체---");
        Collections.sort(list, new AscendingPersonComparator());

        list.stream().forEach(System.out::println);

        System.out.println();
        System.out.println("---내림차순으로 전략교체---");
        Collections.sort(list, new DescendingPersonComparator());

        list.stream().forEach(System.out::println);
    }
}
```