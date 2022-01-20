---
layout: post
title: "버블정렬이란?"
date: 2022-01-01
categories: algorithm
---

# 버블정렬이란?

정렬은 데이터에 대한 검색을 효율적으로 하기 위해서 교환, 선택, 삽입을 사용하여 오름차순(ascending order) 혹은 내림차순(descending order)으로 데이터를 나열하는 것이다. 검색에 대한 효율성이 향상되기 때문에 알고리즘 시간복잡도에 큰 영향을 줄 수 있다. 정렬 알고리즘은 여러가지가 있는데 이번 포스팅에서는 오름차순의 버블 정렬에 대해 포스팅한다.

## Pass

버블정렬은 pass의 모임이므로 pass에 대하여 먼저 알아본다. pass란 배열 a의 크기가 n이라고 하고 배열 첫번째 인덱스를 j이라고 할때, a의 가장 뒤에 두수 a[n-1], a[n-2]부터 가장 앞의 두수 a[j], a[j+1]까지 차례대로 선택 및 교환하여 가장 작은 수를 a[j]로 옮기는 행위이다. 물론 가장 큰수 a[j]로 옮길 수도 있고 행위를 반대로 하여 가장 앞에서 부터 비교 할 수도 있다. 가장 작은 수를 가장 앞으로 옮기는 예시와 가장 큰수를 가장 뒤로 옮기는 코드를 만들어 본다.


```
class Main {

    //가장 작은 수를 앞으로 옮기는 pass
    private static int[] pass0(int[] a, int n){
        for(int i=n-1; i>0; i--){
            if(a[i] <  a[i-1]){
                int tmp = a[i];
                a[i] = a[i-1];
                a[i-1] = tmp;
            }
        }
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass
    private static int[] pass1(int[] a, int n){
        for(int i=0; i<n-1; i++){
            if(a[i] > a [i+1]){
                int tmp = a[i];
                a[i] = a[i+1];
                a[i+1] = tmp;
            }
        }
        return a;
    }

    private static void printA(int[] a){
        System.out.println("=== printA ===");
        for(int tmp : a){
            System.out.print(tmp+" ");
        }
        System.out.println();
    }


    public static void main(String[] args) {

        int[] a0 = {4,3,5,1,2};
        int n0 = a0.length;
        printA(pass0(a0, n0));

        int[] a1 = {4,3,5,1,2};
        int n1 = a1.length;
        printA(pass1(a1, n1));

    }

}

-----출력 결과-----

=== printA ===
1 4 3 5 2 
=== printA ===
3 4 1 2 5 

```

출력 결과를 살펴보면 첫번째 pass는 가장 작은 수 1이 가장 앞으로 두번째 pass는 가장 큰수 5가 가장 뒤로 이동한 것을 확인 할 수 있다.

## 버블 정렬

전 pass의 최소수 혹은 최대수를 제외하고 pass를 n-1번 반복하면 배열은 최종적으로 정렬이 된다. 버블 정렬을 완성하여 본다.
```
class Main {

    //가장 작은 수를 앞으로 옮기는 pass로 구성 된 버블 정렬
    private static int[] bubbleSort0(int[] a, int n){
        for(int j=0; j<n-1; j++){
            for(int i=n-1; i>j; i--){
                if(a[i] <  a[i-1]){
                    int tmp = a[i];
                    a[i] = a[i-1];
                    a[i-1] = tmp;
                }
            }
        }
        return a;
    }

    //가장 큰 수를 뒤로 옮기는 pass로 구성 된 버블 정렬
    private static int[] bubbleSort1(int[] a, int n){
        for(int j=n-1; j>0; j--){
            for(int i=0; i<j; i++){
                if(a[i] > a[i+1]){
                    int tmp = a[i];
                    a[i] = a[i+1];
                    a[i+1] = tmp;
                }
            }
        }
        return a;
    }

    private static void printA(int[] a){
        System.out.println("=== printA ===");
        for(int tmp : a){
            System.out.print(tmp+" ");
        }
        System.out.println();
    }


    public static void main(String[] args) {

        int[] a0 = {4,3,5,1,2};
        int n0 = a0.length;
        printA(bubbleSort0(a0, n0));

        int[] a1 = {4,3,5,1,2};
        int n1 = a1.length;
        printA(bubbleSort1(a1, n1));

    }

}

-----출력 결과-----

=== printA ===
1 2 3 4 5 
=== printA ===
1 2 3 4 5 
```

출력 결과를 살펴보면 배열 a0, a1모두 정렬 된 것을 확인 할 수 있다.

## 시간복잡도

버블정렬의 시간 복잡도를 계산해본다.

* pass의 총 횟수는 n-1번 발생한다.

* pass안에서의 선택 및 교환 연산은 최초에는 n-1번 발생하고 그 후에는 1씩 감소하면서 발생한다.

이를 식으로 나타내어 본다.
```
버블정렬의 시간복잡도 T(n)에 대해서

T(n) = (n-1) + (n-2) + ... + 2 + 1

가우스의 덧셈 공식을 적용

T(n) = 0.5 *(n^2 - n)

빅오표기법으로는 O(n^2)이다

```
